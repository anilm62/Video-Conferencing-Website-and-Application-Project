document.getElementById('post-form').addEventListener('submit', async (e) => {
  e.preventDefault();
  
  const content = document.getElementById('post-content').value;
  const response = await fetch('/api/posts', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${localStorage.getItem('token')}`
    },
    body: JSON.stringify({ content })
  });
  
  if (response.ok) {
    const post = await response.json();
    addPostToFeed(post);
    e.target.reset();
  } else {
    alert('Error creating post');
  }
});

function addPostToFeed(post) {
  const feed = document.getElementById('posts-feed');
  const postElement = document.createElement('div');
  postElement.className = 'post';
  postElement.innerHTML = `
    <div class="post-header">
      <img src="${post.author.avatar}" class="avatar">
      <span class="username">${post.author.username}</span>
    </div>
    <div class="post-content">${post.content}</div>
    <div class="post-actions">
      <button class="like-btn">Like</button>
      <button class="comment-btn">Comment</button>
    </div>
  `;
  feed.prepend(postElement);
}
