## Backend Implementation Examples

**Express.js Route Example:**
```javascript
// Post routes
router.post('/posts', authMiddleware, async (req, res) => {
  try {
    const post = new Post({
      content: req.body.content,
      author: req.user.id
    });
    await post.save();
    res.status(201).json(post);
  } catch (err) {
    res.status(400).json({ error: err.message });
  }
});

// Follow route
router.post('/users/:id/follow', authMiddleware, async (req, res) => {
  if (req.params.id === req.user.id) {
    return res.status(400).json({ error: "Cannot follow yourself" });
  }
  
  const userToFollow = await User.findById(req.params.id);
  const currentUser = await User.findById(req.user.id);
  
  if (currentUser.following.includes(userToFollow._id)) {
    return res.status(400).json({ error: "Already following" });
  }
  
  currentUser.following.push(userToFollow._id);
  userToFollow.followers.push(currentUser._id);
  
  await Promise.all([currentUser.save(), userToFollow.save()]);
  
  res.json({ message: "Follow successful" });
});
