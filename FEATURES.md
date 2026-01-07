SafeHer - Complete Features Documentation
🎯 Core Features Implementation
1. AUTHENTICATION SYSTEM
Multi-Factor Authentication Flow
Implementation: index.html + js/auth.js
Flow:

User enters email and password
Firebase Authentication validates credentials
System generates 6-digit OTP
OTP stored in sessionStorage with 5-minute expiry
Email sent to user (demo: shown in alert, production: Cloud Function)
User redirected to OTP verification page

Security Features:

Password minimum 6 characters
OTP expires after 5 minutes
Rate limiting on failed attempts
Secure session management
Error handling for all Firebase auth errors

Code Highlights:
javascript// OTP Generation
function generateOTP() {
    return Math.floor(100000 + Math.random() * 900000).toString();
}

// OTP Storage with expiry
storeOTP(email, otp) {
    const otpData = {
        otp: otp,
        email: email,
        timestamp: Date.now(),
        expires: Date.now() + (5 * 60 * 1000) // 5 minutes
    };
    sessionStorage.setItem('otpData', JSON.stringify(otpData));
}

2. OTP VERIFICATION
Implementation
Files: otp-verify.html + js/otp-verify.js
Features:

6 individual input boxes with auto-focus
Auto-advance to next input on digit entry
Backspace navigation between inputs
Paste support for 6-digit codes
Resend OTP functionality
Visual feedback for validation

User Experience:

Smooth transitions between inputs
Clear error messages
Loading states during verification
Automatic redirect after success

Code Highlights:
javascript// Auto-focus next input
input.addEventListener('input', (e) => {
    if (value && index < otpInputs.length - 1) {
        otpInputs[index + 1].focus();
    }
});

// Paste handling
input.addEventListener('paste', (e) => {
    const pastedData = e.clipboardData.getData('text').trim();
    if (/^\d{6}$/.test(pastedData)) {
        pastedData.split('').forEach((char, i) => {
            otpInputs[i].value = char;
        });
    }
});

3. EMERGENCY CONTACTS SETUP
First-Time User Onboarding
Implementation: emergency-setup.html + js/emergency-setup.js
Requirements:

Exactly 3 contacts required
Each contact needs: Name, Phone, Relationship
Phone validation (10-11 digits)
No duplicate phone numbers allowed
Automatic phone formatting

Validation:

All fields required
Phone number format validation
Unique phone numbers
Relationship selection mandatory

Database Structure:
javascriptemergencyContacts/{contactId}
  - userId: string
  - name: string
  - phone: string (digits only)
  - relation: string
  - priority: number (1-3)
  - createdAt: timestamp
  - updatedAt: timestamp
User Flow:

User completes OTP verification
System checks if contacts exist
If no contacts → Force emergency setup
User adds 3 contacts
Contacts saved to Firestore
User profile updated with hasEmergencyContacts: true
Redirect to dashboard


4. DASHBOARD
Main Interface
Implementation: dashboard.html + js/dashboard.js
Sections:

Header with user info and logout
Status card with safety indicator
Safety mode toggle
Live location map
SOS emergency button
State selection
Helpline numbers
Safety tips
Emergency contacts list

Key Features:

Real-time status updates
Dynamic content based on state/time
Responsive grid layout
Smooth animations
Professional design


5. SAFETY MODE
Active Monitoring System
Implementation: js/dashboard.js
When Active:

Location Tracking:

Continuous GPS monitoring
Updates every 30 seconds
Saved to Firestore
Displayed on Google Maps


Motion Monitoring:

Uses DeviceMotion API
Detects abnormal movements
Threshold-based detection
False positive prevention


Wake Word Detection:

Listens for "Hey Safety"
Web Speech API (placeholder)
Immediate SOS trigger



Status Indicators:

🟢 Safe: Safety mode OFF
🟡 Monitoring: Safety mode ON
🔴 Emergency: Alert active

Code Structure:
javascriptfunction activateSafetyMode() {
    updateStatus('monitoring', 'Safety Mode Active', 'Monitoring your safety');
    startLocationTracking();
    startMotionMonitoring();
    startWakeWordDetection();
    saveSafetyModeStatus(true);
}

6. LOCATION TRACKING
Real-Time GPS Monitoring
Implementation: Google Maps JavaScript API
Features:

Live location display on map
Automatic location updates
Reverse geocoding (address display)
Location history logging
Manual refresh option

Tracking Modes:

Safety Mode OFF: Single location request
Safety Mode ON: Continuous tracking (watchPosition)
Emergency: High-frequency updates

Map Customization:

