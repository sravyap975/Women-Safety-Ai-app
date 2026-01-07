SafeHer - Women Safety AI Application
A comprehensive women safety web application with AI-powered monitoring, emergency alerts, and real-time location tracking.
🌟 Features
Core Functionality

Multi-Factor Authentication: Email/Password + OTP verification
Emergency Contacts: Mandatory setup of 3 trusted contacts on first login
Safety Mode: Active monitoring with motion detection and location tracking
SOS Button: Press and hold for 3 seconds to trigger emergency alert
Live Location Tracking: Real-time location updates on Google Maps
State-Based Helplines: Dynamic helpline numbers based on selected state
Context-Aware Safety Tips: Tips that change based on time of day
Emergency Alerts: Automatic notification to emergency contacts and authorities

Advanced Features

Motion Monitoring: Detects abnormal movements using device sensors
Wake Word Detection: Voice-activated emergency trigger (placeholder)
Police Siren Sound: Plays during emergency to deter threats
Incident Logging: All emergencies recorded in Firestore
Continuous Location Tracking: Updates every 30 seconds during safety mode
False Positive Prevention: Vibration alerts before triggering emergency

🛠️ Tech Stack

Frontend: HTML5, CSS3, JavaScript (ES6+)
Backend: Firebase (BaaS)
Authentication: Firebase Authentication
Database: Cloud Firestore
Maps: Google Maps JavaScript API
Hosting: Firebase Hosting

📁 Project Structure
women-safety-app/
├── index.html              # Login page
├── otp-verify.html         # OTP verification page
├── emergency-setup.html    # Emergency contacts setup
├── dashboard.html          # Main dashboard
├── css/
│   └── styles.css          # Complete styling
├── js/
│   ├── firebase-config.js  # Firebase configuration
│   ├── auth.js             # Authentication logic
│   ├── otp-verify.js       # OTP verification
│   ├── emergency-setup.js  # Emergency contacts setup
│   └── dashboard.js        # Dashboard & safety features
├── sounds/
│   └── police-siren.mp3    # Emergency siren sound
└── README.md               # This file
🚀 Setup Instructions
1. Prerequisites

A Firebase account (free tier works)
Google Cloud Platform account (for Maps API)
Modern web browser
Text editor or IDE

2. Firebase Setup
Create Firebase Project

Go to Firebase Console
Click "Add project"
Enter project name: "women-safety-app"
Disable Google Analytics (optional)
Click "Create project"

Enable Authentication

In Firebase Console, go to "Authentication"
Click "Get started"
Enable "Email/Password" provider
Click "Save"

Create Firestore Database

Go to "Firestore Database"
Click "Create database"
Choose "Start in production mode"
Select your preferred location
Click "Enable"

Configure Firestore Rules
Replace the default rules with:
javascriptrules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Users can only access their own data
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    
    // Emergency contacts belong to users
    match /emergencyContacts/{contactId} {
      allow read, write: if request.auth != null && 
        request.auth.uid == resource.data.userId;
    }
    
    // Incidents belong to users
    match /incidents/{incidentId} {
      allow read, write: if request.auth != null && 
        request.auth.uid == resource.data.userId;
    }
    
    // Location history
    match /locationHistory/{locationId} {
      allow read, write: if request.auth != null && 
        request.auth.uid == resource.data.userId;
    }
  }
}
Get Firebase Config

Go to Project Settings (gear icon)
Scroll to "Your apps"
Click "Web" icon (</>) to register app
Register app with nickname "SafeHer Web"
Copy the Firebase configuration object

3. Google Maps API Setup

Go to Google Cloud Console
Create a new project or select existing
Enable "Maps JavaScript API"
Create API credentials (API Key)
Restrict API key to your domain (for production)
Copy the API key

4. Configure the Application
Update Firebase Config
Open js/firebase-config.js and replace with your config:
javascriptconst firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "your-project.firebaseapp.com",
    projectId: "your-project-id",
    storageBucket: "your-project.appspot.com",
    messagingSenderId: "123456789",
    appId: "your-app-id"
};
Update Google Maps API Key
Open dashboard.html and replace:
html<script src="https://maps.googleapis.com/maps/api/js?key=YOUR_GOOGLE_MAPS_API_KEY&libraries=places"></script>
5. Add Police Siren Sound

Download a police siren sound file (MP3 format)
Save it as police-siren.mp3 in the sounds/ folder
Or use any royalty-free siren sound from online resources

6. Local Development
Option 1: Live Server (VS Code)

Install "Live Server" extension in VS Code
Right-click index.html
Select "Open with Live Server"

Option 2: Python HTTP Server
bash# Python 3
python -m http.server 8000

# Then open: http://localhost:8000
Option 3: Node.js HTTP Server
bashnpm install -g http-server
http-server -p 8000
7. Deploy to Firebase Hosting
bash# Install Firebase CLI
npm install -g firebase-tools

# Login to Firebase
firebase login

# Initialize Firebase
firebase init

# Select:
# - Hosting
# - Use existing project
# - Public directory: . (current directory)
# - Configure as single-page app: No
# - Set up automatic builds: No

# Deploy
firebase deploy
🔒 Security Features
Authentication Flow

Email/Password authentication via Firebase Auth
6-digit OTP generation and verification
OTP expires after 5 minutes
Session management with secure tokens

Data Security

