<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Kazolkobi</title>
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', sans-serif;
      background: #e9ebee;
    }

    .search-bar {
      background: white;
      padding: 10px 16px;
      display: flex;
      justify-content: center;
      box-shadow: 0 1px 2px rgba(0,0,0,0.1);
      position: sticky;
      top: 0;
      z-index: 999;
    }

    .search-bar input {
      width: 100%;
      max-width: 400px;
      padding: 10px;
      border-radius: 20px;
      border: 1px solid #ccc;
    }

    .navbar {
      background-color: #1877f2;
      color: white;
      padding: 14px;
      font-size: 26px;
      font-weight: bold;
      text-align: center;
      font-family: 'Segoe UI', sans-serif;
      letter-spacing: 0.5px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }

    .container {
      max-width: 500px;
      margin: 0 auto;
      padding: 10px;
    }

    .card {
      background: #fff;
      border-radius: 10px;
      padding: 15px;
      margin-top: 15px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.05);
    }

    input, textarea, button {
      font-family: inherit;
    }

    input[type="text"],
    input[type="file"],
    textarea {
      width: 100%;
      margin-top: 8px;
      padding: 10px;
      border: 1px solid #ccc;
      border-radius: 5px;
    }

    button {
      background-color: #1877f2;
      color: white;
      padding: 10px;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      margin-top: 10px;
      font-weight: bold;
    }

    .post-header {
      display: flex;
      align-items: center;
      justify-content: space-between;
    }

    .user-info {
      display: flex;
      align-items: center;
    }

    .avatar {
      width: 40px;
      height: 40px;
      border-radius: 50%;
      object-fit: cover;
      margin-right: 10px;
      background: gray;
      cursor: pointer;
    }

    .name {
      font-weight: bold;
      font-size: 16px;
      cursor: pointer;
    }

    .timestamp {
      font-size: 12px;
      color: gray;
    }

    .post-content img {
      max-width: 100%;
      margin-top: 10px;
      border-radius: 10px;
    }

    .counts {
      font-size: 14px;
      color: gray;
      margin-top: 8px;
    }

    .action-buttons {
      display: flex;
      justify-content: space-around;
      margin-top: 10px;
      border-top: 1px solid #ddd;
      padding-top: 10px;
    }

    .action-buttons button {
      background: none;
      border: none;
      color: #606770;
      font-weight: bold;
      padding: 8px;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 5px;
      cursor: pointer;
    }

    .action-buttons button:hover {
      background: #f0f2f5;
      border-radius: 5px;
    }

    .comments {
      display: none;
      margin-top: 10px;
    }

    .comment-box {
      display: flex;
      align-items: center;
      margin-top: 10px;
    }

    .comment-box img {
      width: 30px;
      height: 30px;
      border-radius: 50%;
      margin-right: 8px;
    }

    .comment-box input {
      flex: 1;
    }

    .comments ul {
      list-style: none;
      padding-left: 0;
      margin-top: 10px;
    }

    .comments li {
      display: flex;
      align-items: flex-start;
      margin-bottom: 10px;
    }

    .comments li img {
      width: 30px;
      height: 30px;
      border-radius: 50%;
      margin-right: 8px;
      cursor: pointer;
    }

    .comment-content {
      background: #f0f2f5;
      padding: 8px 12px;
      border-radius: 15px;
    }

    .comment-content strong {
      display: block;
      margin-bottom: 4px;
      cursor: pointer;
    }

    .follow-btn {
      background: #e4e6eb;
      color: #1877f2;
      border: none;
      padding: 4px 10px;
      border-radius: 15px;
      margin-left: 6px;
      font-size: 12px;
      cursor: pointer;
    }

    @media (max-width: 480px) {
      .navbar {
        font-size: 20px;
      }
    }
  </style>
