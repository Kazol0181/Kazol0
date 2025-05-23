<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Kazolkobi</title>

  <!-- Firebase SDKs -->
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

  <script>
    const firebaseConfig = {
      apiKey: "AIzaSyDkfW0Yf-9oR64j5GAPuBW_1G-rqGK9cOY",
      authDomain: "kazol-35172.firebaseapp.com",
      databaseURL: "https://kazol-35172-default-rtdb.firebaseio.com",
      projectId: "kazol-35172",
      storageBucket: "kazol-35172.firebasestorage.app",
      messagingSenderId: "862146314863",
      appId: "1:862146314863:web:d72e86690454dac3d6439a",
      measurementId: "G-JJEMZMMZY0"
    };
    firebase.initializeApp(firebaseConfig);
  </script>

  <style>
    /* [এখানে পূর্বের CSS কোড অপরিবর্তিত থাকছে, স্কিপ করা হলো ছোট করার জন্য] */
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
      likes: {}
    };

    function saveProfile() {
      const name = document.getElementById("profileName").value.trim();
      const file = document.getElementById("profilePic").files[0];
      if (name) profile.name = name;
      if (file) {
        const reader = new FileReader();
        reader.onload = e => {
          profile.avatar = e.target.result;
          document.querySelector(".navbar").innerText = `Kazolkobi (${profile.followers.length} ফলোয়ার)`;
        };
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

      const createdAt = new Date();

      const saveToDatabase = (imageUrl) => {
        const postData = {
          name: profile.name,
          avatar: profile.avatar,
          text,
          image: imageUrl || null,
          createdAt: createdAt.toISOString(),
        };
        const newPostKey = firebase.database().ref().child('posts').push().key;
        firebase.database().ref('posts/' + newPostKey).set(postData);
        addPost(text, imageUrl, createdAt);
      };

      if (file) {
        const reader = new FileReader();
        reader.onload = e => saveToDatabase(e.target.result);
        reader.readAsDataURL(file);
      } else {
        saveToDatabase(null);
      }

      document.getElementById("postText").value = '';
      document.getElementById("postImage").value = '';
    }

    function addPost(text, imageSrc, createdAt) {
      const post = document.createElement("div");
      post.className = "card";
      const timestamp = createdAt.getTime();
      post.setAttribute("data-timestamp", timestamp);
      post.setAttribute("data-user", profile.name);
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
      document.getElementById("postFeed").prepend(post);
    }

    function likePost(btn) {
      const post = btn.closest(".card");
      const postId = post.dataset.timestamp;
      const likeSpan = post.querySelector(".likeCount");

      if (!profile.likes[postId]) {
        profile.likes[postId] = true;
        likeSpan.innerText = parseInt(likeSpan.innerText) + 1;
        btn.innerText = "👎 আনলাইক";
      } else {
        delete profile.likes[postId];
        likeSpan.innerText = parseInt(likeSpan.innerText) - 1;
        btn.innerText = "👍 লাইক";
      }
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
          <button onclick="deleteComment(this)" style="margin-left:10px;color:red;border:none;background:none;">x</button>
        </div>
      `;
      ul.appendChild(li);
      input.value = '';

      const count = btn.closest(".card").querySelector(".commentCount");
      count.innerText = parseInt(count.innerText) + 1;
    }

    function deleteComment(btn) {
      const commentItem = btn.closest("li");
      commentItem.remove();
      const post = btn.closest(".card");
      const count = post.querySelector(".commentCount");
      count.innerText = parseInt(count.innerText) - 1;
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

    window.onload = function () {
      const postFeedRef = firebase.database().ref('posts');
      postFeedRef.on('child_added', snapshot => {
        const data = snapshot.val();
        addPost(data.text, data.image, new Date(data.createdAt));
      });
    };
  </script>

</body>
</html>
