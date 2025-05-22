<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Kazolkobi</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
      background-color: #f0f2f5;
    }

    /* Header Area */
    .search-bar {
      background-color: #4267B2;
      padding: 10px;
    }
    .search-bar input {
      width: 100%;
      padding: 8px 12px;
      border: none;
      border-radius: 6px;
      font-size: 16px;
    }

    .header {
      background-color: #4267B2;
      color: white;
      padding: 12px 20px;
      font-size: 24px;
      font-weight: bold;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }

    .container {
      padding: 15px;
    }

    .create-post, .fake-post {
      background: white;
      border-radius: 10px;
      padding: 15px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
      margin-bottom: 20px;
    }

    .profile-area {
      display: flex;
      align-items: center;
      gap: 10px;
    }

    .profile-area img, .post-header img {
      width: 40px;
      height: 40px;
      border-radius: 50%;
      object-fit: cover;
    }

    .create-post textarea {
      width: 100%;
      border: none;
      resize: none;
      font-size: 16px;
      margin-top: 10px;
      outline: none;
    }

    .create-post input[type="file"] {
      margin-top: 10px;
    }

    .create-post button {
      width: 100%;
      padding: 10px;
      background: #1877f2;
      color: white;
      border: none;
      border-radius: 6px;
      margin-top: 10px;
      font-size: 16px;
    }

    .post-header {
      display: flex;
      align-items: center;
      gap: 10px;
    }

    .name-time {
      font-size: 14px;
    }

    .uploaded-img {
      width: 100%;
      margin-top: 10px;
      border-radius: 8px;
    }

    .reaction-bar {
      margin-top: 10px;
      display: flex;
      justify-content: space-between;
      font-size: 14px;
      color: #555;
    }

    .reaction-bar span {
      cursor: pointer;
    }

    .liked {
      color: #1877f2;
    }

    .comments-section {
      display: none;
      margin-top: 10px;
    }

    .toggle-btn {
      background: #e4e6eb;
      border: none;
      padding: 4px 8px;
      font-size: 12px;
      border-radius: 4px;
      cursor: pointer;
      margin-top: 5px;
    }

  </style>
</head>
<body>

  <div class="search-bar">
    <input type="text" placeholder="Search Kazolkobi...">
  </div>
  <div class="header">Kazolkobi</div>

  <div class="container">
    <div class="create-post">
      <div class="profile-area">
        <img id="profilePicPreview" src="https://i.imgur.com/8Km9tLL.png" alt="Profile" />
        <input type="text" id="userName" placeholder="Your Name" />
      </div>
      <textarea id="postText" rows="3" placeholder="What's on your mind?"></textarea>
      <input type="file" id="profilePicInput" accept="image/*" />
      <input type="file" id="postImageInput" accept="image/*" />
      <button onclick="createPost()">Post</button>
    </div>

    <div id="outputPost"></div>
  </div>

  <!-- Firebase SDK (non-module version for compatibility) -->
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
  <script>
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
    firebase.initializeApp(firebaseConfig);
  </script>

  <script>
    let profilePicURL = 'https://i.imgur.com/8Km9tLL.png';
    let postImageURL = '';
    let postId = 0;

    document.getElementById('profilePicInput').addEventListener('change', function(e) {
      const reader = new FileReader();
      reader.onload = function () {
        profilePicURL = reader.result;
        document.getElementById('profilePicPreview').src = profilePicURL;
      }
      reader.readAsDataURL(e.target.files[0]);
    });

    document.getElementById('postImageInput').addEventListener('change', function(e) {
      const reader = new FileReader();
      reader.onload = function () {
        postImageURL = reader.result;
      }
      reader.readAsDataURL(e.target.files[0]);
    });

    function createPost() {
      const postText = document.getElementById('postText').value;
      const userName = document.getElementById('userName').value || "Anonymous";
      const thisId = ++postId;

      let html = `
        <div class="fake-post" id="post-${thisId}">
          <div class="post-header">
            <img src="${profilePicURL}" alt="Profile" />
            <div class="name-time">
              <strong>${userName}</strong><br>
              <span>Just now</span>
            </div>
          </div>
          <div style="margin-top:10px;">${postText}</div>
          ${postImageURL ? `<img class="uploaded-img" src="${postImageURL}" alt="Uploaded">` : ''}
          <div class="reaction-bar">
            <span id="like-${thisId}" onclick="likePost(${thisId})">👍 0</span>
            <span id="comment-count-${thisId}">💬 0</span>
            <span id="share-${thisId}">↪️ 0</span>
          </div>
          <button class="toggle-btn" onclick="toggleComments(${thisId})">Show/Hide Comments</button>
          <div class="comments-section" id="comments-section-${thisId}">
            <input type="text" placeholder="Write a comment..." id="comment-input-${thisId}" style="width: 100%; padding: 6px; border: 1px solid #ccc; border-radius: 6px;" />
            <button onclick="postComment(${thisId})" style="margin-top: 5px; width: 100%; padding: 6px; border-radius: 6px; border: none; background: #0a66c2; color: white;">Post Comment</button>
            <div id="comments-${thisId}" style="margin-top: 10px;"></div>
          </div>
        </div>
      `;
      document.getElementById('outputPost').innerHTML = html + document.getElementById('outputPost').innerHTML;

      document.getElementById('postText').value = "";
      postImageURL = "";

      autoIncreaseShare(thisId);
      autoLikePost(thisId);
      autoComment(thisId);
    }

    function likePost(id) {
      const likeBtn = document.getElementById(`like-${id}`);
      if (!likeBtn.classList.contains("liked")) {
        let count = parseInt(likeBtn.innerText.split(" ")[1]);
        likeBtn.innerText = `👍 ${count + 1}`;
        likeBtn.classList.add("liked");
        likeBtn.style.color = "#1877f2";
      }
    }

    function postComment(id) {
      const input = document.getElementById(`comment-input-${id}`);
      const text = input.value.trim();
      if (!text) return;
      const commentBox = document.getElementById(`comments-${id}`);
      commentBox.innerHTML += `<div style="margin:5px 0;">💬 ${text}</div>`;
      input.value = "";

      const countSpan = document.getElementById(`comment-count-${id}`);
      let count = parseInt(countSpan.innerText.split(" ")[1]);
      countSpan.innerText = `💬 ${count + 1}`;
    }

    function toggleComments(id) {
      const section = document.getElementById(`comments-section-${id}`);
      section.style.display = section.style.display === "none" || !section.style.display ? "block" : "none";
    }

    function autoIncreaseShare(id) {
      let shareCount = 0;
      const shareSpan = document.getElementById(`share-${id}`);
      setInterval(() => {
        shareCount++;
        shareSpan.innerText = `↪️ ${shareCount}`;
      }, 15000);
    }

    function autoLikePost(id) {
      let likeCount = 0;
      const likeSpan = document.getElementById(`like-${id}`);
      setInterval(() => {
        likeCount++;
        likeSpan.innerText = `👍 ${likeCount}`;
      }, 10000);
    }

    function autoComment(id) {
      const commentBox = document.getElementById(`comments-${id}`);
      const countSpan = document.getElementById(`comment-count-${id}`);
      const autoComments = ["Nice!", "Awesome!", "Cool!", "Great post!", "Love it!"];
      let i = 0;

      setInterval(() => {
        const text = autoComments[i % autoComments.length];
        commentBox.innerHTML += `<div style="margin:5px 0;">💬 ${text}</div>`;
        let count = parseInt(countSpan.innerText.split(" ")[1]);
        countSpan.innerText = `💬 ${count + 1}`;
        i++;
      }, 20000);
    }
  </script>

</body>
</html>
