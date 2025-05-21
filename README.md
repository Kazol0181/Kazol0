<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8">
  <title>কমেন্ট সিস্টেম</title>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      max-width: 600px;
      margin: 20px auto;
      padding: 10px;
    }
    textarea, input {
      width: 100%;
      font-size: 16px;
      padding: 8px;
      margin-bottom: 10px;
    }
    button {
      padding: 10px 20px;
      font-size: 16px;
      cursor: pointer;
    }
    .comment {
      border-bottom: 1px solid #ccc;
      margin-bottom: 10px;
      padding-bottom: 8px;
    }
    .timestamp {
      font-size: 12px;
      color: #666;
    }
  </style>
</head>
<body>

  <h2>কমেন্ট করুন</h2>
  <form id="commentForm">
    <input type="text" id="name" placeholder="আপনার নাম" required />
    <textarea id="comment" placeholder="আপনার মন্তব্য লিখুন..." required></textarea>
    <button type="submit">জমা দিন</button>
  </form>

  <h3>সকল কমেন্ট</h3>
  <div id="commentsList"></div>

  <script>
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

    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    const form = document.getElementById('commentForm');
    const nameInput = document.getElementById('name');
    const commentInput = document.getElementById('comment');
    const commentsList = document.getElementById('commentsList');

    form.addEventListener('submit', function (e) {
      e.preventDefault();
      const name = nameInput.value.trim();
      const comment = commentInput.value.trim();
      if (name && comment) {
        db.ref("comments").push({
          name,
          comment,
          createdAt: new Date().toISOString()
        }).then(() => {
          form.reset();
        });
      }
    });

    const formatDate = iso => {
      const date = new Date(iso);
      return date.toLocaleString('bn-BD', {
        dateStyle: 'medium',
        timeStyle: 'short'
      });
    };

    db.ref("comments").orderByChild("createdAt").on("value", snapshot => {
      commentsList.innerHTML = "";
      snapshot.forEach(child => {
        const { name, comment, createdAt } = child.val();
        const div = document.createElement('div');
        div.className = 'comment';
        div.innerHTML = `<strong>${name}</strong><br>${comment}<div class="timestamp">${formatDate(createdAt)}</div>`;
        commentsList.prepend(div);
      });
    });
  </script>
</body>
</html>
