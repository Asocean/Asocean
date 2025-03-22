- 👋 Hi, I’m @Asocean
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
Asocean/Asocean is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->

const express = require('express');
const mongoose = require('mongoose');
const jwt = require('jsonwebtoken');
const bcrypt = require('bcryptjs');

const app = express();
app.use(express.json());

mongoose.connect('mongodb://localhost:27017/yourdbname', { useNewUrlParser: true, useUnifiedTopology: true });

const UserSchema = new mongoose.Schema({
  email: String,
  password: String,
  phone: String
});

const User = mongoose.model('User', UserSchema);

app.post('/register', async (req, res) => {
  const { email, password, phone } = req.body;
  const hashedPassword = await bcrypt.hash(password, 10);
  const user = new User({ email, password: hashedPassword, phone });
  await user.save();
  const token = jwt.sign({ userId: user._id }, 'your_jwt_secret');
  res.json({ token });
});

app.post('/login', async (req, res) => {
  const { email, password } = req.body;
  const user = await User.findOne({ email });
  if (!user || !await bcrypt.compare(password, user.password)) {
    return res.status(400).send('Invalid credentials');
  }
  const token = jwt.sign({ userId: user._id }, 'your_jwt_secret');
  res.json({ token });
});

app.listen(3000, () => {
  console.log('Server started on port 3000');
});