
<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>নিহান চৌধুরী</title>
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', sans-serif;
      background-color: #e5ddd5;
    }
    .messenger {
      max-width: 400px;
      margin: auto;
      height: 100vh;
      display: flex;
      flex-direction: column;
      background-color: #fff;
      border: 1px solid #ccc;
    }
    .header {
      background-color: #0084ff;
      color: white;
      padding: 10px;
      font-size: 18px;
      font-weight: bold;
    }
    .chat-box {
      flex: 1;
      padding: 10px;
      overflow-y: auto;
      display: flex;
      flex-direction: column;
    }
    .message {
      max-width: 70%;
      margin: 5px 0;
      padding: 8px 12px;
      border-radius: 16px;
      line-height: 1.4;
      font-size: 14px;
    }
    .incoming {
      align-self: flex-start;
      background-color: #f1f0f0;
    }
    .outgoing {
      align-self: flex-end;
      background-color: #0084ff;
      color: white;
    }
    .footer {
      padding: 10px;
      border-top: 1px solid #ccc;
      display: flex;
      align-items: center;
      gap: 10px;
    }
    .footer input {
      flex: 1;
      padding: 8px 10px;
      border-radius: 20px;
      border: 1px solid #ccc;
      outline: none;
    }
    .footer button {
      background-color: #0084ff;
      color: white;
      border: none;
      padding: 8px 12px;
      border-radius: 50%;
      font-size: 16px;
      cursor: pointer;
    }

    .typing {
      display: inline-block;
      margin: 5px 0;
      padding: 8px 12px;
      border-radius: 16px;
      background-color: #f1f0f0;
      align-self: flex-start;
      font-size: 14px;
    }
    .typing span {
      display: inline-block;
      width: 6px;
      height: 6px;
      margin: 0 2px;
      background-color: #999;
      border-radius: 50%;
      animation: blink 1.4s infinite both;
    }
    .typing span:nth-child(2) {
      animation-delay: 0.2s;
    }
    .typing span:nth-child(3) {
      animation-delay: 0.4s;
    }
    @keyframes blink {
      0%, 80%, 100% {
        opacity: 0;
      }
      40% {
        opacity: 1;
      }
    }
  </style>
</head>
<body>
  <div class="messenger">
    <div class="header">নিহান চৌধুরী</div>
    <div class="chat-box" id="chat-box"></div>
    <div class="footer">
      <input type="text" id="userInput" placeholder="Type a message...">
      <button onclick="sendMessage()">➤</button>
    </div>
  </div>

  <script>
    const botData = {
      "নাম": "আমার নাম নিহান । ",
      "কি কর": "আমি এখন মোবাইলে গেমখেলি",
      "থাকো": "আমি ঢাকা মহাখালী থাকি।",
      "মা": "তুমি কি আমার পরিবার নিয়ে জানতে চাও ?!",
      "ধন্যবাদ": "তোমাকেও ধন্যবাদ। তোমার সাথে কথা বলে ভালো লাগছে।",
      "ভালো": "আমি ভালো আছি। তুমি কেমন আছ?",
      "তুমি কে": "আমি এক ডিজিটাল বন্ধু, যার নাম নিহান।"
    };

    function getBotReply(userMessage) {
      userMessage = userMessage.toLowerCase();
      for (let key in botData) {
        if (userMessage.includes(key)) {
          return botData[key];
        }
      }

      if (userMessage.match(/তোমার.*নাম|তুমি.*কে/)) return botData["নাম"];
      if (userMessage.match(/কর|কি.*কর/)) return botData["কি কর"];
      if (userMessage.match(/থাকো|বাড়ি|কোথায়/)) return botData["থাকো"];
      if (userMessage.match(/মা|চুরি/)) return botData["মা"];
      if (userMessage.match(/ধন্যবাদ|শুভেচ্ছা/)) return botData["ধন্যবাদ"];
      if (userMessage.match(/ভালো|কেমন/)) return botData["ভালো"];

      return "Hmm";
    }

    function showMessage(text, type) {
      const chatBox = document.getElementById("chat-box");
      const messageDiv = document.createElement("div");
      messageDiv.className = type + " message";
      messageDiv.textContent = text;
      chatBox.appendChild(messageDiv);
      chatBox.scrollTop = chatBox.scrollHeight;
    }

    function sendMessage() {
      const inputField = document.getElementById("userInput");
      const message = inputField.value.trim();
      if (message === "") return;

      showMessage(message, "outgoing");
      inputField.value = "";

      const chatBox = document.getElementById("chat-box");

      // Step 1: Wait 5 seconds before showing typing animation
      setTimeout(() => {
        const typingDiv = document.createElement("div");
        typingDiv.className = "typing";
        typingDiv.id = "typing-indicator";
        typingDiv.innerHTML = "<span></span><span></span><span></span>";
        chatBox.appendChild(typingDiv);
        chatBox.scrollTop = chatBox.scrollHeight;

        // Step 2: After another 5 seconds, show reply
        setTimeout(() => {
          const reply = getBotReply(message);
          const typingEl = document.getElementById("typing-indicator");
          if (typingEl) typingEl.remove();
          showMessage(reply, "incoming");
        }, 5000); // Show reply after typing for 5 seconds
      }, 5000); // Wait 5 seconds before typing starts
    }

    window.onload = function () {
      setTimeout(() => {
        showMessage("কেমন আছ? আমার সাথে কথা বলতে চাইলে নিচে দেখ মেসেজ পাঠানোর অপশন আছে । বাংলায় লিখ ", "incoming");
      }, 1000);
    };
  </script>
</body>
</html>

