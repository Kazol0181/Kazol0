<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>ফেক চ্যাট</title>
  <style>
    * { box-sizing: border-box; }
    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #f0f0f0;
    }
    .chat-container {
      height: 100vh;
      display: flex;
      flex-direction: column;
      background: white;
    }
    .chat-header {
      background: #0084ff;
      color: white;
      display: flex;
      align-items: center;
      padding: 10px;
      gap: 10px;
    }
    .chat-header img {
      width: 42px;
      height: 42px;
      border-radius: 50%;
    }
    .chat-header span {
      font-weight: bold;
      font-size: 16px;
    }
    .chat-messages {
      flex: 1;
      overflow-y: auto;
      padding: 10px;
      display: flex;
      flex-direction: column;
      gap: 6px;
    }
    .message {
      max-width: 70%;
      padding: 10px 14px;
      border-radius: 18px;
      font-size: 14px;
      line-height: 1.4;
      position: relative;
    }
    .sent {
      align-self: flex-end;
      background: #0084ff;
      color: white;
      border-bottom-right-radius: 4px;
    }
    .received {
      align-self: flex-start;
      background: #e4e6eb;
      color: black;
      border-bottom-left-radius: 4px;
    }
    .time {
      font-size: 10px;
      opacity: 0.6;
      margin-top: 4px;
      text-align: right;
    }
    .chat-input {
      display: flex;
      padding: 10px;
      background: white;
      border-top: 1px solid #ccc;
      gap: 10px;
    }
    .chat-input input {
      flex: 1;
      padding: 10px 14px;
      border-radius: 20px;
      border: 1px solid #ccc;
      font-size: 14px;
    }
    .chat-input button {
      padding: 10px 16px;
      border-radius: 20px;
      border: none;
      background: #0084ff;
      color: white;
      cursor: pointer;
      font-size: 14px;
    }
    .delete-btn {
      font-size: 10px;
      background: none;
      border: none;
      color: red;
      position: absolute;
      top: 4px;
      right: 10px;
      cursor: pointer;
    }
    .login-screen {
      height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .login-screen button {
      padding: 12px 20px;
      font-size: 16px;
      border: none;
      background: #db4437;
      color: white;
      border-radius: 6px;
      cursor: pointer;
    }
  </style>
</head>
<body>
<div id="app">
  <div class="login-screen" id="login-screen">
    <button onclick="signIn()">Google দিয়ে লগইন করুন</button>
  </div>

  <div class="chat-container" style="display:none" id="chat-container">
    <div class="chat-header">
      <img id="user-pic" src="" />
      <span id="user-name">...</span>
    </div>
    <div class="chat-messages" id="messages"></div>
    <div class="chat-input">
      <input type="text" id="message-input" placeholder="মেসেজ লিখুন..." />
      <button onclick="sendMessage()">Send</button>
    </div>
  </div>
</div>

<!-- Firebase SDK -->
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-auth.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

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
  const db = firebase.database();
  const auth = firebase.auth();

  let currentUser;

  auth.onAuthStateChanged(user => {
    if (user) {
      currentUser = user;
      document.getElementById("login-screen").style.display = "none";
      document.getElementById("chat-container").style.display = "flex";
      document.getElementById("user-pic").src = user.photoURL;
      document.getElementById("user-name").textContent = user.displayName;
      listenForMessages();
    }
  });

  function signIn() {
    const provider = new firebase.auth.GoogleAuthProvider();
    auth.signInWithPopup(provider).catch(alert);
  }

  function getCurrentTime() {
    const now = new Date();
    return now.toLocaleTimeString([], { hour: "2-digit", minute: "2-digit" });
  }

  function appendMessage(key, text, type, time, showDelete = false) {
    const div = document.createElement("div");
    div.className = `message ${type}`;
    div.innerHTML = `
      <div>${text}</div>
      <div class="time">${time}</div>
      ${showDelete ? `<button class="delete-btn" onclick="deleteMessage('${key}')">x</button>` : ""}
    `;
    document.getElementById("messages").appendChild(div);
    document.getElementById("messages").scrollTop = messages.scrollHeight;

    if (type === "received" && document.visibilityState !== "visible") {
      showNotification("আয়াজ হাসান", text);
    }
  }

  function sendMessage() {
    const input = document.getElementById("message-input");
    const text = input.value.trim();
    if (!text) return;
    const time = getCurrentTime();
    db.ref("messages").push({
      text,
      time,
      uid: currentUser.uid,
      name: currentUser.displayName
    });
    input.value = "";
  }

  function deleteMessage(key) {
    db.ref("messages/" + key).remove();
  }

  function listenForMessages() {
    db.ref("messages").on("child_added", (snapshot) => {
      const msg = snapshot.val();
      const type = msg.uid === currentUser.uid ? "sent" : "received";
      appendMessage(snapshot.key, msg.text, type, msg.time, msg.uid === currentUser.uid);
    });

    db.ref("messages").on("child_removed", (snapshot) => {
      document.querySelectorAll(".message").forEach(div => {
        if (div.innerHTML.includes(`deleteMessage('${snapshot.key}')`)) {
          div.remove();
        }
      });
    });
  }

  function showNotification(title, body) {
    if (Notification.permission === "granted") {
      new Notification(title, {
        body,
        icon: "https://i.ibb.co/6HYz3mF/profile.jpg"
      });
    } else {
      Notification.requestPermission();
    }
  }

  document.getElementById("message-input").addEventListener("keydown", (e) => {
    if (e.key === "Enter") sendMessage();
  });
</script>
</body>
</html>
