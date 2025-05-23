
<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>রিয়েলটাইম চ্যাট</title>
  <style>
    * { box-sizing: border-box; }
    body { margin: 0; font-family: Arial, sans-serif; background: #f0f0f0; }
    .chat-container { height: 100vh; display: flex; flex-direction: column; }
    .chat-header { background: #0084ff; color: white; padding: 10px; font-weight: bold; }
    .chat-messages { flex: 1; padding: 10px; overflow-y: auto; background: #fff; }
    .message { max-width: 70%; margin-bottom: 10px; padding: 10px; border-radius: 10px; position: relative; font-size: 14px; }
    .sent { align-self: flex-end; background: #0084ff; color: white; border-bottom-right-radius: 4px; }
    .received { align-self: flex-start; background: #e4e6eb; color: black; border-bottom-left-radius: 4px; }
    .time { font-size: 10px; opacity: 0.6; margin-top: 4px; text-align: right; }
    .chat-input { display: flex; padding: 10px; background: white; border-top: 1px solid #ccc; gap: 5px; }
    .chat-input input[type="text"] { flex: 1; padding: 10px; border-radius: 20px; border: 1px solid #ccc; }
    .chat-input input[type="file"] { width: 120px; }
    .chat-input button { padding: 10px 16px; border-radius: 20px; border: none; background: #0084ff; color: white; cursor: pointer; }
    #login { padding: 20px; text-align: center; }
    #login button { padding: 10px 20px; font-size: 16px; background: #db4437; color: white; border: none; border-radius: 6px; cursor: pointer; }
  </style>
</head>
<body>

<div id="login">
  <h2>গুগল দিয়ে লগইন করুন</h2>
  <button onclick="login()">Google Sign In</button>
</div>

<div class="chat-container" style="display:none;" id="chat-box">
  <div class="chat-header" id="user-name">চ্যাট</div>
  <div class="chat-messages" id="messages"></div>
  <div class="chat-input">
    <input type="text" id="message-input" placeholder="মেসেজ লিখুন..." />
    <input type="file" id="image-input" accept="image/*" />
    <button onclick="sendMessage()">Send</button>
  </div>
</div>

<!-- Firebase SDKs -->
<script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-auth-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-database-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-storage-compat.js"></script>

<script>
  const firebaseConfig = {
    apiKey: "AIzaSyDkfW0Yf-9oR64j5GAPuBW_1G-rqGK9cOY",
    authDomain: "kazol-35172.firebaseapp.com",
    databaseURL: "https://kazol-35172-default-rtdb.firebaseio.com",
    projectId: "kazol-35172",
    storageBucket: "kazol-35172.appspot.com",
    messagingSenderId: "862146314863",
    appId: "1:862146314863:web:6f09888dce4bbda6d6439a",
    measurementId: "G-MSH3KSKBTJ"
  };

  firebase.initializeApp(firebaseConfig);
  const auth = firebase.auth();
  const db = firebase.database();
  const storage = firebase.storage();
  let currentUser = null;

  function login() {
    const provider = new firebase.auth.GoogleAuthProvider();
    auth.signInWithPopup(provider).then(res => {
      currentUser = res.user;
      document.getElementById("login").style.display = "none";
      document.getElementById("chat-box").style.display = "flex";
      document.getElementById("user-name").textContent = currentUser.displayName;
      listenMessages();
    });
  }

  function getTime() {
    const now = new Date();
    return now.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
  }

  function sendMessage() {
    const input = document.getElementById("message-input");
    const fileInput = document.getElementById("image-input");
    const text = input.value.trim();
    const file = fileInput.files[0];

    if (!text && !file) return;

    if (file) {
      const fileRef = storage.ref("images/" + Date.now() + "_" + file.name);
      fileRef.put(file).then(snapshot => {
        snapshot.ref.getDownloadURL().then(url => {
          db.ref("messages").push({
            uid: currentUser.uid,
            name: currentUser.displayName,
            imageUrl: url,
            time: getTime()
          });
        });
      });
      fileInput.value = "";
    }

    if (text) {
      db.ref("messages").push({
        uid: currentUser.uid,
        name: currentUser.displayName,
        text,
        time: getTime()
      });
      input.value = "";
    }
  }

  function listenMessages() {
    const messages = document.getElementById("messages");
    db.ref("messages").on("child_added", snap => {
      const msg = snap.val();
      const div = document.createElement("div");
      div.classList.add("message");
      div.classList.add(msg.uid === currentUser.uid ? "sent" : "received");

      if (msg.imageUrl) {
        div.innerHTML = `<div><strong>${msg.name}</strong><br><img src="${msg.imageUrl}" style="max-width: 200px; border-radius: 10px;" /></div><div class="time">${msg.time}</div>`;
      } else {
        div.innerHTML = `<div><strong>${msg.name}</strong><br>${msg.text}</div><div class="time">${msg.time}</div>`;
      }

      messages.appendChild(div);
      messages.scrollTop = messages.scrollHeight;
    });
  }
</script>

</body>
</html>
