<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Facebook Style Chat</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f0f2f5;
      display: flex;
      justify-content: center;
      padding-top: 50px;
    }
    .chat-container {
      width: 400px;
      height: 600px;
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
    }
    button {
      background: #0084ff;
      color: white;
      border: none;
      padding: 10px 15px;
      cursor: pointer;
      border-radius: 0 0 10px 0;
    }
  </style>
</head>
<body>

<div class="chat-container">
  <div class="messages" id="messages"></div>
  <div class="input-area">
    <input type="text" id="messageInput" placeholder="Type a message..." />
    <button onclick="sendMessage()">Send</button>
  </div>
</div>

<!-- Firebase SDKs -->
<script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database.js"></script>

<script>
  // Firebase Config
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

  // Initialize Firebase
  const app = firebase.initializeApp(firebaseConfig);
  const db = firebase.database();

  const messagesRef = db.ref('messages');

  // Send message function
  function sendMessage() {
    const input = document.getElementById('messageInput');
    const message = input.value.trim();
    if (message) {
      messagesRef.push({
        text: message,
        timestamp: Date.now()
      });
      input.value = '';
    }
  }

  // Listen for new messages
  messagesRef.on('child_added', snapshot => {
    const data = snapshot.val();
    const messagesDiv = document.getElementById('messages');
    const msgDiv = document.createElement('div');
    msgDiv.classList.add('message');
    msgDiv.classList.add('you');
    msgDiv.innerText = data.text;
    messagesDiv.appendChild(msgDiv);
    messagesDiv.scrollTop = messagesDiv.scrollHeight;
  });
</script>

</body>
</html>