</head>
<body>

  <div class="search-bar">
    <input type="text" placeholder="🔍 সার্চ করুন...">
  </div>
  <div class="navbar">Kazolkobi</div>

  <div class="container">

    <div class="card">
      <h3>প্রোফাইল সেট করুন</h3>
      <input type="text" id="profileName" placeholder="আপনার নাম লিখুন">
      <input type="file" id="profilePic" accept="image/*">
      <button onclick="saveProfile()">সেভ প্রোফাইল</button>
    </div>

    <div class="card">
      <textarea id="postText" rows="3" placeholder="আপনার কী মনে হচ্ছে?"></textarea>
      <input type="file" id="postImage" accept="image/*">
      <button onclick="createPost()">পোস্ট করুন</button>
    </div>

    <div id="postFeed"></div>
  </div>

  <script>
    let profile = {
      name: "নাম নেই",
      avatar: "",
      followers: [],
    };

    function saveProfile() {
      const name = document.getElementById("profileName").value.trim();
      const file = document.getElementById("profilePic").files[0];
      if (name) profile.name = name;
      if (file) {
        const reader = new FileReader();
        reader.onload = e => profile.avatar = e.target.result;
        reader.readAsDataURL(file);
      } else {
        profile.avatar = '';
      }
      alert("প্রোফাইল সেভ হয়েছে!");
    }

    function minutesAgo(date) {
      const diff = Math.floor((new Date() - date) / 60000);
      return `${diff} মিনিট আগে`;
    }

    function createPost() {
      const text = document.getElementById("postText").value.trim();
      const file = document.getElementById("postImage").files[0];
      if (!text && !file) return alert("পোস্ট করার জন্য কিছু লিখুন বা ছবি দিন।");

      const reader = new FileReader();
      const createdAt = new Date();
      reader.onload = function (e) {
        const imageSrc = file ? e.target.result : null;
        addPost(text, imageSrc, createdAt);
      };
      if (file) reader.readAsDataURL(file);
      else addPost(text, null, createdAt);

      document.getElementById("postText").value = '';
      document.getElementById("postImage").value = '';
    }

    function addPost(text, imageSrc, createdAt) {
      const post = document.createElement("div");
      post.className = "card";
      const avatarHTML = profile.avatar ? `<img class="avatar" src="${profile.avatar}" onclick="viewProfile()">` : `<div class="avatar"></div>`;
      post.innerHTML = `
        <div class="post-header">
          <div class="user-info">
            ${avatarHTML}
            <div>
              <div class="name" onclick="viewProfile()">${profile.name} <button class="follow-btn" onclick="followUser(event)">ফলো</button></div>
              <div class="timestamp">${minutesAgo(createdAt)}</div>
            </div>
          </div>
        </div>
        <div class="post-content">
          <div style="margin-top:10px;">${text}</div>
          ${imageSrc ? `<img src="${imageSrc}">` : ""}
        </div>
        <div class="counts">
          👍 <span class="likeCount">0</span> | 💬 <span class="commentCount">0</span>
        </div>
        <div class="action-buttons">
          <button onclick="likePost(this)">👍 লাইক</button>
          <button onclick="toggleComments(this)">💬 কমেন্ট</button>
          <button onclick="alert('শেয়ার করা হয়েছে!')">↗️ শেয়ার</button>
          <button onclick="deletePost(this)">🗑️ ডিলিট</button>
        </div>
        <div class="comments">
          <div class="comment-box">
            <img src="${profile.avatar || 'https://via.placeholder.com/30'}">
            <input type="text" placeholder="মন্তব্য লিখুন...">
            <button onclick="addComment(this)">পোস্ট</button>
          </div>
          <ul></ul>
        </div>
      `;
      post.setAttribute("data-user", profile.name);
      document.getElementById("postFeed").prepend(post);
    }

    function likePost(btn) {
      const post = btn.closest(".card");
      const span = post.querySelector(".likeCount");
      span.innerText = parseInt(span.innerText) + 1;
    }

    function toggleComments(btn) {
      const post = btn.closest(".card");
      const comments = post.querySelector(".comments");
      comments.style.display = comments.style.display === "none" ? "block" : "none";
    }

    function addComment(btn) {
      const commentBox = btn.closest(".comment-box");
      const input = commentBox.querySelector("input");
      const text = input.value.trim();
      if (!text) return;

      const ul = btn.closest(".comments").querySelector("ul");
      const li = document.createElement("li");
      li.innerHTML = `
        <img src="${profile.avatar || 'https://via.placeholder.com/30'}" onclick="viewProfile()">
        <div class="comment-content">
          <strong onclick="viewProfile()">${profile.name}</strong>
          ${text}
        </div>
      `;
      ul.appendChild(li);
      input.value = '';

      const count = btn.closest(".card").querySelector(".commentCount");
      count.innerText = parseInt(count.innerText) + 1;
    }

    function deletePost(btn) {
      const post = btn.closest(".card");
      if (post.getAttribute("data-user") === profile.name) {
        post.remove();
      } else {
        alert("শুধু নিজের পোস্ট ডিলিট করা যাবে।");
      }
    }

    function viewProfile() {
      alert("প্রোফাইল ভিউ ফিচার আসছে...");
    }

    function followUser(e) {
      e.stopPropagation();
      alert("ফলো করা হয়েছে!");
    }
  </script>

</body>
</html>
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
