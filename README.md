// App.jsx
import React, { useState, useEffect } from 'react';
import { BrowserRouter as Router, Routes, Route, Link } from 'react-router-dom';
import Home from './components/Home';
import MessageForm from './components/MessageForm';
import Subscription from './components/Subscription';
import UserDashboard from './components/UserDashboard';
import './App.css';

function App() {
  return (
    <Router>
      <div className="app">
        <header className="app-header">
          <h1>Scheduled Messenger</h1>
          <nav>
            <Link to="/">Home</Link>
            <Link to="/dashboard">My Messages</Link>
            <Link to="/subscription">Subscription</Link>
          </nav>
        </header>
        <main>
          <Routes>
            <Route path="/" element={<Home />} />
            <Route path="/message/:type" element={<MessageForm />} />
            <Route path="/subscription" element={<Subscription />} />
            <Route path="/dashboard" element={<UserDashboard />} />
          </Routes>
        </main>
        <footer>
          <p>&copy; 2025 Scheduled Messenger. All rights reserved.</p>
        </footer>
      </div>
    </Router>
  );
}

export default App;

// components/Home.jsx
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import './Home.css';

const Home = () => {
  const navigate = useNavigate();
  const [selectedType, setSelectedType] = useState('');
  const [isDropdownOpen, setIsDropdownOpen] = useState(false);

  const handleMessageTypeSelect = (type) => {
    setSelectedType(type);
    setIsDropdownOpen(false);
    navigate(`/message/${type}`);
  };

  return (
    <div className="home-container">
      <h2>Send Scheduled Messages</h2>
      <p>Connect with your loved ones at the perfect moment.</p>
      
  <div className="message-type-selector">
        <button 
          className="dropdown-button"
          onClick={() => setIsDropdownOpen(!isDropdownOpen)}
        >
          {selectedType || 'Select Message Type'} ▼
        </button>
        
  {isDropdownOpen && (
          <div className="dropdown-menu">
            <div className="dropdown-item" onClick={() => handleMessageTypeSelect('text')}>
              <h3>Text Message</h3>
              <p>First 10 messages free, then ₹1/message (India) or $0.3/message (International)</p>
            </div>
            <div className="dropdown-item" onClick={() => handleMessageTypeSelect('audio')}>
              <h3>Audio Message</h3>
              <p>First message free, then ₹2/message (India) or $0.5/message (International)</p>
            </div>
            <div className="dropdown-item" onClick={() => handleMessageTypeSelect('video')}>
              <h3>Video Message</h3>
              <p>First message free, then ₹5/message (India) or $0.7/message (International)</p>
            </div>
          </div>
        )}
      </div>

  <div className="features">
        <div className="feature">
          <h3>Schedule Messages</h3>
          <p>Plan your messages to arrive at the perfect time</p>
        </div>
        <div className="feature">
          <h3>Multiple Formats</h3>
          <p>Send text, audio, or video messages</p>
        </div>
        <div className="feature">
          <h3>Global Delivery</h3>
          <p>Reach anyone with an email or phone number</p>
        </div>
      </div>
    </div>
  );
};

export default Home;

// components/MessageForm.jsx
import React, { useState, useRef, useEffect } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import DatePicker from 'react-datepicker';
import "react-datepicker/dist/react-datepicker.css";
import './MessageForm.css';

