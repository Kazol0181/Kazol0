<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Facebook Post Clone</title>
  <style>
    body {
      background: #f0f2f5;
      font-family: Helvetica, Arial, sans-serif;
      padding: 10px;
    }
    .post-container {
      background: #fff;
      max-width: 500px;
      margin: auto;
      border-radius: 10px;
      box-shadow: 0 1px 2px rgba(0,0,0,0.1);
      overflow: hidden;
    }
    .post-header {
      display: flex;
      align-items: center;
      padding: 10px;
    }
    .page-logo {
      width: 40px;
      height: 40px;
      border-radius: 50%;
      margin-right: 10px;
    }
    .page-info {
      flex: 1;
    }
    .page-name {
      font-weight: bold;
      font-size: 15px;
    }
    .post-meta {
      font-size: 12px;
      color: gray;
    }
    .dots {
      font-size: 20px;
      padding-left: 10px;
      color: #606770;
    }
    .post-text {
      padding: 0 10px 10px 10px;
      font-size: 15px;
      line-height: 1.5;
    }
    .post-image {
      width: 100%;
      display: block;
    }
    .post-stats {
      display: flex;
      justify-content: space-between;
      padding: 5px 10px;
      font-size: 13px;
      color: #65676b;
    }
    .stats-left {
      display: flex;
      align-items: center;
      gap: 5px;
    }
    .emoji {
      font-size: 16px;
    }
    .post-actions {
      display: flex;
      border-top: 1px solid #ddd;
      border-bottom: 1px solid #ddd;
      text-align: center;
    }
    .post-actions div {
      flex: 1;
      padding: 8px 0;
      color: #65676b;
      font-size: 14px;
      font-weight: bold;
      cursor: pointer;
    }
    .post-actions div:hover {
      background: #f2f2f2;
    }
  </style>
</head>
<body>

<div class="post-container">
  <div class="post-header">
    <img class="page-logo" src="https://upload.wikimedia.org/wikipedia/commons/0/05/Facebook_Logo_%282019%29.png" alt="Page Logo">
    <div class="page-info">
      <div class="page-name">Daily Ba...</div>
      <div class="post-meta">1d · <img src="https://upload.wikimedia.org/wikipedia/commons/e/ec/Globe_icon.svg" width="10"></div>
    </div>
    <div class="dots">•••</div>
  </div>
  <div class="post-text">
    পুরোটা সময় মাথা নিচু করে ছিলেন-<br>অভিনেত্রী নুসরাত ফারিয়া
  </div>
  <img class="post-image" src="vidma_recorder_20052025_230701.jpg" alt="Post Image">
  <div class="post-stats">
    <div class="stats-left">
      <span class="emoji">🥲</span> 8.1K
    </div>
    <div>757 comments &nbsp;&nbsp; 70 shares</div>
  </div>
  <div class="post-actions">
    <div>Like</div>
    <div>Comment</div>
    <div>Share</div>
  </div>
</div>

</body>
</html>