Custom color scheme matching app design
User marker with custom icon
Smooth pan and zoom animations
Responsive to window resize

Database Storage:
javascriptlocationHistory/{locationId}
  - userId: string
  - location: GeoPoint
  - timestamp: timestamp
  - safetyModeActive: boolean

7. MOTION DETECTION
Abnormal Movement Detection
Implementation: DeviceMotion API
How It Works:

Monitor device acceleration
Calculate total acceleration vector
Compare against threshold (25 m/s²)
Count consecutive abnormal movements
Trigger alert after 3 detections

False Positive Prevention:

Phone vibrates twice
User prompt: "Are you okay?"
User confirms safety OR
No response → Emergency triggered

Code Implementation:
javascriptfunction handleMotionEvent(event) {
    const acc = event.accelerationIncludingGravity;
    const totalAcc = Math.sqrt(acc.x**2 + acc.y**2 + acc.z**2);
    
    if (totalAcc > threshold) {
        abnormalMovementCount++;
        if (abnormalMovementCount >= 3) {
            triggerAbnormalMovementAlert();
        }
    }
}

8. SOS BUTTON
Emergency Trigger
Implementation: Dashboard with hold-to-activate
Design:

Large circular button
Red gradient background
Pulsing animation
Clear "SOS" label
200px × 200px size

Activation:

Press and hold for 3 seconds
Visual feedback (scale animation)
Timer prevents accidental triggers
Works with mouse and touch events

User Instructions:
"Press and hold for 3 seconds to activate emergency alert"
Event Handling:
javascriptsosButton.addEventListener('mousedown', startSOSTimer);
sosButton.addEventListener('mouseup', cancelSOSTimer);
sosButton.addEventListener('touchstart', startSOSTimer);
sosButton.addEventListener('touchend', cancelSOSTimer);

function startSOSTimer() {
    sosTimer = setTimeout(() => {
        triggerEmergency('Manual SOS activated');
    }, 3000);
}

9. EMERGENCY ALERT SYSTEM
Complete Emergency Response
Implementation: Multi-step emergency protocol
Trigger Methods:

SOS button (hold 3 seconds)
Abnormal movement detection
Wake word "Hey Safety"

Emergency Actions:

✅ Update status to EMERGENCY
✅ Show emergency modal
✅ Play police siren sound
✅ Get current location
✅ Start continuous tracking
✅ Send alerts to emergency contacts
✅ Log incident to Firestore
✅ Share location URL

Alert Message Template:
🚨 EMERGENCY ALERT 🚨

[User Email] has triggered an emergency alert.

Reason: [Trigger reason]
Time: [Timestamp]
Location: [Google Maps URL]

Please check on them immediately!
Emergency Modal:

Large warning icon with pulse animation
Clear emergency message
Two action buttons:

"Cancel Alert"
"I'm Safe Now"


Prevents accidental dismissal


10. POLICE SIREN
Audio Deterrent
Implementation: HTML5 Audio API
Features:

Automatic playback on emergency
Continuous loop
Pause on emergency resolution
Browser autoplay handling

File Location: sounds/police-siren.mp3
Code:
javascriptconst siren = document.getElementById('sirenSound');
siren.play(); // On emergency
siren.pause(); // On resolution

11. STATE-BASED HELPLINES
Dynamic Emergency Numbers
Implementation: Dropdown with state selection
Database:
javascriptconst stateHelplines = {
    'CA': { name: 'California', helpline: '1-800-799-7233' },
    'NY': { name: 'New York', helpline: '1-800-942-6906' },
    // ... more states
};
Display:

Emergency: 911 (always shown)
State-specific women's helpline
National domestic violence hotline

Features:

Clickable phone links
Auto-dial on mobile
Updates on state change
Clear formatting


12. SAFETY TIPS
Context-Aware Advice
Implementation: Dynamic tips based on time
Time-Based Logic:

Night (8 PM - 6 AM): Stay in well-lit areas, transportation safety
Day (6 AM - 8 PM): Situational awareness, trust instincts

Tip Format:

Icon indicator
Clear, actionable advice
4 tips displayed at once
Updates automatically

Example Tips:
javascriptNight Tips:
- 🌙 Stay in well-lit areas when walking alone
- 📱 Keep your phone charged and accessible
- 👥 Share your location with trusted contacts
- 🚗 Use trusted transportation services

Day Tips:
- ☀️ Stay aware of your surroundings
- 👀 Trust your instincts
- 📍 Share your live location
- 🆘 Know emergency exits in public places