const MessageForm = () => {
  const { type } = useParams();
  const navigate = useNavigate();
  const videoRef = useRef(null);
  const audioRef = useRef(null);
  const mediaRecorderRef = useRef(null);
  const [mediaChunks, setMediaChunks] = useState([]);
  
  const [messageCount, setMessageCount] = useState({
    text: 0,
    audio: 0,
    video: 0
  });
  const [subscriptionStatus, setSubscriptionStatus] = useState(false);
  
  const [formData, setFormData] = useState({
    senderName: '',
    senderAddress: '',
    senderPincode: '',
    senderContact: '',
    receiverName: '',
    receiverAddress: '',
    receiverPincode: '',
    receiverContact: '',
    messageText: '',
    scheduledDate: new Date(new Date().getTime() + 24 * 60 * 60 * 1000), // Tomorrow by default
    mediaFile: null,
    mediaUrl: null
  });
  
  const [country, setCountry] = useState('india');
  const [contactType, setContactType] = useState('email');
  const [isRecording, setIsRecording] = useState(false);
  const [validationErrors, setValidationErrors] = useState({});
  
  useEffect(() => {
    // Load message counts from local storage
    const savedCounts = JSON.parse(localStorage.getItem('messageCounts')) || { text: 0, audio: 0, video: 0 };
    setMessageCount(savedCounts);
    
   // Check subscription status
    const subscription = JSON.parse(localStorage.getItem('subscription')) || false;
    setSubscriptionStatus(subscription);
    
  // Clean up media streams when component unmounts
    return () => {
      if (videoRef.current && videoRef.current.srcObject) {
        const tracks = videoRef.current.srcObject.getTracks();
        tracks.forEach(track => track.stop());
      }
      if (audioRef.current && audioRef.current.srcObject) {
        const tracks = audioRef.current.srcObject.getTracks();
        tracks.forEach(track => track.stop());
      }
    };
  }, []);
  
  const checkMessageLimit = () => {
    // Check if user has reached the free message limit
    if (
      (type === 'text' && messageCount.text >= 10) ||
      (type === 'audio' && messageCount.audio >= 1) ||
      (type === 'video' && messageCount.video >= 1)
    ) {
      if (!subscriptionStatus) {
        alert('You have used all your free messages. Please subscribe to continue.');
        navigate('/subscription');
        return false;
      }
    }
    return true;
  };
  
  const handleInputChange = (e) => {
    const { name, value } = e.target;
    setFormData({
      ...formData,
      [name]: value
    });
    
   // Clear validation error when field is updated
    if (validationErrors[name]) {
      setValidationErrors({
        ...validationErrors,
        [name]: null
      });
    }
  };
  
  const handleCountryChange = (e) => {
    setCountry(e.target.value);
  };
  
  const handleContactTypeChange = (e) => {
    setContactType(e.target.value);
  };
  
  const handleDateChange = (date) => {
    setFormData({
      ...formData,
      scheduledDate: date
    });
  };
  
  const startRecording = async () => {
    if (!checkMessageLimit()) return;
    
  setMediaChunks([]);
    
  try {
      let stream;
      if (type === 'video') {
        stream = await navigator.mediaDevices.getUserMedia({ video: true, audio: true });
        if (videoRef.current) {
          videoRef.current.srcObject = stream;
        }
      } else if (type === 'audio') {
        stream = await navigator.mediaDevices.getUserMedia({ audio: true });
        if (audioRef.current) {
          audioRef.current.srcObject = stream;
        }
      }
      
const mediaRecorder = new MediaRecorder(stream);
      mediaRecorderRef.current = mediaRecorder;
      
   mediaRecorder.ondataavailable = (e) => {
        if (e.data.size > 0) {
          setMediaChunks(prev => [...prev, e.data]);
        }
      };
      
  mediaRecorder.onstop = () => {
        const blob = new Blob(mediaChunks, { type: type === 'video' ? 'video/webm' : 'audio/webm' });
        const url = URL.createObjectURL(blob);
        
  setFormData({
          ...formData,
          mediaFile: blob,
          mediaUrl: url
        });
        
  // Stop all tracks
        stream.getTracks().forEach(track => track.stop());
      };
      
   mediaRecorder.start();
      setIsRecording(true);
    } catch (error) {
      console.error('Error accessing media devices:', error);
      alert('Could not access camera/microphone. Please check permissions.');
    }
  };
  
  const stopRecording = () => {
    if (mediaRecorderRef.current && isRecording) {
      mediaRecorderRef.current.stop();
      setIsRecording(false);
    }
  };
  
  const handleFileUpload = (e) => {
    if (!checkMessageLimit()) return;
    
const file = e.target.files[0];
    if (!file) return;
    
  // Validate file type
    if (type === 'video' && !file.type.includes('video/')) {
      alert('Please upload a valid video file');
      return;
    } else if (type === 'audio' && !file.type.includes('audio/')) {
      alert('Please upload a valid audio file');
      return;
    }
    
  const url = URL.createObjectURL(file);
    setFormData({
      ...formData,
      mediaFile: file,
      mediaUrl: url
    });
  };
  
  const validateForm = () => {
    const errors = {};
    
  // Required fields validation
    if (!formData.senderName) errors.senderName = 'Sender name is required';
    if (!formData.senderAddress) errors.senderAddress = 'Sender address is required';
    if (!formData.senderPincode) errors.senderPincode = 'Sender pincode is required';
    if (!formData.senderContact) errors.senderContact = 'Sender contact is required';
    if (!formData.receiverName) errors.receiverName = 'Receiver name is required';
    if (!formData.receiverAddress) errors.receiverAddress = 'Receiver address is required';
    if (!formData.receiverPincode) errors.receiverPincode = 'Receiver pincode is required';
    if (!formData.receiverContact) errors.receiverContact = 'Receiver contact is required';
    
  // Type-specific validation
    if (type === 'text') {
      if (!formData.messageText) {
        errors.messageText = 'Message text is required';
      } else if (formData.messageText.length < 10) {
        errors.messageText = 'Message must be at least 10 characters';
      }
    } else if ((type === 'audio' || type === 'video') && !formData.mediaFile) {
      errors.mediaFile = `${type.charAt(0).toUpperCase() + type.slice(1)} file is required`;
    }
    
  // Contact validation (simple patterns)
    if (contactType === 'email') {
      const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      if (!emailPattern.test(formData.senderContact)) {
        errors.senderContact = 'Please enter a valid email address';
      }
      if (!emailPattern.test(formData.receiverContact)) {
        errors.receiverContact = 'Please enter a valid email address';
      }
    } else {
      // Phone validation (simple check)
      if (!/^\d{10,15}$/.test(formData.senderContact)) {
        errors.senderContact = 'Please enter a valid phone number';
      }
      if (!/^\d{10,15}$/.test(formData.receiverContact)) {
        errors.receiverContact = 'Please enter a valid phone number';
      }
    }
    
  setValidationErrors(errors);
    return Object.keys(errors).length === 0;
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    
  if (!checkMessageLimit()) return;
    
  if (!validateForm()) {
      return;
    }
    
  // Update message count
    const newCounts = {
      ...messageCount,
      [type]: messageCount[type] + 1
    };
    setMessageCount(newCounts);
    localStorage.setItem('messageCounts', JSON.stringify(newCounts));
    
   // Store message in local storage (in a real app this would go to a server)
    const messages = JSON.parse(localStorage.getItem('scheduledMessages')) || [];
    
  // For demo purposes, we'll store media as a URL. In a real app, you'd upload to server
    const messageData = {
      id: Date.now().toString(),
      type,
      ...formData,
      mediaUrl: formData.mediaUrl, // This would be a server URL in production
      scheduledDate: formData.scheduledDate.toISOString(),
      status: 'scheduled',
      createdAt: new Date().toISOString()
    };
    
   messages.push(messageData);
    localStorage.setItem('scheduledMessages', JSON.stringify(messages));
    
  alert('Your message has been scheduled successfully!');
    navigate('/dashboard');
  };
  
  const getTitle = () => {
    switch(type) {
      case 'text': return 'Schedule Text Message';
      case 'audio': return 'Schedule Audio Message';
      case 'video': return 'Schedule Video Message';
      default: return 'Schedule Message';
    }
  };
  
  return (
    <div className="message-form-container">
      <h2>{getTitle()}</h2>
      
  {!checkMessageLimit() ? (
        <div className="subscription-alert">
          <p>You have used all your free messages. Please subscribe to continue.</p>
          <button onClick={() => navigate('/subscription')}>Go to Subscription</button>
        </div>
      ) : (
        <form onSubmit={handleSubmit}>
          <div className="form-section">
            <h3>Sender Information</h3>
            
   <div className="form-group">
              <label htmlFor="senderName">Name:</label>
              <input
                type="text"
                id="senderName"
                name="senderName"
                value={formData.senderName}
                onChange={handleInputChange}
                className={validationErrors.senderName ? 'error' : ''}
              />
              {validationErrors.senderName && <p className="error-text">{validationErrors.senderName}</p>}
            </div>
            
  <div className="form-group">
              <label htmlFor="senderAddress">Address:</label>
              <textarea
                id="senderAddress"
                name="senderAddress"
                value={formData.senderAddress}
                onChange={handleInputChange}
                className={validationErrors.senderAddress ? 'error' : ''}
              />
              {validationErrors.senderAddress && <p className="error-text">{validationErrors.senderAddress}</p>}
            </div>
            
  <div className="form-group">
              <label htmlFor="senderPincode">Pincode:</label>
              <input
                type="text"
                id="senderPincode"
                name="senderPincode"
                value={formData.senderPincode}
                onChange={handleInputChange}
                className={validationErrors.senderPincode ? 'error' : ''}
              />
              {validationErrors.senderPincode && <p className="error-text">{validationErrors.senderPincode}</p>}
            </div>
            
  <div className="form-row">
              <div className="form-group">
                <label htmlFor="country">Country:</label>
                <select id="country" value={country} onChange={handleCountryChange}>
                  <option value="india">India</option>
                  <option value="international">International</option>
                </select>
              </div>
              
  <div className="form-group">
                <label htmlFor="contactType">Contact Type:</label>
                <select id="contactType" value={contactType} onChange={handleContactTypeChange}>
                  <option value="email">Email</option>
                  <option value="phone">Phone</option>
                </select>
              </div>
            </div>
            
  <div className="form-group">
              <label htmlFor="senderContact">
                {contactType === 'email' ? 'Email:' : 'Phone Number:'}
              </label>
              <input
                type={contactType === 'email' ? 'email' : 'tel'}
                id="senderContact"
                name="senderContact"
                value={formData.senderContact}
                onChange={handleInputChange}
                className={validationErrors.senderContact ? 'error' : ''}
              />
              {validationErrors.senderContact && <p className="error-text">{validationErrors.senderContact}</p>}
            </div>
          </div>
          
   <div className="form-section">
            <h3>Receiver Information</h3>
            
  <div className="form-group">
              <label htmlFor="receiverName">Name:</label>
              <input
                type="text"
                id="receiverName"
                name="receiverName"
                value={formData.receiverName}
                onChange={handleInputChange}
                className={validationErrors.receiverName ? 'error' : ''}
              />
              {validationErrors.receiverName && <p className="error-text">{validationErrors.receiverName}</p>}
            </div>
            
  <div className="form-group">
              <label htmlFor="receiverAddress">Address:</label>
              <textarea
                id="receiverAddress"
                name="receiverAddress"
                value={formData.receiverAddress}
                onChange={handleInputChange}
                className={validationErrors.receiverAddress ? 'error' : ''}
              />
              {validationErrors.receiverAddress && <p className="error-text">{validationErrors.receiverAddress}</p>}
            </div>
            
  <div className="form-group">
              <label htmlFor="receiverPincode">Pincode:</label>
              <input
                type="text"
                id="receiverPincode"
                name="receiverPincode"
                value={formData.receiverPincode}
                onChange={handleInputChange}
                className={validationErrors.receiverPincode ? 'error' : ''}
              />
              {validationErrors.receiverPincode && <p className="error-text">{validationErrors.receiverPincode}</p>}
            </div>
            
 <div className="form-group">
              <label htmlFor="receiverContact">
                {contactType === 'email' ? 'Email:' : 'Phone Number:'}
              </label>
              <input
                type={contactType === 'email' ? 'email' : 'tel'}
                id="receiverContact"
                name="receiverContact"
                value={formData.receiverContact}
                onChange={handleInputChange}
                className={validationErrors.receiverContact ? 'error' : ''}
              />
              {validationErrors.receiverContact && <p className="error-text">{validationErrors.receiverContact}</p>}
            </div>
          </div>
         
  <div className="form-section">
            <h3>Message Details</h3>
            
  {type === 'text' && (
              <div className="form-group">
                <label htmlFor="messageText">Message (min 10 characters):</label>
                <textarea
                  id="messageText"
                  name="messageText"
                  value={formData.messageText}
                  onChange={handleInputChange}
                  rows="5"
                  className={validationErrors.messageText ? 'error' : ''}
                />
                {validationErrors.messageText && <p className="error-text">{validationErrors.messageText}</p>}
                <div className="character-count">
                  {formData.messageText.length} characters (minimum 10)
                </div>
              </div>
            )}
            
  {type === 'audio' && (
              <div className="media-section">
                <h4>Audio Message</h4>
                <div className="media-controls">
                  <div className="record-controls">
                    {!isRecording ? (
                      <button type="button" onClick={startRecording}>Start Recording</button>
                    ) : (
                      <button type="button" onClick={stopRecording}>Stop Recording</button>
                    )}
                  </div>
                  
   <div className="upload-controls">
                    <label className="file-upload-btn">
                      Upload Audio
                      <input
                        type="file"
                        accept="audio/*"
                        onChange={handleFileUpload}
                        style={{ display: 'none' }}
                      />
                    </label>
                  </div>
                </div>
                
  <audio ref={audioRef} controls={isRecording} className="audio-preview"></audio>
                
  {formData.mediaUrl && !isRecording && (
                  <div className="media-preview">
                    <h5>Recorded Audio:</h5>
                    <audio src={formData.mediaUrl} controls></audio>
                  </div>
                )}
                
   {validationErrors.mediaFile && <p className="error-text">{validationErrors.mediaFile}</p>}
              </div>
            )}
            
  {type === 'video' && (
              <div className="media-section">
                <h4>Video Message</h4>
                <div className="media-controls">
                  <div className="record-controls">
                    {!isRecording ? (
                      <button type="button" onClick={startRecording}>Start Recording</button>
                    ) : (
                      <button type="button" onClick={stopRecording}>Stop Recording</button>
                    )}
                  </div>
                  
  <div className="upload-controls">
                    <label className="file-upload-btn">
                      Upload Video
                      <input
                        type="file"
                        accept="video/*"
                        onChange={handleFileUpload}
                        style={{ display: 'none' }}
                      />
                    </label>
                  </div>
                </div>
                
  {isRecording && (
                  <div className="recording-preview">
                    <h5>Recording Preview:</h5>
                    <video ref={videoRef} autoPlay muted></video>
                  </div>
                )}
                
  {formData.mediaUrl && !isRecording && (
                  <div className="media-preview">
                    <h5>Recorded Video:</h5>
                    <video src={formData.mediaUrl} controls></video>
                  </div>
                )}
                
  {validationErrors.mediaFile && <p className="error-text">{validationErrors.mediaFile}</p>}
              </div>
            )}
            
  <div className="form-group">
              <label htmlFor="scheduledDate">Scheduled Date and Time:</label>
              <DatePicker
                selected={formData.scheduledDate}
                onChange={handleDateChange}
                showTimeSelect
                timeFormat="HH:mm"
                timeIntervals={15}
                timeCaption="time"
                dateFormat="MMMM d, yyyy h:mm aa"
                minDate={new Date()}
                className={validationErrors.scheduledDate ? 'error' : ''}
              />
              {validationErrors.scheduledDate && <p className="error-text">{validationErrors.scheduledDate}</p>}
            </div>
          </div>
          
  <div className="pricing-info">
            {type === 'text' && (
              <p>Text Message Pricing: First 10 messages free, then {country === 'india' ? '₹1' : '$0.3'} per message</p>
            )}
            {type === 'audio' && (
              <p>Audio Message Pricing: First message free, then {country === 'india' ? '₹2' : '$0.5'} per message</p>
            )}
            {type === 'video' && (
              <p>Video Message Pricing: First message free, then {country === 'india' ? '₹5' : '$0.7'} per message</p>
            )}
            <p>Messages sent so far: {messageCount[type]}</p>
          </div>
          
  <div className="form-actions">
            <button type="submit" className="submit-btn">Schedule Message</button>
            <button type="button" className="cancel-btn" onClick={() => navigate('/')}>Cancel</button>
          </div>
        </form>
      )}
    </div>
  );
};