Firestore security rules ensure users can only access their own data
Emergency contacts are user-specific
Location data is private and encrypted
All API calls are authenticated

Privacy

Location tracking only active when safety mode is ON
Users have full control over data sharing
Emergency contacts are stored securely
Incident history is private

📱 User Workflow
Phase 1: User Intent

User installs/opens app
Logs in with email/password
Verifies OTP from email
Sets up 3 emergency contacts (first time only)
Grants location and motion permissions

Phase 2: Active Monitoring

User toggles Safety Mode ON
App starts monitoring:

Location updates every 30 seconds
Motion sensor for abnormal movements
Wake word detection (optional)


Real-time status displayed on dashboard

Phase 3: Intelligence Layer

Abnormal Movement Detected:

Phone vibrates twice
User prompt: "Are you okay?"
If no response → Trigger emergency


Wake Word Detected:

"Hey Safety" spoken
Immediate SOS trigger



Phase 4: Emergency Response

SOS button pressed (hold 3 seconds) OR automated trigger
Police siren sound plays
Location captured and tracked continuously
Emergency alerts sent to all 3 contacts
Location shared with contacts
Incident logged in database

Phase 5: Post-Incident

User marks as "Safe" or "Cancel Alert"
Incident status updated in Firestore
Emergency contacts notified of resolution
Safety mode continues or turns off

🎨 Design Features

Women-Centric Design: Soft rose, lavender, and sage color palette
Sophisticated Typography: Cormorant Garamond + DM Sans
Smooth Animations: CSS transitions and keyframe animations
Responsive Layout: Works on mobile, tablet, and desktop
Accessibility: High contrast, clear labels, keyboard navigation
Professional UI: Hackathon/portfolio-grade quality

🔧 Customization
Add More States
Edit js/dashboard.js:
javascriptconst stateHelplines = {
    'ST': { name: 'State Name', helpline: '1-800-XXX-XXXX' }
};
Change Colors
Edit css/styles.css CSS variables:
css:root {
    --primary-rose: #d4748e;
    --secondary-lavender: #b8a4d4;
    /* etc. */
}
Modify Safety Tips
Edit getSafetyTips() function in js/dashboard.js
🚨 Production Considerations
Email Service for OTP
Implement a backend service (Cloud Function) to send OTP emails:
javascript// Cloud Function example
const nodemailer = require('nodemailer');

exports.sendOTP = functions.https.onRequest(async (req, res) => {
    const { email, otp } = req.body;
    
    // Send email using SendGrid, AWS SES, or similar
    // ...
});
SMS Alerts
Integrate with Twilio or similar service:
javascriptconst twilio = require('twilio');
const client = twilio(accountSid, authToken);

// Send SMS to emergency contacts
await client.messages.create({
    body: 'Emergency alert message',
    from: '+1234567890',
    to: contact.phone
});
Audio Recording
Implement audio capture during emergency:
javascriptconst mediaRecorder = new MediaRecorder(stream);
mediaRecorder.start();
// Upload to Firebase Storage
Push Notifications
Use Firebase Cloud Messaging for alerts
Production Security

Enable HTTPS
Set up proper CORS
Implement rate limiting
Add CAPTCHA to prevent bots
Use environment variables for API keys

📊 Firestore Data Structure
Users Collection
javascriptusers/{userId}
  - email: string
  - createdAt: timestamp
  - hasEmergencyContacts: boolean
  - safetyModeActive: boolean
  - displayName: string (optional)
Emergency Contacts Collection
javascriptemergencyContacts/{contactId}
  - userId: string
  - name: string
  - phone: string
  - relation: string
  - priority: number (1-3)
  - createdAt: timestamp
Incidents Collection
javascriptincidents/{incidentId}
  - userId: string
  - reason: string
  - location: GeoPoint
  - timestamp: timestamp
  - status: string (active/resolved_safe/cancelled)
  - resolved: boolean
  - resolvedAt: timestamp
Location History Collection
javascriptlocationHistory/{locationId}
  - userId: string
  - location: GeoPoint
  - timestamp: timestamp
  - safetyModeActive: boolean
🐛 Troubleshooting
Firebase Connection Issues

Verify Firebase config is correct
Check browser console for errors
Ensure Firestore rules allow access

Google Maps Not Loading

Verify API key is correct
Check if Maps JavaScript API is enabled
Ensure billing is set up (required even for free tier)

Location Not Working

Check browser permissions
Ensure HTTPS (required for geolocation)
Test on different browsers/devices

OTP Not Showing

Check browser console for OTP (demo mode)
Implement email service for production
Verify email format is correct

📝 Testing
Test User Account

Create test account via signup
Use temporary email services
Test full workflow

Test Emergency Features

Toggle safety mode ON
Shake device to test motion detection
Hold SOS button to trigger alert
Verify emergency contacts receive alerts

Browser Compatibility

Chrome 90+
Firefox 88+
Safari 14+
Edge 90+

📄 License
This project is created for educational and safety purposes.
🤝 Contributing
This is a hackathon/portfolio project. Feel free to fork and customize for your needs.
📞 Support
For issues or questions:

Check Troubleshooting section
Review Firebase documentation
Check Google Maps API documentation

⚠️ Disclaimer
This application is designed to assist in emergency situations but should not be relied upon as the sole means of protection. Always contact local emergency services (911 in US) for immediate help.

Built with care for women's safety 💜