13. INCIDENT LOGGING
Complete Audit Trail
Implementation: Firestore database
Data Structure:
javascriptincidents/{incidentId}
  - userId: string
  - reason: string
  - location: GeoPoint
  - timestamp: timestamp
  - status: 'active' | 'resolved_safe' | 'cancelled'
  - resolved: boolean
  - resolvedAt: timestamp
Status Flow:

active: Emergency triggered
resolved_safe: User confirmed safe
cancelled: User cancelled alert

Use Cases:

Emergency history
Safety analytics
Pattern detection
Support documentation


🎨 Design System
Color Palette
css--primary-rose: #d4748e        /* Main brand color */
--primary-rose-dark: #b85c74   /* Hover states */
--primary-rose-light: #e8a4b8  /* Backgrounds */
--secondary-lavender: #b8a4d4  /* Accents */
--secondary-sage: #a4c4a4      /* Success states */

--bg-cream: #faf7f5            /* Page background */
--bg-white: #ffffff            /* Card backgrounds */
--bg-soft: #f5f0ed             /* Input backgrounds */

--text-primary: #3d2e2e        /* Main text */
--text-secondary: #6d5d5d      /* Secondary text */
--text-muted: #9d8d8d          /* Muted text */
Typography

Display: Cormorant Garamond (elegant, feminine)
Body: DM Sans (modern, readable)

Animations

Slide-up entrance animations
Smooth hover transitions
Pulsing indicators
Floating decorative elements
Breathing logo animation


🔐 Security Features
Authentication

Firebase Authentication
OTP verification
Session management
Auto-logout on inactivity

Data Privacy

User-specific data access
Firestore security rules
Encrypted connections (HTTPS)
No data sharing without consent

Location Privacy

Only tracked with permission
Only during safety mode
User can view all location history
Can delete account and data


📊 Database Schema
Users Collection
javascriptusers/{userId}
  - email: string
  - displayName: string (optional)
  - createdAt: timestamp
  - hasEmergencyContacts: boolean
  - safetyModeActive: boolean
  - safetyModeUpdatedAt: timestamp
  - emergencyContactsSetupAt: timestamp
Emergency Contacts Collection
javascriptemergencyContacts/{contactId}
  - userId: string
  - name: string
  - phone: string
  - relation: string
  - priority: number (1-3)
  - createdAt: timestamp
  - updatedAt: timestamp
Incidents Collection
javascriptincidents/{incidentId}
  - userId: string
  - reason: string
  - location: GeoPoint
  - timestamp: timestamp
  - status: string
  - resolved: boolean
  - resolvedAt: timestamp
Location History Collection
javascriptlocationHistory/{locationId}
  - userId: string
  - location: GeoPoint
  - timestamp: timestamp
  - safetyModeActive: boolean

🚀 Performance
Load Time Optimization

Minified CSS and JavaScript
Lazy loading for maps
Optimized images
Firebase CDN for static assets

Mobile Optimization

Responsive design
Touch-optimized controls
Efficient battery usage
Minimal data consumption


♿ Accessibility
WCAG 2.1 Compliance

High contrast ratios
Keyboard navigation
Screen reader support
Clear focus indicators
ARIA labels
Semantic HTML


📱 Browser Support
Tested Browsers

✅ Chrome 90+
✅ Firefox 88+
✅ Safari 14+
✅ Edge 90+
✅ Mobile Safari (iOS 14+)
✅ Chrome Mobile (Android 10+)

Required APIs

Geolocation API
DeviceMotion API (optional)
Web Audio API
Local Storage
Session Storage


🎯 User Journey
First-Time User

Open app → Login page
Click "Create account"
Enter email and password
Receive OTP via email
Verify OTP
Add 3 emergency contacts
Arrive at dashboard
Grant location permission
Ready to use

Returning User

Open app → Login page
Enter credentials
Verify OTP
Arrive at dashboard
Toggle safety mode as needed

Emergency Scenario

User feels unsafe
Toggles safety mode ON
App monitors location and motion
Abnormal movement detected
Phone vibrates for confirmation
No response after 10 seconds
Emergency triggered automatically
Siren plays, contacts notified
Continuous location tracking
User resolves when safe


💡 Future Enhancements
Planned Features

Audio recording during emergencies
Video recording option
Fake call feature
Shake gesture for SOS
Bluetooth panic button support
Apple Watch integration
Live streaming to contacts
Chat with emergency contacts
Community safety map
Self-defense tips
Local crime data integration

Technical Improvements

Offline mode with service workers
PWA conversion
Native mobile apps
ML-based threat detection
Voice recognition for distress
Facial recognition for attacker
Smart wearable integration


This application is built with one goal: keeping women safe. Every feature is designed with care and attention to real-world safety needs. 💜