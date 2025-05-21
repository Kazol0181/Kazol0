
<html lang="bn">
<head>
  <meta charset="UTF-8" />
  <title>কমেন্ট সিস্টেম (Firebase Realtime DB)</title>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      max-width: 600px;
      margin: 20px auto;
      padding: 10px;
    }
    textarea {
      width: 100%;
      height: 80px;
      font-size: 16px;
      padding: 8px;
      resize: vertical;
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
    <textarea id="comment" placeholder="এখানে কমেন্ট লিখুন..." required></textarea><br><br>
    <button type="submit">কমেন্ট করুন</button>
  </form>

  <h3>সকল কমেন্ট</h3>
  <div id="commentsList"></div>

  <script>
    // তোমার Firebase কনফিগারেশন
    const firebaseConfig = {
      apiKey: "AIzaSyDkfW0Yf-9oR64j5GAPuBW_1G-rqGK9cOY",
      authDomain: "kazol-35172.firebaseapp.com",
      databaseURL: "https://kazol-35172-default-rtdb.firebaseio.com",
      projectId: "kazol-35172",
      storageBucket: "kazol-35172.firebasestorage.app",
      messagingSenderId: "862146314863",
      appId: "1:862146314863:web:15d961531d90c23cd6439a",
      measurementId: "G-ECVXRGP67B"
    };

    // Initialize Firebase
    firebase.initializeApp(firebaseConfig);
    const database = firebase.database();

    const commentForm = document.getElementById('commentForm');
    const commentsList = document.getElementById('commentsList');

    // কমেন্ট জমা দেওয়া
    commentForm.addEventListener('submit', function(e) {
      e.preventDefault();

      const commentText = document.getElementById('comment').value.trim();
      if (commentText === "") return;

      // নতুন কমেন্ট Firebase এ push করা
      const newCommentRef = database.ref('comments').push();
      newCommentRef.set({
        comment: commentText,
        createdAt: new Date().toISOString()
      })
      .then(() => {
        alert('কমেন্ট সফলভাবে জমা হয়েছে!');
        commentForm.reset();
      })
      .catch(error => {
        alert('কমেন্ট জমা দিতে সমস্যা হয়েছে।');
        console.error(error);
      });
    });

    // Firebase থেকে কমেন্টগুলো রিয়েলটাইমে লোড করা
    const commentsRef = database.ref('comments').orderByChild('createdAt');
    commentsRef.on('value', (snapshot) => {
      commentsList.innerHTML = ""; // পুরনো কমেন্ট মুছে দাও
      const comments = snapshot.val();
      if (comments) {
        // টাইমস্ট্যাম্প সুন্দর দেখানোর জন্য ফাংশন
        const formatDate = isoString => {
          const d = new Date(isoString);
          return d.toLocaleString('bn-BD', { dateStyle: 'medium', timeStyle: 'short' });
        };

        // সব কমেন্ট এলিমেন্ট তৈরি করে যোগ করা
        Object.keys(comments).forEach(key => {
          const c = comments[key];
          const div = document.createElement('div');
          div.classList.add('comment');
          div.innerHTML = `
            <p>${c.comment}</p>
            <div class="timestamp">${formatDate(c.createdAt)}</div>
          `;
          commentsList.prepend(div); // নতুন কমেন্ট শীর্ষে দেখাবে
        });
      } else {
        commentsList.innerHTML = "<p>কোনো কমেন্ট পাওয়া যায়নি।</p>";
      }
    });
  </script>

</body>
</html>

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
