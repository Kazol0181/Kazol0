<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Kazolkobi Facebook Clone</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 0;
      margin: 0;
      background-color: #f0f2f5;
    }
    .topbar {
      background-color: #1877f2;
      color: white;
      padding: 10px 15px;
    }
    .topbar input[type="text"] {
      width: 100%;
      padding: 6px;
      border-radius: 6px;
      border: none;
      font-size: 14px;
      margin-bottom: 10px;
    }
    .header {
      font-size: 24px;
      font-weight: bold;
      text-align: center;
    }
    .create-post {
      background: white;
      border-radius: 10px;
      padding: 15px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
      margin: 20px;
    }
    .profile-area {
      display: flex;
      align-items: center;
      gap: 10px;
    }
    .profile-area img {
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
    .fake-post {
      background: white;
      border-radius: 10px;
      padding: 15px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
      margin: 20px;
    }
    .post-header {
      display: flex;
      align-items: center;
      gap: 10px;
    }
    .post-header img {
      width: 40px;
      height: 40px;
      border-radius: 50%;
      object-fit: cover;
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
    .comment-toggle {
      background: none;
      border: none;
      color: #1877f2;
      cursor: pointer;
      padding: 5px 0;
      font-size: 14px;
    }
  </style>
  <!-- Firebase SDKs -->
  <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
</head>
<body>

<div class="topbar">
  <input type="text" placeholder="Search Kazolkobi...">
  <div class="header">Kazolkobi</div>
</div>

<div class="create-post">
  <div class="profile-area">
    <img id="profilePicPreview" src="https://i.imgur.com/8Km9tLL.png" alt="Profile">
    <input type="text" id="userName" placeholder="Your Name" />
  </div>
  <textarea id="postText" rows="3" placeholder="What's on your mind?"></textarea>
  <input type="file" id="profilePicInput" accept="image/*">
  <input type="file" id="postImageInput" accept="image/*">
  <button onclick="createPost()">Post</button>
</div>

<div id="outputPost"></div>

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
const database = firebase.database();

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

  const newPost = {
    id: thisId,
    name: userName,
    text: postText,
    profilePic: profilePicURL,
    postImage: postImageURL || "",
    timestamp: Date.now()
  };
  database.ref("posts/" + thisId).set(newPost);
  renderPostFromData(newPost);

  document.getElementById('postText').value = "";
  postImageURL = "";
}

function renderPostFromData(post) {
  let html = `
    <div class="fake-post" id="post-${post.id}">
      <div class="post-header">
        <img src="${post.profilePic}" alt="Profile">
        <div class="name-time">
          <strong>${post.name}</strong><br>
          <span>Just now</span>
        </div>
      </div>
      <div style="margin-top:10px;">${post.text}</div>
      ${post.postImage ? `<img class="uploaded-img" src="${post.postImage}" alt="Uploaded">` : ''}
      <div class="reaction-bar">
        <span id="like-${post.id}" onclick="likePost(${post.id})">👍 0</span>
        <span id="comment-count-${post.id}">💬 0</span>
        <span id="share-${post.id}">↪️ 0</span>
      </div>
      <button class="comment-toggle" onclick="toggleComments(${post.id})">Show Comments</button>
      <div style="display:none" id="comment-section-${post.id}">
        <input type="text" placeholder="Write a comment..." id="comment-input-${post.id}" style="width: 100%; padding: 6px; border: 1px solid #ccc; border-radius: 6px;" />
        <button onclick="postComment(${post.id})" style="margin-top: 5px; width: 100%; padding: 6px; border-radius: 6px; border: none; background: #0a66c2; color: white;">Post Comment</button>
        <div id="comments-${post.id}" style="margin-top: 10px;"></div>
      </div>
    </div>`;
  document.getElementById('outputPost').innerHTML = html + document.getElementById('outputPost').innerHTML;
  autoIncreaseShare(post.id);
  autoLikePost(post.id);
  autoComment(post.id);
}

function toggleComments(id) {
  const section = document.getElementById(`comment-section-${id}`);
  section.style.display = section.style.display === 'none' ? 'block' : 'none';
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

window.onload = function () {
  database.ref("posts").once("value", function(snapshot) {
    const data = snapshot.val();
    for (let key in data) {
      const post = data[key];
      postId = Math.max(postId, post.id);
      renderPostFromData(post);
    }
  });
};
</script>

</body>
</html>
