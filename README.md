// File: src/App.jsx
import React, { useState, useEffect } from 'react';
import axios from 'axios';

function App() {
  const [username, setUsername] = useState('');
  const [content, setContent] = useState('');
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    axios.get('http://localhost:5000/api/posts').then(res => setPosts(res.data));
  }, []);

  const createPost = async () => {
    if (!username || !content) return;
    await axios.post('http://localhost:5000/api/posts', { author: username, content });
    const res = await axios.get('http://localhost:5000/api/posts');
    setPosts(res.data);
    setContent('');
  };

  const likePost = async (id) => {
    await axios.post(`http://localhost:5000/api/posts/${id}/like`);
    const res = await axios.get('http://localhost:5000/api/posts');
    setPosts(res.data);
  };

  return (
    <div className="max-w-md mx-auto p-4 min-h-screen bg-gray-100">
      <h1 className="text-2xl font-bold text-center mb-4 text-blue-600">Mini Facebook</h1>
      <input
        className="border p-2 w-full mb-2 rounded"
        placeholder="Your name"
        value={username}
        onChange={e => setUsername(e.target.value)}
      />
      <textarea
        className="border p-2 w-full mb-2 rounded"
        placeholder="What's on your mind?"
        value={content}
        onChange={e => setContent(e.target.value)}
      />
      <button
        onClick={createPost}
        className="bg-blue-500 text-white px-4 py-2 rounded w-full hover:bg-blue-600"
      >
        Post
      </button>

      <div className="mt-6 space-y-4">
        {posts.map(post => (
          <div key={post._id} className="bg-white border p-4 rounded shadow">
            <p className="font-bold text-blue-700">{post.author}</p>
            <p className="text-gray-800 mt-1">{post.content}</p>
            <div className="flex items-center mt-2">
              <button onClick={() => likePost(post._id)} className="text-red-600">❤️ Like</button>
              <span className="ml-2 text-sm text-gray-600">{post.likes} likes</span>
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}

export default App;