export default MessageForm;
// components/Subscription.jsx
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import './Subscription.css';

const Subscription = () => {
  const navigate = useNavigate();
  const [country, setCountry] = useState('india');
  const [plan, setPlan] = useState('monthly');
  const [paymentMethod, setPaymentMethod] = useState('card');
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    cardNumber: '',
    expiryDate: '',
    cvv: '',
    upiId: '',
  });

  const handleCountryChange = (e) => {
    setCountry(e.target.value);
  };

  const handlePlanChange = (e) => {
    setPlan(e.target.value);
  };

  const handlePaymentMethodChange = (e) => {
    setPaymentMethod(e.target.value);
  };

  const handleInputChange = (e) => {
    const { name, value } = e.target;
    setFormData({
      ...formData,
      [name]: value
    });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    // In a real app, this would process the payment
    // For demo purposes, we'll just set subscription to true in localStorage
    localStorage.setItem('subscription', JSON.stringify(true));
    alert('Subscription successful! You now have access to all messaging features.');
    navigate('/');
  };

  const getPlanPrice = () => {
    if (country === 'india') {
      return plan === 'monthly' ? '₹99' : '₹999';
    } else {
      return plan === 'monthly' ? '$4.99' : '$49.99';
    }
  };

  return (
    <div className="subscription-container">
      <h2>Subscribe to Unlimited Messaging</h2>
      
  <div className="subscription-benefits">
        <h3>Benefits of Subscription:</h3>
        <ul>
          <li>Unlimited text messages (regular price: {country === 'india' ? '₹1' : '$0.3'} each)</li>
          <li>Unlimited audio messages (regular price: {country === 'india' ? '₹2' : '$0.5'} each)</li>
          <li>Unlimited video messages (regular price: {country === 'india' ? '₹5' : '$0.7'} each)</li>
          <li>Priority message delivery</li>
          <li>Advanced scheduling options</li>
        </ul>
      </div>
      
  <div className="subscription-options">
        <div className="location-selection">
          <h3>Location:</h3>
          <div className="radio-group">
            <label>
              <input 
                type="radio" 
                name="country" 
                value="india" 
                checked={country === 'india'} 
                onChange={handleCountryChange} 
              />
              India
            </label>
            <label>
              <input 
                type="radio" 
                name="country" 
                value="international" 
                checked={country === 'international'} 
                onChange={handleCountryChange} 
              />
              International
            </label>
          </div>
        </div>
        
  <div className="plan-selection">
          <h3>Select Plan:</h3>
          <div className="radio-group">
            <label>
              <input 
                type="radio" 
                name="plan" 
                value="monthly" 
                checked={plan === 'monthly'} 
                onChange={handlePlanChange} 
              />
              Monthly ({country === 'india' ? '₹99' : '$4.99'})
            </label>
            <label>
              <input 
                type="radio" 
                name="plan" 
                value="yearly" 
                checked={plan === 'yearly'} 
                onChange={handlePlanChange} 
              />
              Yearly ({country === 'india' ? '₹999' : '$49.99'}) <span className="savings">Save 16%</span>
            </label>
          </div>
        </div>
      </div>
      
  <div className="payment-section">
        <h3>Payment Details</h3>
        
   <div className="payment-method-selection">
          <div className="radio-group">
            <label>
              <input 
                type="radio" 
                name="paymentMethod" 
                value="card" 
                checked={paymentMethod === 'card'} 
                onChange={handlePaymentMethodChange} 
              />
              Credit/Debit Card
            </label>
            {country === 'india' && (
              <label>
                <input 
                  type="radio" 
                  name="paymentMethod" 
                  value="upi" 
                  checked={paymentMethod === 'upi'} 
                  onChange={handlePaymentMethodChange} 
                />
                UPI
              </label>
            )}
          </div>
        </div>
        
   <form onSubmit={handleSubmit}>
          <div className="form-group">
            <label htmlFor="name">Full Name:</label>
            <input
              type="text"
              id="name"
              name="name"
              value={formData.name}
              onChange={handleInputChange}
              required
            />
          </div>
          
  <div className="form-group">
            <label htmlFor="email">Email:</label>
            <input
              type="email"
              id="email"
              name="email"
              value={formData.email}
              onChange={handleInputChange}
              required
            />
          </div>
          
   {paymentMethod === 'card' && (
            <>
              <div className="form-group">
                <label htmlFor="cardNumber">Card Number:</label>
                <input
                  type="text"
                  id="cardNumber"
                  name="cardNumber"
                  value={formData.cardNumber}
                  onChange={handleInputChange}
                  placeholder="XXXX XXXX XXXX XXXX"
                  required
                />
              </div>
              
   <div className="form-row">
                <div className="form-group half">
                  <label htmlFor="expiryDate">Expiry Date:</label>
                  <input
                    type="text"
                    id="expiryDate"
                    name="expiryDate"
                    value={formData.expiryDate}
                    onChange={handleInputChange}
                    placeholder="MM/YY"
                    required
                  />
                </div>
                
  <div className="form-group half">
                  <label htmlFor="cvv">CVV:</label>
                  <input
                    type="text"
                    id="cvv"
                    name="cvv"
                    value={formData.cvv}
                    onChange={handleInputChange}
                    placeholder="XXX"
                    required
                  />
                </div>
              </div>
            </>
          )}
          
   {paymentMethod === 'upi' && (
            <div className="form-group">
              <label htmlFor="upiId">UPI ID:</label>
              <input
                type="text"
                id="upiId"
                name="upiId"
                value={formData.upiId}
                onChange={handleInputChange}
                placeholder="username@upi"
                required
              />
            </div>
          )}
          
  <div className="subscription-summary">
            <h4>Summary:</h4>
            <p>Plan: {plan === 'monthly' ? 'Monthly' : 'Yearly'}</p>
            <p>Price: {getPlanPrice()}</p>
            <p>Location: {country === 'india' ? 'India' : 'International'}</p>
          </div>
          
  <div className="form-actions">
            <button type="submit" className="submit-btn">Subscribe Now</button>
            <button type="button" className="cancel-btn" onClick={() => navigate('/')}>Cancel</button>
          </div>
        </form>
      </div>
    </div>
  );
};

