<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>My Comment Box</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
    }
    input, textarea {
      width: 100%;
      margin-bottom: 10px;
      padding: 8px;
    }
    button {
      padding: 10px 20px;
    }
    .comment {
      background: #f1f1f1;
      padding: 10px;
      margin: 10px 0;
      border-radius: 5px;
    }
  </style>
</head>
<body>
  <h2>কমেন্ট করুন</h2>
  <input type="text" id="name" placeholder="আপনার নাম">
  <textarea id="comment" placeholder="আপনার মন্তব্য লিখুন..."></textarea>
  <button onclick="submitComment()">পাঠান</button>

  <h3>সব মন্তব্য:</h3>
  <div id="comments"></div>

  <!-- Firebase SDK -->
  <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database.js"></script>

  <script>
    // তোমার Firebase Config
    const firebaseConfig = {
      apiKey: "AIzaSyDkfW0Yf-9oR64j5GAPuBW_1G-rqGK9cOY",
      authDomain: "kazol-35172.firebaseapp.com",
      databaseURL: "https://kazol-35172-default-rtdb.firebaseio.com",
      projectId: "kazol-35172",
      storageBucket: "kazol-35172.firebasestorage.app",
      messagingSenderId: "862146314863",
      appId: "1:862146314863:web:6f09888dce4bbda6d6439a",
      measurementId: "G-MSH3KSKBTJ"
    };

    // Firebase initialize
    const app = firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    // কমেন্ট সাবমিট ফাংশন
    function submitComment() {
      const name = document.getElementById("name").value;
      const comment = document.getElementById("comment").value;

      if (name && comment) {
        db.ref("comments").push({
          name: name,
          comment: comment
        });

        document.getElementById("name").value = "";
        document.getElementById("comment").value = "";
      }
    }

    // কমেন্ট দেখানোর ফাংশন
    db.ref("comments").on("value", function(snapshot) {
      const commentsDiv = document.getElementById("comments");
      commentsDiv.innerHTML = "";
      snapshot.forEach(function(childSnapshot) {
        const data = childSnapshot.val();
        const div = document.createElement("div");
        div.className = "comment";
        div.innerHTML = `<strong>${data.name}</strong><br>${data.comment}`;
        commentsDiv.appendChild(div);
      });
    });
  </script>
</body>
</html>
