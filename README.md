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
  </style>
</head>
<body>
<div class="chat-container">
  <div class="chat-header">
    <img src="https://i.ibb.co/6HYz3mF/profile.jpg" />
    <span>আয়াজ হাসান</span>
  </div>

  <div class="chat-messages" id="messages"></div>

  <div class="chat-input">
    <input type="text" id="message-input" placeholder="মেসেজ লিখুন..." />
    <button onclick="sendMessage()">Send</button>
  </div>
</div>

<!-- Firebase SDK -->
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

<script>
  // Firebase config
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

  // Initialize Firebase
  firebase.initializeApp(firebaseConfig);
  const db = firebase.database();

  const messages = document.getElementById("messages");
  const input = document.getElementById("message-input");

  function getCurrentTime() {
    const now = new Date();
    return now.toLocaleTimeString([], { hour: "2-digit", minute: "2-digit" });
  }

  function appendMessage(text, type, time) {
    const div = document.createElement("div");
    div.className = `message ${type}`;
    div.innerHTML = `<div>${text}</div><div class="time">${time}</div>`;
    messages.appendChild(div);
    messages.scrollTop = messages.scrollHeight;
  }

  function sendMessage() {
    const text = input.value.trim();
    if (!text) return;

    const time = getCurrentTime();
    db.ref("messages").push({
      text,
      type: "sent",
      time
    });

    input.value = "";
  }

  // Listen for messages
  db.ref("messages").on("child_added", (snapshot) => {
    const msg = snapshot.val();
    appendMessage(msg.text, msg.type, msg.time);
  });

  input.addEventListener("keydown", (e) => {
    if (e.key === "Enter") sendMessage();
  });
</script>
</body>
</html>
