<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
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
    <input type="text" placeholder="🔍 সার্চ করুন..." />
  </div>
  <div class="navbar">Kazolkobi</div>

  <div class="container">

    <div class="card">
      <h3>প্রোফাইল সেট করুন</h3>
      <input type="text" id="profileName" placeholder="আপনার নাম লিখুন" />
      <input type="file" id="profilePic" accept="image/*" />
      <button onclick="saveProfile()">সেভ প্রোফাইল</button>
    </div>

    <div class="card">
      <textarea id="postText" rows="3" placeholder="আপনার কী মনে হচ্ছে?"></textarea>
      <input type="file" id="postImage" accept="image/*" />
      <button onclick="createPost()">পোস্ট করুন</button>
    </div>

    <div id="postFeed"></div>
  </div>

  <!-- Firebase SDK -->
  <script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-database-compat.js"></script>

  <script>
    // Firebase config
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
    const db = firebase.database();

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
        reader.onload = e => {
          profile.avatar = e.target.result;
          alert("প্রোফাইল সেভ হয়েছে!");
        };
        reader.readAsDataURL(file);
      } else {
        profile.avatar = '';
        alert("প্রোফাইল সেভ হয়েছে!");
      }
    }

    function minutesAgo(date) {
      const now = new Date();
      const diff = Math.floor((now - new Date(date)) / 60000);
      if(diff < 1) return "এখনই";
      if(diff < 60) return `${diff} মিনিট আগে`;
      const hours = Math.floor(diff / 60);
      if(hours < 24) return `${hours} ঘন্টা আগে`;
      const days = Math.floor(hours / 24);
      return `${days} দিন আগে`;
    }

    function createPost() {
      const text = document.getElementById("postText").value.trim();
      const file = document.getElementById("postImage").files[0];
      if (!text && !file) return alert("পোস্ট করার জন্য কিছু লিখুন বা ছবি দিন।");

      const createdAt = new Date().toISOString();

      if (file) {
        const reader = new FileReader();
        reader.onload = function (e) {
          const imageSrc = e.target.result;
          savePostToFirebase(text, imageSrc, createdAt);
        };
        reader.readAsDataURL(file);
      } else {
        savePostToFirebase(text, null, createdAt);
      }

      document.getElementById("postText").value = '';
      document.getElementById("postImage").value = '';
    }

    function savePostToFirebase(text, imageSrc, createdAt) {
      const postData = {
        text: text,
        image: imageSrc,
        createdAt: createdAt,
        user: profile.name,
        avatar: profile.avatar,
        likeCount: 0,
        commentCount: 0
      };
      const newPostKey = db.ref().child('posts').push().key;
      let updates = {};
      updates['/posts/' + newPostKey] = postData;
      db.ref().update(updates);
    }

    function addPost(text, imageSrc, createdAt, userName = profile.name, avatar = profile.avatar, likeCount = 0, commentCount = 0, postKey = null) {
      const post = document.createElement("div");
      post.className = "card";
      post.setAttribute("data-user", userName);
      if(postKey) post.setAttribute("data-postkey", postKey);

      const avatarHTML = avatar ? `<img class="avatar" src="${avatar}" onclick="viewProfile()">` : `<div class="avatar"></div>`;
      post.innerHTML = `
        <div class="post-header">
          <div class="user-info">
            ${avatarHTML}
            <div>
              <div class="name" onclick="viewProfile()">${userName} <button class="follow-btn" onclick="followUser(event)">ফলো</button></div>
              <div class="timestamp">${minutesAgo(createdAt)}</div>
            </div>
          </div>
        </div>
        <div class="post-content">
          <div style="margin-top:10px;">${text}</div>
          ${imageSrc ? `<img src="${imageSrc}">` : ""}
        </div>
        <div class="counts">
          👍 <span class="likeCount">${likeCount}</span> | 💬 <span class="commentCount">${commentCount}</span>
        </div>
        <div class="action-buttons">
          <button onclick="likePost(this)">👍 লাইক</button>
          <button onclick="toggleComments(this)">💬 কমেন্ট</button>
          <button onclick="alert('শেয়ার করা হয়েছে!')">↗️ শেয়ার</button>
          <button onclick="deletePost(this)">🗑️ ডিলিট</button>
        </div>
        <div class="comments">
          <div class="comment-box">
            <img src="${avatar || 'https://via.placeholder.com/30'}" />
            <input type="text" placeholder="মন্তব্য লিখুন..." />
            <button onclick="addComment(this)">পোস্ট</button>
          </div>
          <ul></ul>
        </div>
      `;
      document.getElementById("postFeed").prepend(post);
    }

    // লাইক বাটন হ্যান্ডলার
    function likePost(btn) {
      const post = btn.closest(".card");
      const likeCountSpan = post.querySelector(".likeCount");
      let count = parseInt(likeCountSpan.innerText) || 0;
      count++;
      likeCountSpan.innerText = count;

      // Firebase আপডেট
      const postKey = post.getAttribute("data-postkey");
      if (postKey) {
        db.ref('posts/' + postKey).update({ likeCount: count });
      }
    }

    // কমেন্ট টগল
    function toggleComments(btn) {
      const post = btn.closest(".card");
      const comments = post.querySelector(".comments");
      comments.style.display = comments.style.display === "none" || comments.style.display === "" ? "block" : "none";
    }

    // কমেন্ট যোগ করা
    function addComment(btn) {
      const commentBox = btn.closest(".comment-box");
      const input = commentBox.querySelector("input");
      const text = input.value.trim();
      if (!text) return;

      const ul = btn.closest(".comments").querySelector("ul");
      const post = btn.closest(".card");
      const postKey = post.getAttribute("data-postkey");

      // নতুন কমেন্ট এলিমেন্ট
      const li = document.createElement("li");
      li.innerHTML = `
        <img src="${profile.avatar || 'https://via.placeholder.com/30'}" onclick="viewProfile()" />
        <div class="comment-content">
          <strong onclick="viewProfile()">${profile.name}</strong>
          ${text}
        </div>
      `;
      ul.appendChild(li);
      input.value = '';

      // Firebase এ কমেন্ট আপডেট
      if (postKey) {
        const commentRef = db.ref('comments/' + postKey);
        const newCommentKey = commentRef.push().key;
        const commentData = {
          user: profile.name,
          avatar: profile.avatar,
          text: text,
          createdAt: new Date().toISOString()
        };
        let updates = {};
        updates[newCommentKey] = commentData;
        commentRef.update(updates);

        // পোস্টে কমেন্ট কাউন্ট আপডেট
        const commentCountSpan = post.querySelector(".commentCount");
        let commentCount = parseInt(commentCountSpan.innerText) || 0;
        commentCount++;
        commentCountSpan.innerText = commentCount;
        db.ref('posts/' + postKey).update({ commentCount: commentCount });
      }
    }

    // পোস্ট ডিলিট করা
    function deletePost(btn) {
      const post = btn.closest(".card");
      const userName = post.getAttribute("data-user");
      const postKey = post.getAttribute("data-postkey");
      if (userName === profile.name) {
        if(postKey){
          db.ref('posts/' + postKey).remove();
          db.ref('comments/' + postKey).remove();
        }
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

    // পেজ লোড হলে Firebase থেকে পোস্টগুলো লোড করবো
    window.onload = function() {
      const postFeed = document.getElementById('postFeed');
      db.ref('posts').on('value', snapshot => {
        postFeed.innerHTML = ''; // পুরনো পোস্ট ডিলিট করে নতুন লোড দিবো
        const posts = snapshot.val();
        if (!posts) return;
        Object.entries(posts).sort((a,b) => new Date(b[1].createdAt) - new Date(a[1].createdAt)).forEach(([key, post]) => {
          addPost(post.text, post.image, post.createdAt, post.user, post.avatar, post.likeCount || 0, post.commentCount || 0, key);
          loadComments(key);
        });
      });
    };

    // কমেন্ট লোড ফাংশন
    function loadComments(postKey) {
      const post = document.querySelector(`[data-postkey="${postKey}"]`);
      if (!post) return;
      const commentList = post.querySelector(".comments ul");
      const commentBox = post.querySelector(".comments");
      db.ref('comments/' + postKey).on('value', snapshot => {
        commentList.innerHTML = '';
        const comments = snapshot.val();
        if (!comments) return;
        Object.values(comments).forEach(comment => {
          const li = document.createElement("li");
          li.innerHTML = `
            <img src="${comment.avatar || 'https://via.placeholder.com/30'}" onclick="viewProfile()" />
            <div class="comment-content">
              <strong onclick="viewProfile()">${comment.user}</strong>
              ${comment.text}
            </div>
          `;
          commentList.appendChild(li);
        });
      });
    }

  </script>
</body>
</html>
