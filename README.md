<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DEAR FUTURE - Scheduled Messenger</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div id="root"></div>
  <!-- React & ReactDOM -->
    <script src="https://unpkg.com/react@18/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
    <script src="https://unpkg.com/react-router-dom/umd/react-router-dom.min.js"></script>
    <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>

  <script type="text/babel" src="script.js"></script>
</body>
</html>
body {
    font-family: Arial, sans-serif;
    text-align: center;
    background-color: #f4f4f4;
    margin: 0;
    padding: 0;
}

.navbar {
    background-color: #FB6F92;
    padding: 1rem;
}

.navbar a {
    color: white;
    text-decoration: none;
    margin: 0 15px;
    font-size: 18px;
}

.container {
    margin: 50px auto;
    max-width: 800px;
    background: white;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
}

footer {
    margin-top: 20px;
    padding: 10px;
    background-color: #FB6F92;
    color: white;
}

/* Message Form */
.message-form {
    text-align: left;
}

.message-form label {
    display: block;
    margin-top: 10px;
    font-weight: bold;
}

.message-form input, 
.message-form textarea {
    width: 100%;
    padding: 10px;
    margin-top: 5px;
    border-radius: 5px;
    border: 1px solid #ccc;
}

.submit-btn {
    background-color: #FB6F92;
    color: white;
    padding: 10px 20px;
    border-radius: 5px;
    cursor: pointer;
}

.submit-btn:hover {
    background-color: #FF8FAB;
}
const { useState } = React;
const { BrowserRouter, Routes, Route, Link, useNavigate } = ReactRouterDOM;

function App() {
    return (
        <BrowserRouter>
            <div>
                <nav className="navbar">
                    <Link to="/">Home</Link>
                    <Link to="/dashboard">Dashboard</Link>
                    <Link to="/subscription">Subscription</Link>
                </nav>

  <Routes>
                    <Route path="/" element={<Home />} />
                    <Route path="/message/:type" element={<MessageForm />} />
                    <Route path="/subscription" element={<Subscription />} />
                    <Route path="/dashboard" element={<Dashboard />} />
                </Routes>

  <footer>&copy; 2025 DEAR FUTURE</footer>
            </div>
        </BrowserRouter>
    );
}

function Home() {
    const navigate = useNavigate();

return (
        <div className="container">
            <h2>Send Scheduled Messages</h2>
            <p>Connect with your loved ones at the perfect moment.</p>

  <button className="submit-btn" onClick={() => navigate("/message/text")}>
                Schedule a Text Message
            </button>
        </div>
    );
}

function MessageForm() {
    const [message, setMessage] = useState("");
    const [scheduledDate, setScheduledDate] = useState("");

  function handleSubmit(e) {
        e.preventDefault();
        alert(`Message Scheduled: "${message}" at ${scheduledDate}`);
    }

   return (
        <div className="container">
            <h2>Schedule a Message</h2>
            <form className="message-form" onSubmit={handleSubmit}>
                <label>Message:</label>
                <textarea value={message} onChange={(e) => setMessage(e.target.value)} required></textarea>

  <label>Scheduled Date & Time:</label>
                <input type="datetime-local" value={scheduledDate} onChange={(e) => setScheduledDate(e.target.value)} required />

  <button className="submit-btn" type="submit">Schedule</button>
            </form>
        </div>
    );
}

function Subscription() {
    return (
        <div className="container">
            <h2>Subscribe for Unlimited Messaging</h2>
            <p>Get unlimited access to scheduled messages.</p>
            <button className="submit-btn">Subscribe Now</button>
        </div>
    );
}

function Dashboard() {
    return (
        <div className="container">
            <h2>My Messages</h2>
            <p>View your scheduled messages here.</p>
        </div>
    );
}

// Render the app
ReactDOM.createRoot(document.getElementById("root")).render(<App />);
