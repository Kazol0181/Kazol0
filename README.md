<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Many.com</title>
  <script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-firestore-compat.js"></script>
  <style>
    /* আগের সব CSS ঠিক রাখা হয়েছে */
    body { font-family: Arial; background: #f0f2f5; margin: 0; }
    header { background: #1877f2; color: white; padding: 15px; text-align: center; font-size: 24px; font-weight: bold; }
    .container { padding: 15px; max-width: 600px; margin: auto; background: #fff; border-radius: 10px; margin-top: 20px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); }
    .user-info { display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; }
    .left-info { display: flex; align-items: center; gap: 10px; }
    .profile-pic { width: 40px; height: 40px; background: gray; border-radius: 50%; }
    .follow-btn { padding: 5px 10px; border: 1px solid #1877f2; background: white; color: #1877f2; border-radius: 5px; cursor: pointer; }
    .content { margin-top: 10px; }
    .actions { margin-top: 10px; display: flex; gap: 10px; flex-wrap: wrap; }
    .actions button { padding: 5px 10px; border: none; border-radius: 5px; background: #e4e6eb; cursor: pointer; }
    #commentForm { margin-top: 10px; display: none; }
    #commentForm input { margin: 5px 0; padding: 5px; width: 100%; }
    .comment { background: #f0f2f5; padding: 5px; border-radius: 5px; margin-top: 5px; }
    .popup { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); display: none; justify-content: center; align-items: center; }
    .popup-content { background: #fff; padding: 20px; border-radius: 10px; text-align: center; }
    .admin-panel { margin-top: 20px; padding: 15px; background: #e9ebee; border-radius: 10px; }
    .admin-panel input { margin: 5px 0; width: 100%; padding: 5px; }
    @media (max-width: 600px) { .actions { flex-direction: column; } }
  </style>
</head>
<body>
  <header>Many.com</header>
  <div class="container">
    <div class="user-info">
      <div class="left-info">
        <div class="profile-pic"></div>
        <strong id="username">Rubaed</strong>
        <span style="color:gray;">8m</span>
      </div>
      <button class="follow-btn">Follow</button>
    </div>
    <div class="content">
      <p id="postText">Indeed, we have lost the starface</p>
      <div style="width:100%;text-align:center;padding:30px;background:#f0f0f0;border-radius:8px;">No image</div>
    </div>
    <div class="actions">
      <button onclick="likePost()">Like (<span id="likeCount">0</span>)</button>
      <button onclick="toggleCommentList()">Comment (<span id="commentCount">0</span>)</button>
      <button onclick="showLogin()">Message</button>
    </div>
    <div id="commentForm">
      <input type="text" id="commenterName" placeholder="Your name">
      <input type="text" id="commentText" placeholder="Your comment">
      <button onclick="postComment()">Post Comment</button>
    </div>
    <div id="comments" style="display:none;"></div>
    <div class="admin-panel">
      <h3>Admin Panel</h3>
      <input type="password" id="adminPass" placeholder="Password">
      <button onclick="unlockAdmin()">Login</button>
      <div id="adminTools" style="display:none;">
        <input type="text" id="editProfileName" placeholder="Edit Profile Name">
        <input type="text" id="editPostText" placeholder="Edit Post Text">
        <input type="number" id="editLikeCount" placeholder="Edit Like Count">
        <input type="text" id="adminCommentName" placeholder="Comment Name">
        <input type="text" id="adminCommentText" placeholder="Comment Text">
        <button onclick="applyAdminChanges()">Apply Changes</button>
        <button onclick="adminPostComment()">Post Admin Comment</button>
      </div>
    </div>
  </div>
  <div class="popup" id="popup">
    <div class="popup-content">
      <p>Please login to message</p>
      <button onclick="closePopup()">Close</button>
    </div>
  </div>

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
    const db = firebase.firestore();

    let likeCount = 0;
    let commentCount = 0;
    let commentsVisible = false;

    function likePost() {
      likeCount++;
      document.getElementById("likeCount").innerText = likeCount;
      db.collection("posts").doc("mainPost").set({ likes: likeCount }, { merge: true });
    }

    function toggleCommentList() {
      document.getElementById("commentForm").style.display = "block";
      commentsVisible = !commentsVisible;
      document.getElementById("comments").style.display = commentsVisible ? "block" : "none";
    }

    function postComment() {
      const name = document.getElementById("commenterName").value.trim();
      const text = document.getElementById("commentText").value.trim();
      if (!name || !text) return alert("Name and comment required!");
      db.collection("comments").add({ name, text, timestamp: Date.now() });
      document.getElementById("commentText").value = "";
    }

    function showLogin() {
      document.getElementById("popup").style.display = "flex";
    }

    function closePopup() {
      document.getElementById("popup").style.display = "none";
    }

    function unlockAdmin() {
      const pass = document.getElementById("adminPass").value;
      if (pass === "0181") {
        document.getElementById("adminTools").style.display = "block";
      } else {
        alert("Incorrect password");
      }
    }

    function applyAdminChanges() {
      const newName = document.getElementById("editProfileName").value;
      const newText = document.getElementById("editPostText").value;
      const newLikes = document.getElementById("editLikeCount").value;
      if (newName) document.getElementById("username").innerText = newName;
      if (newText) document.getElementById("postText").innerText = newText;
      if (newLikes !== "") {
        likeCount = parseInt(newLikes);
        document.getElementById("likeCount").innerText = likeCount;
      }
    }

    function adminPostComment() {
      const name = document.getElementById("adminCommentName").value;
      const text = document.getElementById("adminCommentText").value;
      if (!name || !text) return;
      db.collection("comments").add({ name, text, timestamp: Date.now() });
    }

    db.collection("comments").orderBy("timestamp", "asc").onSnapshot(snapshot => {
      const commentsDiv = document.getElementById("comments");
      commentsDiv.innerHTML = "";
      commentCount = 0;
      snapshot.forEach(doc => {
        const data = doc.data();
        commentsDiv.innerHTML += `<div class='comment'><b>${data.name}</b>: ${data.text}</div>`;
        commentCount++;
      });
      document.getElementById("commentCount").innerText = commentCount;
    });

    db.collection("posts").doc("mainPost").get().then(doc => {
      if (doc.exists) {
        likeCount = doc.data().likes || 0;
        document.getElementById("likeCount").innerText = likeCount;
      }
    });
  </script>
</body>
</html>
