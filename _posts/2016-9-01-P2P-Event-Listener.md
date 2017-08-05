---
layout: post
title: Simple Event Engine between Two Devices for IoT
tags:
- blog
---

A little while ago, I was tasked with sending commands from one mobile device to another in order to facilitate command transfer.  I came up with a solution involving pairing the two devices to use the second device as a proxy for the first device.

This pairing process was done through an Event Listener, which is a term I just made up, using a back-end system.  In my case, Google Firebase just came out and enabled me to quickly develop this system in a couple weeks.

*I'm skipping over the setup of Google Firebase as well as the initial references since you can easily read the documentation on the Firebase Database* **[here](https://firebase.google.com/docs/database/)**.

Device A (the proxy) creates an entity on Firebase with a unique ID, and sits waiting for someone to make a request:

    // Device A: Android device (Java)

    // gets a key to identify me
    String key = database.getReference("A").push().getKey();
    Map<String, Object> postValues = new HashMap<>();

    // save my unique key for later access
    savedKey = key;

    String name = inputName.getText().toString();

    // variables to set up the update
    postValues.put("name", name);
    Map<String, Object> childUpdates = new HashMap<>();
    childUpdates.put("/A/" + key, postValues);

    // commit the update to the database
    database.getReference().updateChildren(childUpdates);


Device B can then make a request by adding a child to this table:

    // Device B: an iOS device (Swift)
    let selectedKey = listenerArray[selectedIndex].key

    // get the reference for the location to put requests
    let ref = FIRDatabase.database().reference().child("A").child(selectedKey).child("B")
    let key = ref.childByAutoId().key

    // set variables and commit update
    let jsonObject = ["name": self.textName.text] as [String : AnyObject]
    let childUpdates = ["\(key)": jsonObject as AnyObject] as [String : AnyObject]
    ref.updateChildValues(childUpdates)


After a request is made by updating this entry, Device A can do some local verification, either via manual user validation or automated via an algorithm, to decide whether or not to accept this request.  If not, the request can simply be deleted.  If it is accepted, then another update can be made to exchange a randomized secret key.  This code creates a listener in Android to respond to updates to this table:

    // Device A: Android device (Java)

    // add a listener to the database of B, using the savedKey from earlier
    database.getReference("A/" + savedKey + "/B").addChildEventListener(requestListener);

    ChildEventListener requestListener = new ChildEventListener() {
        @Override
        public void onChildAdded(DataSnapshot dataSnapshot, String previousChildName) {

            HashMap<String, Object> passengerMap = (HashMap<String, Object>)dataSnapshot.getValue();
            final String name = (String)passengerMap.get("name");

            // create a pop-up Alert dialog for the user of device A to verify the request manually

            new AlertDialog.Builder(MainActivity.this)
                    .setTitle("Request found!"")
                    .setMessage(name + " requests.  Accept?")
                    .setPositiveButton(android.R.string.yes, new DialogInterface.OnClickListener() {
                        public void onClick(DialogInterface dialog, int which) {
                            // secret to pair driver client and end user
                            String uuid = UUID.randomUUID().toString();
                            secret = uuid;
                            database.getReference("A/" + savedKey).child("secret").setValue(uuid);
                            database.getReference("A/" + savedKey + "/ready").addValueEventListener(readyListener);
                        }
                    })
                    .setNegativeButton(android.R.string.no, new DialogInterface.OnClickListener() {
                        public void onClick(DialogInterface dialog, int which) {
                            // do nothing
                        }
                    })
                    .setIcon(android.R.drawable.ic_dialog_alert)
                    .show();
        }

Device B can store this locally using a similar listener for the /secret location and delete the key from Firebase:

    // Device B: an iOS device (Swift)

    FIRDatabase.database().reference().child("A").child(savedKey).child("secret").observe(FIRDataEventType.value, with: { (snapshot) in
        if let secret = snapshot.value as? String{
            // signal that the pairing is complete, and A can stop listening
            FIRDatabase.database().reference().child("A").child(savedKey).child("ready").setValue(true)

            // remove secret listener
              FIRDatabase.database().reference().child("A").child(savedKey).child("secret").removeAllObservers()

              FIRDatabase.database().reference().child("A").child(savedKey).child("secret").removeValue()

              // do something
      }

    }) { (error) in

      print(error.localizedDescription)

    }

This enables Device B to communicate with Device A and **only** Device B.  No other device knows the secret key, which will be passed to Device A anytime a command is sent.

In turn, Device A can now stop accepting new requests and just ignore any other devices.

From here, Device B can send commands in the same manner, only sending them with the secret key:

    // Device B: an iOS device (Swift)
    let ref = FIRDatabase.database().reference().child("A").child(savedKey).child("commands")

    let key = ref.childByAutoId().key

    // parameters, must include the secret key
    let jsonObject = ["parameter": setParameter as AnyObject,
                      "secret": self.secret as AnyObject
        ] as [String : AnyObject]

    let childUpdates = ["\(key)": jsonObject as AnyObject] as [String : AnyObject]

    ref.updateChildValues(childUpdates)

And Device A can interpret them as it pleases, using another EventListener to wait for incoming commands:

    // Device A: Android Device (Java)
    database.getReference("A/" + savedKey + "/commands").addChildEventListener(commandListener);

and using logic to allow/disallow certain commands or parameters for said commands.  Only the ones that pass the permissions table will be passed along to the server or other receiving device.  This downstream passing can also likewise be gated by another verification step.

The great thing about Google Firebase is that this setup is quite easy to code and expand for any IoT setup.
