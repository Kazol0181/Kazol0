<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>কাজল কবি চ্যটিং বক্স</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f0f2f5;
      display: flex;
      justify-content: center;
      padding: 20px;
      margin: 0;
    }
    .chat-container {
      width: 100%;
      max-width: 400px;
      height: 90vh;
      background: white;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
      display: flex;
      flex-direction: column;
    }
    .messages {
      flex: 1;
      padding: 10px;
      overflow-y: auto;
    }
    .message {
      margin-bottom: 10px;
      padding: 8px 12px;
      background: #e4e6eb;
      border-radius: 15px;
      max-width: 70%;
      word-wrap: break-word;
    }
    .message.you {
      background: #0084ff;
      color: white;
      margin-left: auto;
    }
    .input-area {
      display: flex;
      border-top: 1px solid #ccc;
    }
    input {
      flex: 1;
      padding: 10px;
      border: none;
      border-radius: 0 0 0 10px;
      outline: none;
      font-size: 16px;
    }
    button {
      background: #0084ff;
      color: white;
      border: none;
      padding: 10px 15px;
      cursor: pointer;
      border-radius: 0 0 10px 0;
      font-size: 16px;
    }
  </style>
</head>
<body>

<div class="chat-container">
  <div class="messages" id="messages"></div>
  <div class="input-area">
    <input type="text" id="messageInput" placeholder="মেসেজ টাইপ করুন..." />
    <button id="sendButton">পাঠান</button>
  </div>
</div>

<!-- Firebase with Modular JavaScript -->
<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js";
  import { getDatabase, ref, push, onChildAdded } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-database.js";

  const firebaseConfig = {
    apiKey: "AIzaSyDkfW0Yf-9oR64j5GAPuBW_1G-rqGK9cOY",
    authDomain: "kazol-35172.firebaseapp.com",
    databaseURL: "https://kazol-35172-default-rtdb.firebaseio.com",
    projectId: "kazol-35172",
    storageBucket: "kazol-35172.appspot.com",
    messagingSenderId: "862146314863",
    appId: "1:862146314863:web:15d961531d90c23cd6439a",
    measurementId: "G-ECVXRGP67B"
  };

  const app = initializeApp(firebaseConfig);
  const db = getDatabase(app);
  const messagesRef = ref(db, 'messages');

  // পাঠান বাটনের ক্লিক ইভেন্ট
  document.getElementById("sendButton").addEventListener("click", () => {
    const input = document.getElementById("messageInput");
    const message = input.value.trim();
    if (message) {
      push(messagesRef, {
        text: message,
        timestamp: Date.now()
      });
      input.value = "";
    }
  });

  // নতুন মেসেজ রিয়েলটাইমে দেখাও
  onChildAdded(messagesRef, snapshot => {
    const data = snapshot.val();
    const messagesDiv = document.getElementById("messages");
    const msgDiv = document.createElement("div");
    msgDiv.classList.add("message", "you");
    msgDiv.innerText = data.text;
    messagesDiv.appendChild(msgDiv);
    messagesDiv.scrollTop = messagesDiv.scrollHeight;
  });
</script>

</body>
</html>
