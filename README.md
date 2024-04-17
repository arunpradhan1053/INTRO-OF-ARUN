// app.js (or server.js)

const express = require('express');
const multer = require('multer'); // For handling file uploads
const path = require('path');
const app = express();

// Set up middleware
app.use(express.json()); // Parse JSON requests
app.use(express.urlencoded({ extended: true })); // Parse form data
app.use(express.static(path.join(__dirname, 'public'))); // Serve static files (e.g., videos)

// Set up storage for uploaded videos
const storage = multer.diskStorage({
    destination: (req, file, cb) => {
        cb(null, 'public/videos'); // Save videos in the 'public/videos' directory
    },
    filename: (req, file, cb) => {
        const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1e9);
        cb(null, file.fieldname + '-' + uniqueSuffix + path.extname(file.originalname));
    },
});
const upload = multer({ storage });

// Dummy user database (replace with a real database)
const users = [];

// Register a new user
app.post('/register', (req, res) => {
    const { username, email, password } = req.body;
    // Validate input (e.g., check if username or email already exists)
    // Hash the password (use bcrypt or a similar library)
    // Save user data to the database
    users.push({ username, email, password });
    res.status(201).json({ message: 'User registered successfully' });
});

// Handle video uploads
app.post('/upload', upload.single('videoFile'), (req, res) => {
    const { title, description } = req.body;
    const videoFile = req.file; // Contains information about the uploaded video
    // Save video metadata (title, description, filename) to the database
    // Respond with success message
    res.status(201).json({ message: 'Video uploaded successfully' });
});

// Serve video files
app.get('/videos/:filename', (req, res) => {
    const filename = req.params.filename;
    const videoPath = path.join(__dirname, 'public/videos', filename);
    res.sendFile(videoPath);
});

// Start the server
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
});