export default Subscription;
import React, { useState, useEffect } from 'react';
import { useNavigate } from 'react-router-dom';
import './UserDashboard.css';

const UserDashboard = () => {
  const navigate = useNavigate();
  const [messages, setMessages] = useState([]);
  const [activeTab, setActiveTab] = useState('scheduled');
  const [subscriptionStatus, setSubscriptionStatus] = useState(false);
  const [messageCount, setMessageCount] = useState({
    text: 0,
    audio: 0,
    video: 0
  });

  useEffect(() => {
    // Load messages from local storage
    const savedMessages = JSON.parse(localStorage.getItem('scheduledMessages')) || [];
    setMessages(savedMessages);
    
    // Check subscription status
    const subscription = JSON.parse(localStorage.getItem('subscription')) || false;
    setSubscriptionStatus(subscription);
    
    // Load message counts
    const savedCounts = JSON.parse(localStorage.getItem('messageCounts')) || { text: 0, audio: 0, video: 0 };
    setMessageCount(savedCounts);
    
    // Simulate message delivery for demo purposes
    // In a real app, this would be handled by a server
    const interval = setInterval(() => {
      const now = new Date();
      const updatedMessages = savedMessages.map(message => {
        if (message.status === 'scheduled' && new Date(message.scheduledDate) < now) {
          return { ...message, status: 'delivered' };
        }
        return message;
      });
      
      if (JSON.stringify(updatedMessages) !== JSON.stringify(savedMessages)) {
        setMessages(updatedMessages);
        localStorage.setItem('scheduledMessages', JSON.stringify(updatedMessages));
      }
    }, 5000);
    
    return () => clearInterval(interval);
  }, []);

  const filteredMessages = messages.filter(message => {
    if (activeTab === 'scheduled') {
      return message.status === 'scheduled';
    } else if (activeTab === 'delivered') {
      return message.status === 'delivered';
    }
    return true;
  });

  const handleDeleteMessage = (id) => {
    const confirmDelete = window.confirm('Are you sure you want to delete this message?');
    if (confirmDelete) {
      const updatedMessages = messages.filter(message => message.id !== id);
      setMessages(updatedMessages);
      localStorage.setItem('scheduledMessages', JSON.stringify(updatedMessages));
    }
  };

  const formatDate = (dateString) => {
    const date = new Date(dateString);
    return date.toLocaleString();
  };

  const getTypeIcon = (type) => {
    switch(type) {
      case 'text': return '📝';
      case 'audio': return '🔊';
      case 'video': return '📹';
      default: return '📄';
    }
  };

  return (
    <div className="dashboard-container">
      <div className="dashboard-header">
        <h2>My Messages</h2>
        <button className="new-message-btn" onClick={() => navigate('/')}>
          New Message
        </button>
      </div>
      
  <div className="subscription-status">
        <div className="status-indicator">
          Status: <span className={subscriptionStatus ? 'status-active' : 'status-inactive'}>
            {subscriptionStatus ? 'Subscribed' : 'Free Plan'}
          </span>
        </div>
        
  {!subscriptionStatus && (
          <button className="subscribe-btn" onClick={() => navigate('/subscription')}>
            Upgrade to Premium
          </button>
        )}
      </div>
      
<div className="message-count-summary">
        <div className="count-item">
          <span className="count-label">Text Messages:</span>
          <span className="count-value">{messageCount.text}</span>
        </div>
        <div className="count-item">
          <span className="count-label">Audio Messages:</span>
          <span className="count-value">{messageCount.audio}</span>
        </div>
        <div className="count-item">
          <span className="count-label">Video Messages:</span>
          <span className="count-value">{messageCount.video}</span>
        </div>
      </div>
      
<div className="dashboard-tabs">
        <button 
          className={activeTab === 'all' ? 'tab-active' : ''}
          onClick={() => setActiveTab('all')}
        >
          All Messages
        </button>
        <button 
          className={activeTab === 'scheduled' ? 'tab-active' : ''}
          onClick={() => setActiveTab('scheduled')}
        >
          Scheduled
        </button>
        <button 
          className={activeTab === 'delivered' ? 'tab-active' : ''}
          onClick={() => setActiveTab('delivered')}
        >
          Delivered
        </button>
      </div>
      
   <div className="messages-list">
        {filteredMessages.length === 0 ? (
          <div className="no-messages">
            <p>No messages found.</p>
            {activeTab === 'scheduled' && (
              <button onClick={() => navigate('/')}>Schedule a new message</button>
            )}
          </div>
        ) : (
          filteredMessages.map(message => (
            <div key={message.id} className={`message-card ${message.status}`}>
              <div className="message-header">
                <div className="message-type">
                  <span className="type-icon">{getTypeIcon(message.type)}</span>
                  <span className="type-text">{message.type.charAt(0).toUpperCase() + message.type.slice(1)} Message</span>
                </div>
                <div className="message-status">
                  {message.status === 'scheduled' ? '⏳ Scheduled' : '✅ Delivered'}
                </div>
              </div>
              
   <div className="message-recipient">
                <strong>To:</strong> {message.receiverName}
              </div>
              
  <div className="message-content">
                {message.type === 'text' && (
                  <div className="text-content">
                    {message.messageText}
                  </div>
                )}
                
  {message.type === 'audio' && message.mediaUrl && (
                  <div className="audio-content">
                    <audio src={message.mediaUrl} controls></audio>
                  </div>
                )}
                
  {message.type === 'video' && message.mediaUrl && (
                  <div className="video-content">
                    <video src={message.mediaUrl} controls width="100%"></video>
                  </div>
                )}
              </div>
              
  <div className="message-schedule">
                <div>
                  <strong>Scheduled for:</strong> {formatDate(message.scheduledDate)}
                </div>
                <div>
                  <strong>Created:</strong> {formatDate(message.createdAt)}
                </div>
              </div>
              
 <div className="message-actions">
                {message.status === 'scheduled' && (
                  <button className="delete-btn" onClick={() => handleDeleteMessage(message.id)}>
                    Delete
                  </button>
                )}
              </div>
            </div>
          ))
        )}
      </div>
    </div>
  );
};

