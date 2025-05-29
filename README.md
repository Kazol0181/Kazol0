<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Facebook Mobile View Clone</title>
  <style>
    :root {
      --bg: #f0f2f5;
      --text: #000;
      --card: #fff;
    }

    body.dark {
      --bg: #18191a;
      --text: #e4e6eb;
      --card: #242526;
    }

    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: var(--bg);
      color: var(--text);
    }

    .navbar {
      background-color: #1877f2;
      color: white;
      padding: 10px 16px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-weight: bold;
      position: sticky;
      top: 0;
      z-index: 10;
    }

    .top-icons {
      background: var(--card);
      display: flex;
      justify-content: space-around;
      padding: 10px 0;
      border-bottom: 1px solid #ccc;
      position: sticky;
      top: 44px;
      z-index: 9;
    }

    .top-icons div {
      text-align: center;
      font-size: 14px;
    }

    .story-section {
      display: flex;
      overflow-x: auto;
      padding: 10px;
      background-color: var(--card);
    }

    .story {
      min-width: 100px;
      height: 160px;
      background-color: #ddd;
      border-radius: 10px;
      margin-right: 10px;
      position: relative;
      flex-shrink: 0;
    }

    .story::after {
      content: "Story";
      position: absolute;
      bottom: 5px;
      left: 5px;
      font-size: 12px;
      background: rgba(0,0,0,0.5);
      color: white;
      padding: 2px 4px;
      border-radius: 3px;
    }

    .post {
      background: var(--card);
      margin: 10px;
      border-radius: 8px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
    }

    .post-header {
      display: flex;
      align-items: center;
      padding: 10px;
      justify-content: space-between;
    }

    .post-header-left {
      display: flex;
      align-items: center;
    }

    .avatar {
      width: 40px;
      height: 40px;
      border-radius: 50%;
      background: gray;
      margin-right: 10px;
    }

    .post-user {
      font-weight: bold;
      font-size: 14px;
    }

    .follow-btn {
      background-color: #1877f2;
      color: white;
      border: none;
      border-radius: 4px;
      padding: 4px 8px;
      font-size: 12px;
      cursor: pointer;
    }

    .post-time {
      font-size: 10px;
      color: gray;
      margin-left: 50px;
      margin-top: -10px;
      padding-bottom: 5px;
    }

    .post-content {
      padding: 0 10px 10px 10px;
      font-size: 14px;
    }

    .post-image {
      width: 100%;
      height: auto;
    }

    .reaction-stats {
      padding: 0 10px;
      font-size: 11px;
      color: #888;
    }

    .post-actions {
      display: flex;
      justify-content: space-around;
      padding: 10px;
      border-top: 1px solid #ddd;
      font-size: 14px;
      color: #555;
    }

    .post-actions div {
      display: flex;
      align-items: center;
      gap: 5px;
      color: #000;
      background: #fff;
      padding: 5px 10px;
      border-radius: 5px;
      cursor: pointer;
    }

    .comments {
      display: none;
      padding: 10px;
      font-size: 13px;
      color: #333;
    }

    .footer {
      text-align: center;
      font-size: 12px;
      color: #888;
      margin: 60px 0 20px;
    }

    .toggle-darkmode {
      position: fixed;
      top: 10px;
      right: 10px;
      background: #333;
      color: white;
      padding: 5px 10px;
      border-radius: 5px;
      font-size: 12px;
      cursor: pointer;
      z-index: 20;
    }
  </style>
</head>
<body>

  <div class="toggle-darkmode" onclick="toggleDarkMode()">Dark Mode</div>

  <div class="navbar">
    <div>facebook</div>
    <div>বন্ধু</div>
  </div>

  <div class="top-icons">
    <div>Home</div>
    <div>Message</div>
    <div>Notification</div>
    <div>Profile</div>
  </div>

  <div class="story-section">
    <div class="story"></div>
    <div class="story"></div>
    <div class="story"></div>
    <div class="story"></div>
  </div>

  <div id="posts"></div>

  <div class="post">
    <div class="post-header">
      <div class="post-header-left">
        <div class="avatar"></div>
        <div class="post-user">Friend Suggestion</div>
      </div>
    </div>
    <div class="post-content">আপনি "ইমরান খান" কে চেনেন? <br><br><button>Add Friend</button></div>
  </div>

  <div class="footer">ডেমো ফেসবুক ভিউ - শুধুমাত্র দেখার জন্য</div>

  <script>
    function toggleDarkMode() {
      document.body.classList.toggle('dark');
    }

    function toggleComments(button) {
      const post = button.closest('.post');
      const comments = post.querySelector('.comments');
      comments.style.display = (comments.style.display === 'block') ? 'none' : 'block';
    }

    const names = [
      "আরিয়ান হোসেন", "তানভীর ইসলাম", "নিহান রহমান", "ইভান আলম", "শিহাব চৌধুরী",
      "রায়ান কবির", "জায়ান মাহমুদ", "তাজওয়ার আহমেদ", "রিদান সিদ্দিকী", "সায়ান জামান",
      "আফনান খন্দকার", "জিয়ান হাসান", "সাবিহ আনোয়ার", "আর্শ রহমান", "মাহের ইমতিয়াজ",
      "নেহান হক", "ইউসুফ আদনান", "রাফেদ আশফাক", "অয়ন মোবারক", "মিরাজ তাহমিদ",
      "ইশরাক হোসাইন", "নিহান বাবু"
    ];

    const postsContainer = document.getElementById('posts');
    for (let i = 0; i < names.length; i++) {
      const post = document.createElement('div');
      post.className = 'post';
      const likes = Math.floor(Math.random() * 200);
      const commentsCount = Math.floor(Math.random() * 100);
      const shares = Math.floor(Math.random() * 50);
      const minutesAgo = Math.floor(Math.random() * 59) + 1;

      const userName = names[i];
      const postImage = (userName === "নিহান বাবু")
        ? '<div class="post-image"><img src="https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi6Sh7_ja2k2Hw6kmLqfWolMbuUlHkgZ8MnjDz-pYOeZaTEslIbYHeU89k1shSHl15_q_8hktuu2S29Jx9RUjSkynibzNtMk0ZqrT0xA4_FazGdUbNyECH5PQEZn6ofop3vkyKH90lAWs8DF_wtpHBpFwG4F_7nG4vpe63GGLFmPYtDH3d4CKlE6vFTc-Y/s200/1747369246868.jpg" style="width:100%; height:auto;" /></div>'
        : '<div class="post-image"></div>';

      post.innerHTML = `
        <div class="post-header">
          <div class="post-header-left">
            <div class="avatar"></div>
            <div class="post-user">${userName}</div>
          </div>
          <button class="follow-btn">Follow</button>
        </div>
        <div class="post-time">${minutesAgo} minutes ago</div>
        <div class="post-content">আজকের দিনটা ভালো যাচ্ছে।</div>
        ${postImage}
        <div class="reaction-stats">${likes} Likes · ${commentsCount} Comments · ${shares} Shares</div>
        <div class="post-actions">
          <div>Like</div>
          <div onclick="toggleComments(this)">Comment</div>
          <div>Share</div>
        </div>
        <div class="comments">
          <strong>মন্তব্য ১:</strong> দারুন লাগছে!<br>
          <strong>মন্তব্য ২:</strong> একদম সত্যি কথা।
        </div>
      `;
      postsContainer.appendChild(post);
    }
  </script>
</body>
</html>