export default UserDashboard;
:root {
  --color-primary: #FB6F92;
  --color-primary-light: #FF8FAB;
  --color-primary-lighter: #FFB3C6;
  --color-primary-lightest: #FFC2D1;
  --color-background: #FFE5EC;
  --color-text: #333;
  --color-text-light: #666;
  --color-white: #fff;
  --color-error: #d32f2f;
  --color-success: #43a047;
  --color-border: #ddd;
  --border-radius: 8px;
  --box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  --transition-speed: 0.3s;
}

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Roboto', 'Segoe UI', sans-serif;
  background-color: var(--color-background);
  color: var(--color-text);
  line-height: 1.6;
}

.app {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.app-header {
  background-color: var(--color-primary);
  color: var(--color-white);
  padding: 1rem 2rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.app-header h1 {
  font-size: 1.5rem;
  margin: 0;
}

.app-header nav {
  display: flex;
  gap: 1.5rem;
}

.app-header a {
  color: var(--color-white);
  text-decoration: none;
  padding: 0.5rem 0;
  position: relative;
  transition: all var(--transition-speed);
}

.app-header a:hover {
  opacity: 0.9;
}

.app-header a::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  width: 0;
  height: 2px;
  background-color: var(--color-white);
  transition: width var(--transition-speed);
}

.app-header a:hover::after {
  width: 100%;
}

main {
  flex: 1;
  padding: 2rem;
  max-width: 1200px;
  margin: 0 auto;
  width: 100%;
}

footer {
  background-color: var(--color-primary-lightest);
  color: var(--color-text);
  text-align: center;
  padding: 1rem;
  margin-top: 2rem;
}

button {
  cursor: pointer;
  border: none;
  border-radius: var(--border-radius);
  padding: 0.5rem 1rem;
  font-weight: 500;
  transition: all var(--transition-speed);
}

input, select, textarea {
  border: 1px solid var(--color-border);
  border-radius: var(--border-radius);
  padding: 0.75rem;
  width: 100%;
  font-size: 1rem;
  transition: border-color var(--transition-speed);
}

input:focus, select:focus, textarea:focus {
  outline: none;
  border-color: var(--color-primary);
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: 500;
}

.error {
  border-color: var(--color-error) !important;
}

.error-text {
  color: var(--color-error);
  font-size: 0.85rem;
  margin-top: 0.25rem;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

/* Home.css */
.home-container {
  max-width: 800px;
  margin: 0 auto;
  animation: fadeIn 0.6s ease-out;
}

.home-container h2 {
  font-size: 2rem;
  margin-bottom: 1rem;
  color: var(--color-primary);
  text-align: center;
}

.home-container p {
  text-align: center;
  margin-bottom: 2rem;
  font-size: 1.1rem;
}

.message-type-selector {
  position: relative;
  margin: 2rem 0;
}

.dropdown-button {
  background-color: var(--color-white);
  color: var(--color-text);
  width: 100%;
  padding: 1rem;
  text-align: left;
  border: 2px solid var(--color-primary-light);
  border-radius: var(--border-radius);
  font-size: 1.1rem;
  box-shadow: var(--box-shadow);
  transition: all var(--transition-speed);
}

.dropdown-button:hover {
  background-color: var(--color-primary-lightest);
}

.dropdown-menu {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  background-color: var(--color-white);
  border-radius: var(--border-radius);
  box-shadow: var(--box-shadow);
  margin-top: 0.5rem;
  z-index: 10;
  animation: fadeIn 0.3s ease-out;
}

.dropdown-item {
  padding: 1rem;
  border-bottom: 1px solid var(--color-border);
  cursor: pointer;
  transition: background-color var(--transition-speed);
}

.dropdown-item:last-child {
  border-bottom: none;
}

.dropdown-item:hover {
  background-color: var(--color-primary-lightest);
}

.dropdown-item h3 {
  margin-bottom: 0.5rem;
  color: var(--color-primary);
}

.dropdown-item p {
  font-size: 0.9rem;
  margin: 0;
  text-align: left;
  color: var(--color-text-light);
}

.features {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 1.5rem;
  margin-top: 3rem;
}

.feature {
  background-color: var(--color-white);
  padding: 1.5rem;
  border-radius: var(--border-radius);
  box-shadow: var(--box-shadow);
  text-align: center;
  transition: transform var(--transition-speed);
}

.feature:hover {
  transform: translateY(-5px);
}

.feature h3 {
  color: var(--color-primary);
  margin-bottom: 0.75rem;
}

/* MessageForm.css */
.message-form-container {
  max-width: 800px;
  margin: 0 auto;
  background-color: var(--color-white);
  border-radius: var(--border-radius);
  padding: 2rem;
  box-shadow: var(--box-shadow);
  animation: fadeIn 0.6s ease-out;
}

.message-form-container h2 {
  color: var(--color-primary);
  margin-bottom: 1.5rem;
  text-align: center;
}

.form-section {
  margin-bottom: 2rem;
  padding-bottom: 2rem;
  border-bottom: 1px solid var(--color-border);
}

.form-section:last-child {
  border-bottom: none;
  margin-bottom: 1rem;
}

.form-section h3 {
  color: var(--color-primary);
  margin-bottom: 1.5rem;
  padding-bottom: 0.5rem;
  border-bottom: 1px solid var(--color-primary-lightest);
}

.form-row {
  display: flex;
  gap: 1rem;
}

.form-group.half {
  flex: 1;
}

.character-count {
  font-size: 0.85rem;
  color: var(--color-text-light);
  text-align: right;
  margin-top: 0.25rem;
}

.media-section {
  margin-bottom: 2rem;
}

.media-section h4 {
  margin-bottom: 1rem;
}

.media-controls {
  display: flex;
  gap: 1rem;
  margin-bottom: 1rem;
}

.record-controls button,
.file-upload-btn {
  background-color: var(--color-primary);
  color: var(--color-white);
  padding: 0.75rem 1rem;
  border-radius: var(--border-radius);
  cursor: pointer;
  transition: background-color var(--transition-speed);
  display: inline-block;
  text-align: center;
}

.record-controls button:hover,
.file-upload-btn:hover {
  background-color: var(--color-primary-light);
}

.recording-preview,
.media-preview {
  margin: 1rem 0;
}

.recording-preview h5,
.media-preview h5 {
  margin-bottom: 0.5rem;
}

.audio-preview,
.media-preview audio,
.media-preview video {
  width: 100%;
  border-radius: var(--border-radius);
  background-color: var(--color-primary-lightest);
}

.pricing-info {
  background-color: var(--color-primary-lightest);
  border-radius: var(--border-radius);
  padding: 1rem;
  margin-bottom: 2rem;
}

.pricing-info p {
  margin: 0.5rem 0;
}

.form-actions {
  display: flex;
  gap: 1rem;
}

.submit-btn {
  background-color: var(--color-primary);
  color: var(--color-white);
  padding: 0.75rem 1.5rem;
  flex: 2;
}

.submit-btn:hover {
  background-color: var(--color-primary-light);
}

.cancel-btn {
  background-color: var(--color-text-light);
  color: var(--color-white);
  padding: 0.75rem 1.5rem;
  flex: 1;
}

.cancel-btn:hover {
  background-color: #999;
}

.subscription-alert {
  text-align: center;
  padding: 2rem;
}

.subscription-alert p {
  margin-bottom: 1.5rem;
  font-size: 1.1rem;
}

.subscription-alert button {
  background-color: var(--color-primary);
  color: var(--color-white);
  padding: 0.75rem 1.5rem;
  border-radius: var(--border-radius);
}

.subscription-alert button:hover {
  background-color: var(--color-primary-light);
}

/* Subscription.css */
.subscription-container {
  max-width: 800px;
  margin: 0 auto;
  background-color: var(--color-white);
  border-radius: var(--border-radius);
  padding: 2rem;
  box-shadow: var(--box-shadow);
  animation: fadeIn 0.6s ease-out;
}

.subscription-container h2 {
  color: var(--color-primary);
  margin-bottom: 1.5rem;
  text-align: center;
}

.subscription-benefits {
  margin-bottom: 2rem;
  padding: 1.5rem;
  background-color: var(--color-primary-lightest);
  border-radius: var(--border-radius);
}

.subscription-benefits h3 {
  margin-bottom: 1rem;
  color: var(--color-primary);
}

.subscription-benefits ul {
  padding-left: 1.5rem;
}

.subscription-benefits li {
  margin-bottom: 0.5rem;
}

.subscription-options {
  display: flex;
  gap: 2rem;
  margin-bottom: 2rem;
}

.location-selection,
.plan-selection {
  flex: 1;
}

.location-selection h3,
.plan-selection h3 {
  margin-bottom: 1rem;
  color: var(--color-primary);
}

.radio-group {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.radio-group label {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  cursor: pointer;
}

.radio-group input[type="radio"] {
  width: auto;
  margin-right: 0.5rem;
  cursor: pointer;
}

.savings {
  background-color: var(--color-primary);
  color: var(--color-white);
  font-size: 0.8rem;
  padding: 0.25rem 0.5rem;
  border-radius: 4px;
  margin-left: 0.5rem;
}

.payment-section h3 {
  margin-bottom: 1rem;
  color: var(--color-primary);
}

.payment-method-selection {
  margin-bottom: 1.5rem;
}

.subscription-summary {
  background-color: var(--color-primary-lightest);
  padding: 1rem;
  border-radius: var(--border-radius);
  margin: 2rem 0;
}

.subscription-summary h4 {
  margin-bottom: 0.5rem;
  color: var(--color-primary);
}

.subscription-summary p {
  margin-bottom: 0.25rem;
}

/* UserDashboard.css */
.dashboard-container {
  max-width: 900px;
  margin: 0 auto;
  animation: fadeIn 0.6s ease-out;
}

.dashboard-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
}

.dashboard-header h2 {
  color: var(--color-primary);
  margin: 0;
}

.new-message-btn {
  background-color: var(--color-primary);
  color: var(--color-white);
  padding: 0.75rem 1.5rem;
  border-radius: var(--border-radius);
  text-decoration: none;
  transition: background-color var(--transition-speed);
}

.new-message-btn:hover {
  background-color: var(--color-primary-light);
}

.subscription-status {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: var(--color-white);
  padding: 1rem;
  border-radius: var(--border-radius);
  margin-bottom: 1.5rem;
  box-shadow: var(--box-shadow);
}

.status-indicator .status-active {
  color: var(--color-success);
  font-weight: bold;
}

.status-indicator .status-inactive {
  color: var(--color-text-light);
}

.subscribe-btn {
  background-color: var(--color-primary);
  color: var(--color-white);
  padding: 0.5rem 1rem;
  border-radius: var(--border-radius);
  transition: background-color var(--transition-speed);
}

.subscribe-btn:hover {
  background-color: var(--color-primary-light);
}

.message-count-summary {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.count-item {
  background-color: var(--color-white);
  padding: 1rem;
  border-radius: var(--border-radius);
  text-align: center;
  box-shadow: var(--box-shadow);
}

.count-label {
  color: var(--color-text-light);
  font-size: 0.9rem;
}

.count-value {
  display: block;
  font-size: 1.5rem;
  font-weight: bold;
  color: var(--color-primary);
  margin-top: 0.5rem;
}

.dashboard-tabs {
  display: flex;
  gap: 1rem;
  margin-bottom: 1.5rem;
  border-bottom: 1px solid var(--color-border);
  padding-bottom: 0.5rem;
}

.dashboard-tabs button {
  background: none;
  border: none;
  padding: 0.5rem 1rem;
  font-size: 1rem;
  color: var(--color-text-light);
  position: relative;
}

.dashboard-tabs button:hover {
  color: var(--color-primary);
}

.dashboard-tabs button.tab-active {
  color: var(--color-primary);
  font-weight: bold;
}

.dashboard-tabs button.tab-active::after {
  content: '';
  position: absolute;
  bottom: -0.5rem;
  left: 0;
  width: 100%;
  height: 2px;
  background-color: var(--color-primary);
}

.messages-list {
  display: grid;
  gap: 1.5rem;
}

.message-card {
  background-color: var(--color-white);
  border-radius: var(--border-radius);
  padding: 1.5rem;
  box-shadow: var(--box-shadow);
  transition: transform var(--transition-speed);
}

.message-card:hover {
  transform: translateY(-5px);
}

.message-card.scheduled {
  border-left: 4px solid #f9a825;
}

.message-card.delivered {
  border-left: 4px solid var(--color-success);
}

.message-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding-bottom: 0.5rem;
  border-bottom: 1px solid var(--color-border);
}

.message-type {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.type-icon {
  font-size: 1.25rem;
}

.message-status {
  color: var(--color-text-light);
  font-size: 0.9rem;
}

.message-recipient {
  margin-bottom: 1rem;
}

.message-content {
  margin-bottom: 1.5rem;
  padding: 1rem;
  background-color: var(--color-primary-lightest);
  border-radius: var(--border-radius);
}

.text-content {
  white-space: pre-wrap;
}

.message-schedule {
  margin-bottom: 1rem;
  font-size: 0.9rem;
  color: var(--color-text-light);
}

.message-actions {
  display: flex;
  justify-content: flex-end;
}

.delete-btn {
  background-color: var(--color-error);
  color: var(--color-white);
  padding: 0.5rem 1rem;
  border-radius: var(--border-radius);
  transition: background-color var(--transition-speed);
}

.delete-btn:hover {
  background-color: #b71c1c;
}

.no-messages {
  text-align: center;
  padding: 3rem;
  background-color: var(--color-white);
  border-radius: var(--border-radius);
  box-shadow: var(--box-shadow);
}

.no-messages p {
  margin-bottom: 1.5rem;
  color: var(--color-text-light);
}

.no-messages button {
  background-color: var(--color-primary);
  color: var(--color-white);
  padding: 0.75rem 1.5rem;
  border-radius: var(--border-radius);
  transition: background-color var(--transition-speed);
}

.no-messages button:hover {
  background-color: var(--color-primary-light);
}
