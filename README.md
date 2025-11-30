<!doctype html>
<html lang="en">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>BAZRO - Groceries in Minutes</title>
  <script src="/_sdk/element_sdk.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&amp;display=swap" rel="stylesheet">
  <style>
    body {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
      background: #fafafa;
    }
    
    * {
      box-sizing: border-box;
    }

    .hero-gradient {
      background: linear-gradient(135deg, #8B5CF6 0%, #EC4899 100%);
    }

    .glass-card {
      background: rgba(255, 255, 255, 0.95);
      backdrop-filter: blur(10px);
      box-shadow: 0 8px 32px rgba(0, 0, 0, 0.08);
    }

    .product-card {
      transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    }

    .product-card:hover {
      transform: translateY(-8px);
      box-shadow: 0 20px 40px rgba(0, 0, 0, 0.12);
    }

    .category-chip {
      transition: all 0.2s;
    }

    .category-chip:hover {
      transform: scale(1.05);
    }

    .pulse-animation {
      animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
    }

    @keyframes pulse {
      0%, 100% { opacity: 1; }
      50% { opacity: 0.7; }
    }

    @keyframes slideUp {
      from {
        opacity: 0;
        transform: translateY(30px);
      }
      to {
        opacity: 1;
        transform: translateY(0);
      }
    }

    .slide-up {
      animation: slideUp 0.6s ease-out;
    }

    .otp-input {
      width: 50px;
      height: 60px;
      font-size: 24px;
      text-align: center;
      border: 2px solid #e5e7eb;
      border-radius: 12px;
      font-weight: bold;
      transition: border-color 0.2s;
    }

    .otp-input:focus {
      outline: none;
      border-color: #8B5CF6;
      box-shadow: 0 0 0 3px rgba(139, 92, 246, 0.1);
    }

    .scroll-smooth {
      scroll-behavior: smooth;
      scrollbar-width: thin;
      scrollbar-color: #8B5CF6 #f3f4f6;
    }

    .scroll-smooth::-webkit-scrollbar {
      height: 6px;
    }

    .scroll-smooth::-webkit-scrollbar-track {
      background: #f3f4f6;
      border-radius: 10px;
    }

    .scroll-smooth::-webkit-scrollbar-thumb {
      background: #8B5CF6;
      border-radius: 10px;
    }

    .discount-badge {
      background: linear-gradient(135deg, #10b981 0%, #059669 100%);
    }

    @keyframes checkmark {
      0% { transform: scale(0) rotate(0deg); }
      50% { transform: scale(1.2) rotate(180deg); }
      100% { transform: scale(1) rotate(360deg); }
    }

    .checkmark-animation {
      animation: checkmark 0.6s ease-out;
    }
  </style>
  <style>@view-transition { navigation: auto; }</style>
  <script src="/_sdk/data_sdk.js" type="text/javascript"></script>
  <script src="https://cdn.tailwindcss.com" type="text/javascript"></script>
 </head>
 <body>
  <div id="app" class="w-full min-h-full"></div>
  <script>
    const defaultConfig = {
      app_name: "BAZRO",
      tagline: "Groceries in Minutes",
      hero_title: "India's Last Minute App",
      hero_subtitle: "10 minute grocery delivery",
      offer_banner_text: "Use code FIRST50 for 50% OFF",
      primary_color: "#8B5CF6",
      secondary_color: "#ffffff",
      accent_color: "#EC4899",
      text_color: "#1f2937",
      success_color: "#10b981",
      font_family: "Inter",
      font_size: 16
    };

    let config = { ...defaultConfig };
    let isLoggedIn = false;
    let userPhone = '';
    let userLocation = '';
    let cartItems = [];
    let userProfile = {
      name: '',
      photo: ''
    };

    // Check if already logged in
    function checkLogin() {
      // Always return false - user must login manually each time
      return false;
    }

    function showToast(message) {
      const existingToast = document.querySelector('.toast-message');
      if (existingToast) existingToast.remove();
      
      const toast = document.createElement('div');
      toast.className = 'toast-message fixed top-4 left-1/2 px-6 py-3 rounded-full shadow-lg z-50';
      toast.style.transform = 'translateX(-50%)';
      toast.style.backgroundColor = config.success_color || defaultConfig.success_color;
      toast.style.color = config.secondary_color || defaultConfig.secondary_color;
      toast.style.fontWeight = '600';
      toast.textContent = message;
      document.body.appendChild(toast);
      
      setTimeout(() => {
        toast.style.opacity = '0';
        toast.style.transition = 'opacity 0.3s';
        setTimeout(() => toast.remove(), 300);
      }, 2500);
    }

    function showLoginScreen() {
      const app = document.getElementById('app');
      const customFont = config.font_family || defaultConfig.font_family;
      const baseFontSize = config.font_size || defaultConfig.font_size;
      
      app.innerHTML = `
        <div style="width: 100%; min-height: 100%; display: flex; align-items: center; justify-content: center; padding: 20px; font-family: ${customFont}, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; font-size: ${baseFontSize}px; background: linear-gradient(135deg, ${config.primary_color || defaultConfig.primary_color} 0%, ${config.accent_color || defaultConfig.accent_color} 100%)">
          
          <div style="width: 100%; max-width: 440px">
            <!-- Logo & Brand -->
            <div style="text-align: center; margin-bottom: 40px" class="slide-up">
              <div style="width: 100px; height: 100px; border-radius: 50%; margin: 0 auto 20px; display: flex; align-items: center; justify-content: center; background: ${config.secondary_color || defaultConfig.secondary_color}; box-shadow: 0 10px 40px rgba(0, 0, 0, 0.2)">
                <span style="font-size: 50px">🛒</span>
              </div>
              <h1 style="font-size: ${baseFontSize * 3}px; font-weight: 900; color: ${config.secondary_color || defaultConfig.secondary_color}; margin: 0 0 8px 0; letter-spacing: 4px">${config.app_name || defaultConfig.app_name}</h1>
              <p style="font-size: ${baseFontSize * 1.125}px; font-weight: 600; color: ${config.secondary_color || defaultConfig.secondary_color}; opacity: 0.95; margin: 0">${config.tagline || defaultConfig.tagline}</p>
            </div>

            <!-- Login Card -->
            <div id="login-card" class="slide-up glass-card" style="background: ${config.secondary_color || defaultConfig.secondary_color}; border-radius: 24px; padding: 32px; box-shadow: 0 20px 60px rgba(0, 0, 0, 0.2)">
              <h2 style="font-size: ${baseFontSize * 1.5}px; font-weight: 700; color: ${config.text_color || defaultConfig.text_color}; margin: 0 0 24px 0; text-align: center">Enter Mobile Number</h2>
              
              <div style="margin-bottom: 24px">
                <label style="display: block; margin-bottom: 8px; font-size: ${baseFontSize * 0.875}px; font-weight: 600; color: ${config.text_color || defaultConfig.text_color}">Mobile Number</label>
                <div style="display: flex; align-items: center; gap: 12px">
                  <span style="font-size: ${baseFontSize}px; font-weight: 700; padding: 14px 16px; border-radius: 12px; background: #f3f4f6; color: ${config.text_color || defaultConfig.text_color}">+91</span>
                  <input 
                    id="phone-input" 
                    type="tel" 
                    maxlength="10" 
                    placeholder="9876543210"
                    style="flex: 1; padding: 14px 16px; border-radius: 12px; background: #f3f4f6; border: 2px solid transparent; font-size: ${baseFontSize}px; font-weight: 600; color: ${config.text_color || defaultConfig.text_color}"
                    onkeypress="return event.charCode >= 48 && event.charCode <= 57"
                  >
                </div>
              </div>

              <button onclick="sendOTP()" style="width: 100%; padding: 16px; border-radius: 12px; background: ${config.primary_color || defaultConfig.primary_color}; color: ${config.secondary_color || defaultConfig.secondary_color}; font-size: ${baseFontSize * 1.125}px; font-weight: 700; border: none; cursor: pointer; box-shadow: 0 4px 16px rgba(139, 92, 246, 0.3)">
                Continue
              </button>

              <p style="text-align: center; font-size: ${baseFontSize * 0.75}px; color: #9ca3af; margin: 16px 0 0 0">By continuing, you agree to our Terms & Privacy Policy</p>
            </div>

            <!-- Features -->
            <div style="display: flex; justify-content: space-around; margin-top: 32px; gap: 20px">
              <div style="text-align: center">
                <div style="font-size: ${baseFontSize * 1.5}px; font-weight: 900; color: ${config.secondary_color || defaultConfig.secondary_color}; margin-bottom: 4px">10 Min</div>
                <div style="font-size: ${baseFontSize * 0.875}px; color: ${config.secondary_color || defaultConfig.secondary_color}; opacity: 0.9">Delivery</div>
              </div>
              <div style="text-align: center">
                <div style="font-size: ${baseFontSize * 1.5}px; font-weight: 900; color: ${config.secondary_color || defaultConfig.secondary_color}; margin-bottom: 4px">₹0 Fee</div>
                <div style="font-size: ${baseFontSize * 0.875}px; color: ${config.secondary_color || defaultConfig.secondary_color}; opacity: 0.9">On ₹99+</div>
              </div>
              <div style="text-align: center">
                <div style="font-size: ${baseFontSize * 1.5}px; font-weight: 900; color: ${config.secondary_color || defaultConfig.secondary_color}; margin-bottom: 4px">1000+</div>
                <div style="font-size: ${baseFontSize * 0.875}px; color: ${config.secondary_color || defaultConfig.secondary_color}; opacity: 0.9">Products</div>
              </div>
            </div>
          </div>

        </div>
      `;
    }

    function sendOTP() {
      const phoneInput = document.getElementById('phone-input');
      const phone = phoneInput.value.trim();
      
      if (phone.length !== 10) {
        showToast('Please enter a valid 10-digit mobile number');
        return;
      }
      
      userPhone = phone;
      
      // Auto-login for special number
      if (phone === '7897242140') {
        showToast('✅ Welcome back! Auto-logging in...');
        setTimeout(() => {
          showLocationInput();
        }, 1000);
        return;
      }
      
      // Generate OTP for other numbers
      const generatedOTP = Math.floor(100000 + Math.random() * 900000).toString();
      sessionStorage.setItem('currentOTP', generatedOTP);
      
      // Show OTP screen
      showOTPScreen(phone, generatedOTP);
    }

    function showOTPScreen(phone, otp) {
      const loginCard = document.getElementById('login-card');
      const baseFontSize = config.font_size || defaultConfig.font_size;
      
      loginCard.innerHTML = `
        <button onclick="showLoginScreen()" style="display: flex; align-items: center; gap: 8px; color: ${config.primary_color || defaultConfig.primary_color}; background: none; border: none; cursor: pointer; padding: 8px; margin-bottom: 16px; font-size: ${baseFontSize}px; font-weight: 600">
          <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <polyline points="15 18 9 12 15 6"></polyline>
          </svg>
          Back
        </button>

        <h2 style="font-size: ${baseFontSize * 1.5}px; font-weight: 700; color: ${config.text_color || defaultConfig.text_color}; margin: 0 0 8px 0; text-align: center">Enter OTP</h2>
        <p style="font-size: ${baseFontSize * 0.875}px; color: #6b7280; margin: 0 0 24px 0; text-align: center">Sent to +91 ${phone}</p>
        
        <!-- OTP Display (Demo) -->
        <div style="background: linear-gradient(135deg, #fef3c7 0%, #fde68a 100%); border-radius: 12px; padding: 16px; margin-bottom: 24px; text-align: center">
          <p style="font-size: ${baseFontSize * 0.75}px; color: #92400e; margin: 0 0 8px 0; font-weight: 600">🔐 Demo OTP</p>
          <p style="font-size: ${baseFontSize * 1.75}px; font-weight: 900; color: #92400e; margin: 0; letter-spacing: 4px">${otp}</p>
        </div>
        
        <!-- OTP Inputs -->
        <div style="display: flex; justify-content: center; gap: 12px; margin-bottom: 24px">
          <input id="otp1" type="tel" maxlength="1" class="otp-input" onkeyup="moveToNext(this, 'otp2')" onkeypress="return event.charCode >= 48 && event.charCode <= 57">
          <input id="otp2" type="tel" maxlength="1" class="otp-input" onkeyup="moveToNext(this, 'otp3')" onkeypress="return event.charCode >= 48 && event.charCode <= 57">
          <input id="otp3" type="tel" maxlength="1" class="otp-input" onkeyup="moveToNext(this, 'otp4')" onkeypress="return event.charCode >= 48 && event.charCode <= 57">
          <input id="otp4" type="tel" maxlength="1" class="otp-input" onkeyup="moveToNext(this, 'otp5')" onkeypress="return event.charCode >= 48 && event.charCode <= 57">
          <input id="otp5" type="tel" maxlength="1" class="otp-input" onkeyup="moveToNext(this, 'otp6')" onkeypress="return event.charCode >= 48 && event.charCode <= 57">
          <input id="otp6" type="tel" maxlength="1" class="otp-input" onkeyup="verifyOTPAuto()" onkeypress="return event.charCode >= 48 && event.charCode <= 57">
        </div>

        <button onclick="verifyOTP()" style="width: 100%; padding: 16px; border-radius: 12px; background: ${config.primary_color || defaultConfig.primary_color}; color: ${config.secondary_color || defaultConfig.secondary_color}; font-size: ${baseFontSize * 1.125}px; font-weight: 700; border: none; cursor: pointer; margin-bottom: 16px">
          Verify OTP
        </button>

        <div style="text-align: center">
          <button onclick="resendOTP()" style="color: ${config.primary_color || defaultConfig.primary_color}; font-size: ${baseFontSize * 0.875}px; font-weight: 600; background: none; border: none; cursor: pointer">Resend OTP</button>
        </div>
      `;
      
      document.getElementById('otp1').focus();
    }

    function moveToNext(current, nextId) {
      if (current.value.length === 1 && nextId) {
        document.getElementById(nextId).focus();
      }
    }

    function verifyOTPAuto() {
      const otp1 = document.getElementById('otp1').value;
      const otp2 = document.getElementById('otp2').value;
      const otp3 = document.getElementById('otp3').value;
      const otp4 = document.getElementById('otp4').value;
      const otp5 = document.getElementById('otp5').value;
      const otp6 = document.getElementById('otp6').value;
      
      if (otp1 && otp2 && otp3 && otp4 && otp5 && otp6) {
        setTimeout(() => verifyOTP(), 300);
      }
    }

    function verifyOTP() {
      const otp1 = document.getElementById('otp1').value;
      const otp2 = document.getElementById('otp2').value;
      const otp3 = document.getElementById('otp3').value;
      const otp4 = document.getElementById('otp4').value;
      const otp5 = document.getElementById('otp5').value;
      const otp6 = document.getElementById('otp6').value;
      
      const enteredOTP = otp1 + otp2 + otp3 + otp4 + otp5 + otp6;
      const correctOTP = sessionStorage.getItem('currentOTP');
      
      if (enteredOTP.length !== 6) {
        showToast('Please enter complete OTP');
        return;
      }
      
      if (enteredOTP === correctOTP) {
        sessionStorage.removeItem('currentOTP');
        showLocationInput();
      } else {
        showToast('❌ Wrong OTP! Try again');
        for (let i = 1; i <= 6; i++) {
          document.getElementById('otp' + i).value = '';
        }
        document.getElementById('otp1').focus();
      }
    }

    function resendOTP() {
      const generatedOTP = Math.floor(100000 + Math.random() * 900000).toString();
      sessionStorage.setItem('currentOTP', generatedOTP);
      console.log('🔐 New OTP:', generatedOTP);
      showToast('New OTP sent!');
    }

    function showLocationInput() {
      const loginCard = document.getElementById('login-card');
      const baseFontSize = config.font_size || defaultConfig.font_size;
      
      loginCard.innerHTML = `
        <div style="text-align: center; margin-bottom: 24px">
          <div class="checkmark-animation" style="width: 80px; height: 80px; border-radius: 50%; margin: 0 auto 16px; display: flex; align-items: center; justify-content: center; background: linear-gradient(135deg, ${config.success_color || defaultConfig.success_color} 0%, #059669 100%)">
            <svg width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="3">
              <polyline points="20 6 9 17 4 12"></polyline>
            </svg>
          </div>
          <h2 style="font-size: ${baseFontSize * 1.5}px; font-weight: 700; color: ${config.text_color || defaultConfig.text_color}; margin: 0 0 8px 0">Verified! ✅</h2>
          <p style="font-size: ${baseFontSize * 0.875}px; color: #6b7280; margin: 0">Enter your delivery location</p>
        </div>

        <div style="margin-bottom: 16px">
          <label style="display: block; margin-bottom: 8px; font-size: ${baseFontSize * 0.875}px; font-weight: 600; color: ${config.text_color || defaultConfig.text_color}">📍 Delivery Address</label>
          <textarea 
            id="location-input" 
            rows="3"
            placeholder="House No., Building Name, Area"
            style="width: 100%; padding: 14px 16px; border-radius: 12px; background: #f3f4f6; border: 2px solid transparent; font-size: ${baseFontSize}px; font-weight: 500; color: ${config.text_color || defaultConfig.text_color}; font-family: inherit; resize: none"
          ></textarea>
        </div>

        <div style="margin-bottom: 16px">
          <label style="display: block; margin-bottom: 8px; font-size: ${baseFontSize * 0.875}px; font-weight: 600; color: ${config.text_color || defaultConfig.text_color}">🏙️ City</label>
          <input 
            id="city-input" 
            type="text"
            placeholder="Enter your city"
            style="width: 100%; padding: 14px 16px; border-radius: 12px; background: #f3f4f6; border: 2px solid transparent; font-size: ${baseFontSize}px; font-weight: 500; color: ${config.text_color || defaultConfig.text_color}"
          >
        </div>

        <div style="margin-bottom: 24px">
          <label style="display: block; margin-bottom: 8px; font-size: ${baseFontSize * 0.875}px; font-weight: 600; color: ${config.text_color || defaultConfig.text_color}">📮 Pincode</label>
          <input 
            id="pincode-input" 
            type="tel"
            maxlength="6"
            placeholder="201301"
            style="width: 100%; padding: 14px 16px; border-radius: 12px; background: #f3f4f6; border: 2px solid transparent; font-size: ${baseFontSize}px; font-weight: 500; color: ${config.text_color || defaultConfig.text_color}"
            onkeypress="return event.charCode >= 48 && event.charCode <= 57"
          >
        </div>

        <button onclick="saveLocation()" style="width: 100%; padding: 16px; border-radius: 12px; background: ${config.primary_color || defaultConfig.primary_color}; color: ${config.secondary_color || defaultConfig.secondary_color}; font-size: ${baseFontSize * 1.125}px; font-weight: 700; border: none; cursor: pointer">
          Start Shopping 🛒
        </button>
      `;
      
      document.getElementById('location-input').focus();
    }

    function saveLocation() {
      const locationInput = document.getElementById('location-input').value.trim();
      const cityInput = document.getElementById('city-input').value.trim();
      const pincodeInput = document.getElementById('pincode-input').value.trim();
      
      if (!locationInput || !cityInput || !pincodeInput) {
        showToast('Please fill all details');
        return;
      }
      
      if (pincodeInput.length !== 6) {
        showToast('Please enter valid 6-digit pincode');
        return;
      }
      
      userLocation = `${locationInput}, ${cityInput} - ${pincodeInput}`;
      isLoggedIn = true;
      
      localStorage.setItem('userPhone', userPhone);
      localStorage.setItem('userLocation', userLocation);
      localStorage.setItem('loginTime', Date.now().toString());
      
      showToast('✅ Welcome to BAZRO!');
      setTimeout(() => {
        renderHomePage();
      }, 500);
    }

    function logout() {
      isLoggedIn = false;
      userPhone = '';
      userLocation = '';
      cartItems = [];
      userProfile = { name: '', photo: '' };
      localStorage.removeItem('userPhone');
      localStorage.removeItem('userLocation');
      localStorage.removeItem('loginTime');
      localStorage.removeItem('userName');
      localStorage.removeItem('userPhoto');
      showLoginScreen();
      showToast('Logged out successfully');
    }

    function openProfileModal() {
      // If not logged in, show login option
      if (!isLoggedIn) {
        const modal = document.createElement('div');
        modal.id = 'profile-modal';
        modal.style.cssText = 'position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); display: flex; align-items: center; justify-content: center; z-index: 1000; padding: 20px';
        
        const baseFontSize = config.font_size || defaultConfig.font_size;
        
        modal.innerHTML = `
          <div style="background: ${config.secondary_color || defaultConfig.secondary_color}; border-radius: 24px; padding: 32px; max-width: 400px; width: 100%; box-shadow: 0 20px 60px rgba(0,0,0,0.3); text-align: center">
            <button onclick="closeProfileModal()" style="position: absolute; top: 16px; right: 16px; background: none; border: none; cursor: pointer; padding: 8px">
              <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="${config.text_color || defaultConfig.text_color}" stroke-width="2">
                <line x1="18" y1="6" x2="6" y2="18"></line>
                <line x1="6" y1="6" x2="18" y2="18"></line>
              </svg>
            </button>
            
            <div style="width: 100px; height: 100px; border-radius: 50%; margin: 0 auto 20px; display: flex; align-items: center; justify-content: center; background: linear-gradient(135deg, ${config.primary_color || defaultConfig.primary_color} 0%, ${config.accent_color || defaultConfig.accent_color} 100%); box-shadow: 0 8px 24px rgba(139, 92, 246, 0.3)">
              <svg width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="${config.secondary_color || defaultConfig.secondary_color}" stroke-width="2">
                <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path>
                <circle cx="12" cy="7" r="4"></circle>
              </svg>
            </div>
            
            <h2 style="font-size: ${baseFontSize * 1.5}px; font-weight: 800; color: ${config.text_color || defaultConfig.text_color}; margin: 0 0 12px 0">Not Logged In</h2>
            <p style="font-size: ${baseFontSize * 0.875}px; color: #6b7280; margin: 0 0 24px 0">Please login to view and edit your profile</p>
            
            <button onclick="closeProfileModal(); showLoginScreen();" style="width: 100%; padding: 16px; border-radius: 12px; background: ${config.primary_color || defaultConfig.primary_color}; color: ${config.secondary_color || defaultConfig.secondary_color}; font-size: ${baseFontSize * 1.125}px; font-weight: 700; border: none; cursor: pointer; margin-bottom: 12px">
              Login Now 🔐
            </button>
            
            <button onclick="closeProfileModal()" style="width: 100%; padding: 12px; border-radius: 12px; background: #f3f4f6; color: ${config.text_color || defaultConfig.text_color}; font-size: ${baseFontSize}px; font-weight: 600; border: none; cursor: pointer">
              Cancel
            </button>
          </div>
        `;
        
        document.body.appendChild(modal);
        return;
      }
      
      // If logged in, show profile modal
      const modal = document.createElement('div');
      modal.id = 'profile-modal';
      modal.style.cssText = 'position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); display: flex; align-items: center; justify-content: center; z-index: 1000; padding: 20px';
      
      const baseFontSize = config.font_size || defaultConfig.font_size;
      
      modal.innerHTML = `
        <div style="background: ${config.secondary_color || defaultConfig.secondary_color}; border-radius: 24px; padding: 32px; max-width: 480px; width: 100%; box-shadow: 0 20px 60px rgba(0,0,0,0.3)">
          <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 24px">
            <h2 style="font-size: ${baseFontSize * 1.5}px; font-weight: 800; color: ${config.text_color || defaultConfig.text_color}; margin: 0">Customer Profile</h2>
            <button onclick="closeProfileModal()" style="background: none; border: none; cursor: pointer; padding: 8px">
              <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="${config.text_color || defaultConfig.text_color}" stroke-width="2">
                <line x1="18" y1="6" x2="6" y2="18"></line>
                <line x1="6" y1="6" x2="18" y2="18"></line>
              </svg>
            </button>
          </div>

          <!-- Profile Photo -->
          <div style="text-align: center; margin-bottom: 24px">
            <div id="profile-photo-display" style="width: 120px; height: 120px; border-radius: 50%; margin: 0 auto 16px; display: flex; align-items: center; justify-content: center; background: linear-gradient(135deg, ${config.primary_color || defaultConfig.primary_color} 0%, ${config.accent_color || defaultConfig.accent_color} 100%); box-shadow: 0 8px 24px rgba(139, 92, 246, 0.3); overflow: hidden; position: relative">
              ${userProfile.photo ? `<img src="${userProfile.photo}" style="width: 100%; height: 100%; object-cover">` : `<span style="font-size: 56px; color: ${config.secondary_color || defaultConfig.secondary_color}">👤</span>`}
            </div>
            <input type="file" id="photo-upload" accept="image/*" style="display: none" onchange="handlePhotoUpload(event)">
            <button onclick="document.getElementById('photo-upload').click()" style="padding: 10px 24px; border-radius: 12px; background: ${config.primary_color || defaultConfig.primary_color}; color: ${config.secondary_color || defaultConfig.secondary_color}; font-size: ${baseFontSize * 0.875}px; font-weight: 700; border: none; cursor: pointer; margin-bottom: 8px">
              📷 Upload Photo
            </button>
            ${userProfile.photo ? `<button onclick="removePhoto()" style="display: block; margin: 8px auto 0; padding: 8px 16px; border-radius: 10px; background: #ef4444; color: white; font-size: ${baseFontSize * 0.75}px; font-weight: 600; border: none; cursor: pointer">Remove Photo</button>` : ''}
          </div>

          <!-- Profile Info -->
          <div style="margin-bottom: 16px">
            <label style="display: block; margin-bottom: 8px; font-size: ${baseFontSize * 0.875}px; font-weight: 600; color: ${config.text_color || defaultConfig.text_color}">👤 Full Name</label>
            <input 
              id="profile-name-input" 
              type="text"
              value="${userProfile.name}"
              placeholder="Enter your name"
              style="width: 100%; padding: 14px 16px; border-radius: 12px; background: #f3f4f6; border: 2px solid transparent; font-size: ${baseFontSize}px; font-weight: 500; color: ${config.text_color || defaultConfig.text_color}"
            >
          </div>

          <div style="margin-bottom: 16px">
            <label style="display: block; margin-bottom: 8px; font-size: ${baseFontSize * 0.875}px; font-weight: 600; color: ${config.text_color || defaultConfig.text_color}">📱 Phone Number</label>
            <input 
              type="text"
              value="+91 ${userPhone}"
              disabled
              style="width: 100%; padding: 14px 16px; border-radius: 12px; background: #e5e7eb; border: 2px solid transparent; font-size: ${baseFontSize}px; font-weight: 500; color: #9ca3af"
            >
          </div>

          <div style="margin-bottom: 24px">
            <label style="display: block; margin-bottom: 8px; font-size: ${baseFontSize * 0.875}px; font-weight: 600; color: ${config.text_color || defaultConfig.text_color}">📍 Location</label>
            <textarea 
              disabled
              rows="2"
              style="width: 100%; padding: 14px 16px; border-radius: 12px; background: #e5e7eb; border: 2px solid transparent; font-size: ${baseFontSize * 0.875}px; font-weight: 500; color: #9ca3af; font-family: inherit; resize: none"
            >${userLocation}</textarea>
          </div>

          <button onclick="saveProfile()" style="width: 100%; padding: 16px; border-radius: 12px; background: ${config.primary_color || defaultConfig.primary_color}; color: ${config.secondary_color || defaultConfig.secondary_color}; font-size: ${baseFontSize * 1.125}px; font-weight: 700; border: none; cursor: pointer">
            Save Profile
          </button>
        </div>
      `;
      
      document.body.appendChild(modal);
    }

    function closeProfileModal() {
      const modal = document.getElementById('profile-modal');
      if (modal) modal.remove();
    }

    function handlePhotoUpload(event) {
      const file = event.target.files[0];
      if (file) {
        if (file.size > 5 * 1024 * 1024) {
          showToast('❌ Photo size must be less than 5MB');
          return;
        }
        
        const reader = new FileReader();
        reader.onload = function(e) {
          const photoDisplay = document.getElementById('profile-photo-display');
          if (photoDisplay) {
            photoDisplay.innerHTML = `<img src="${e.target.result}" style="width: 100%; height: 100%; object-cover">`;
            userProfile.photo = e.target.result;
          }
          closeProfileModal();
          setTimeout(() => openProfileModal(), 100);
        };
        reader.readAsDataURL(file);
      }
    }

    function removePhoto() {
      userProfile.photo = '';
      localStorage.removeItem('userPhoto');
      closeProfileModal();
      setTimeout(() => openProfileModal(), 100);
      showToast('Photo removed successfully');
    }

    function saveProfile() {
      const nameInput = document.getElementById('profile-name-input');
      const name = nameInput.value.trim();
      
      if (!name) {
        showToast('Please enter your name');
        return;
      }
      
      userProfile.name = name;
      localStorage.setItem('userName', name);
      
      if (userProfile.photo) {
        localStorage.setItem('userPhoto', userProfile.photo);
      }
      
      closeProfileModal();
      showToast('✅ Profile saved successfully!');
      
      // Update header if on home page
      const profileButton = document.querySelector('.profile-button');
      if (profileButton) {
        renderHomePage();
      }
    }

    function addToCart(item) {
      const existing = cartItems.find(ci => ci.id === item.id);
      if (existing) {
        existing.quantity += 1;
      } else {
        cartItems.push({ ...item, quantity: 1 });
      }
      updateCartBadge();
      showToast(`${item.name} added to cart!`);
    }

    function updateCartBadge() {
      const badge = document.querySelector('.cart-badge');
      const totalItems = cartItems.reduce((sum, item) => sum + item.quantity, 0);
      if (badge) {
        badge.textContent = totalItems;
        badge.style.display = totalItems > 0 ? 'flex' : 'none';
      }
    }

    const categories = [
      { id: 1, name: 'Vegetables', emoji: '🥬', color: '#dcfce7' },
      { id: 2, name: 'Fruits', emoji: '🍎', color: '#fee2e2' },
      { id: 3, name: 'Dairy', emoji: '🥛', color: '#dbeafe' },
      { id: 4, name: 'Snacks', emoji: '🍿', color: '#fef3c7' },
      { id: 5, name: 'Beverages', emoji: '🥤', color: '#fce7f3' },
      { id: 6, name: 'Bakery', emoji: '🍞', color: '#fed7aa' },
      { id: 7, name: 'Personal Care', emoji: '💄', color: '#e9d5ff' },
      { id: 8, name: 'Household', emoji: '🧹', color: '#d1fae5' }
    ];

    const products = [
      { id: 1, name: 'Fresh Tomatoes', weight: '500g', price: 30, mrp: 50, discount: '40% OFF', category: 'Vegetables', image: 'https://images.unsplash.com/photo-1546094096-0df4bcaaa337?w=300&h=300&fit=crop' },
      { id: 2, name: 'Fresh Apples', weight: '1kg', price: 120, mrp: 180, discount: '33% OFF', category: 'Fruits', image: 'https://images.unsplash.com/photo-1568702846914-96b305d2aaeb?w=300&h=300&fit=crop' },
      { id: 3, name: 'Milk', weight: '1L', price: 60, mrp: 70, discount: '14% OFF', category: 'Dairy', image: 'https://images.unsplash.com/photo-1550583724-b2692b85b150?w=300&h=300&fit=crop' },
      { id: 4, name: 'Lays Chips', weight: '200g', price: 50, mrp: 70, discount: '29% OFF', category: 'Snacks', image: 'https://images.unsplash.com/photo-1566478989037-eec170784d0b?w=300&h=300&fit=crop' },
      { id: 5, name: 'Coca Cola', weight: '2L', price: 90, mrp: 110, discount: '18% OFF', category: 'Beverages', image: 'https://images.unsplash.com/photo-1554866585-cd94860890b7?w=300&h=300&fit=crop' },
      { id: 6, name: 'White Bread', weight: '400g', price: 40, mrp: 55, discount: '27% OFF', category: 'Bakery', image: 'https://images.unsplash.com/photo-1509440159596-0249088772ff?w=300&h=300&fit=crop' },
      { id: 7, name: 'Dove Soap', weight: '125g', price: 45, mrp: 60, discount: '25% OFF', category: 'Personal Care', image: 'https://images.unsplash.com/photo-1598440947619-2c35fc9aa908?w=300&h=300&fit=crop' },
      { id: 8, name: 'Detergent', weight: '1kg', price: 180, mrp: 250, discount: '28% OFF', category: 'Household', image: 'https://images.unsplash.com/photo-1610557892470-55d9e80c0bce?w=300&h=300&fit=crop' }
    ];

    function renderHomePage() {
      const app = document.getElementById('app');
      const customFont = config.font_family || defaultConfig.font_family;
      const baseFontSize = config.font_size || defaultConfig.font_size;
      
      app.innerHTML = `
        <div style="width: 100%; min-height: 100%; font-family: ${customFont}, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; font-size: ${baseFontSize}px; background: #fafafa; padding-bottom: 80px">
          
          <!-- Header -->
          <header style="background: linear-gradient(135deg, ${config.primary_color || defaultConfig.primary_color} 0%, ${config.accent_color || defaultConfig.accent_color} 100%); box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1); position: sticky; top: 0; z-index: 100">
            <div style="max-width: 1200px; margin: 0 auto; padding: 16px 20px">
              
              <!-- Top Bar -->
              <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px">
                <div style="display: flex; align-items: center; gap: 12px">
                  <div style="font-size: ${baseFontSize * 1.75}px; font-weight: 900; color: ${config.secondary_color || defaultConfig.secondary_color}; letter-spacing: 2px">${config.app_name || defaultConfig.app_name}</div>
                  <p style="font-size: ${baseFontSize * 0.75}px; font-weight: 600; color: ${config.secondary_color || defaultConfig.secondary_color}; opacity: 0.9; margin: 0">${config.tagline || defaultConfig.tagline}</p>
                </div>
                <div style="display: flex; align-items: center; gap: 16px">
                  <button onclick="openProfileModal()" class="profile-button" style="width: 40px; height: 40px; border-radius: 50%; background: ${config.secondary_color || defaultConfig.secondary_color}; border: 2px solid ${config.secondary_color || defaultConfig.secondary_color}; cursor: pointer; overflow: hidden; display: flex; align-items: center; justify-content: center; box-shadow: 0 2px 8px rgba(0,0,0,0.15)">
                    ${userProfile.photo ? `<img src="${userProfile.photo}" style="width: 100%; height: 100%; object-cover">` : `<svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="${config.primary_color || defaultConfig.primary_color}" stroke-width="2">
                      <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path>
                      <circle cx="12" cy="7" r="4"></circle>
                    </svg>`}
                  </button>
                  <button onclick="openCart()" style="position: relative; background: none; border: none; cursor: pointer">
                    <svg width="28" height="28" viewBox="0 0 24 24" fill="none" stroke="${config.secondary_color || defaultConfig.secondary_color}" stroke-width="2">
                      <circle cx="9" cy="21" r="1"></circle>
                      <circle cx="20" cy="21" r="1"></circle>
                      <path d="M1 1h4l2.68 13.39a2 2 0 0 0 2 1.61h9.72a2 2 0 0 0 2-1.61L23 6H6"></path>
                    </svg>
                    <span class="cart-badge" style="position: absolute; top: -4px; right: -4px; width: 20px; height: 20px; border-radius: 50%; background: #ef4444; color: white; font-size: 11px; font-weight: 700; display: none; align-items: center; justify-content: center">0</span>
                  </button>
                </div>
              </div>
              
              <!-- Location -->
              <button style="display: flex; align-items: center; gap: 8px; margin-bottom: 16px; padding: 10px 12px; border-radius: 10px; background: rgba(255, 255, 255, 0.2); border: none; cursor: pointer">
                <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="${config.secondary_color || defaultConfig.secondary_color}" stroke-width="2">
                  <path d="M21 10c0 7-9 13-9 13s-9-6-9-13a9 9 0 0 1 18 0z"></path>
                  <circle cx="12" cy="10" r="3"></circle>
                </svg>
                <span style="font-size: ${baseFontSize * 0.875}px; font-weight: 600; color: ${config.secondary_color || defaultConfig.secondary_color}; flex: 1; text-align: left">${userLocation}</span>
                <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="${config.secondary_color || defaultConfig.secondary_color}" stroke-width="2">
                  <polyline points="6 9 12 15 18 9"></polyline>
                </svg>
              </button>
              
              <!-- Search -->
              <div style="display: flex; align-items: center; gap: 12px; padding: 12px 16px; border-radius: 12px; background: ${config.secondary_color || defaultConfig.secondary_color}">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="#9ca3af" stroke-width="2">
                  <circle cx="11" cy="11" r="8"></circle>
                  <path d="m21 21-4.35-4.35"></path>
                </svg>
                <input type="text" placeholder="Search for products..." style="flex: 1; background: transparent; border: none; outline: none; font-size: ${baseFontSize}px; font-weight: 500; color: ${config.text_color || defaultConfig.text_color}">
                <button style="padding: 8px; background: none; border: none; cursor: pointer">
                  <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="#9ca3af" stroke-width="2">
                    <path d="M12 1a3 3 0 0 0-3 3v8a3 3 0 0 0 6 0V4a3 3 0 0 0-3-3z"></path>
                    <path d="M19 10v2a7 7 0 0 1-14 0v-2"></path>
                  </svg>
                </button>
              </div>
            </div>
          </header>

          <!-- Main Content -->
          <main style="max-width: 1200px; margin: 0 auto; padding: 20px">
            
            <!-- BIG SALE Banner -->
            <div class="slide-up" style="background: linear-gradient(135deg, #e9d5ff 0%, #f3e8ff 50%, #fae8ff 100%); border-radius: 20px; padding: 20px; text-align: center; margin-bottom: 20px; position: relative; overflow: hidden; box-shadow: 0 4px 12px rgba(124, 58, 237, 0.2)">
              <h2 style="font-size: ${baseFontSize * 1.75}px; font-weight: 900; letter-spacing: 2px; margin: 0 0 8px 0; color: white; text-shadow: 2px 2px 6px rgba(0, 0, 0, 0.4)">BIG SALE</h2>
              <p style="font-size: ${baseFontSize * 0.875}px; font-weight: 600; margin: 0 0 12px 0; color: ${config.text_color || defaultConfig.text_color}">The Biggest Indian Grocery Sale</p>
              <div style="display: inline-block; padding: 6px 16px; border-radius: 50px; font-size: ${baseFontSize * 0.75}px; font-weight: 700; background: white; color: ${config.primary_color || defaultConfig.primary_color}; box-shadow: 0 2px 8px rgba(124, 58, 237, 0.2); margin-bottom: 12px">30th Nov �� 6th Dec</div>
              <div style="background: linear-gradient(135deg, #5b21b6 0%, #6b21a8 100%); border-radius: 12px; padding: 12px; box-shadow: 0 4px 12px rgba(91, 33, 182, 0.3)">
                <h3 style="font-size: ${baseFontSize * 1.125}px; font-weight: 900; color: #fbbf24; margin: 0 0 8px 0">₹0 FEES</h3>
                <div style="display: flex; flex-wrap: wrap; gap: 6px; justify-content: center; font-size: ${baseFontSize * 0.75}px">
                  <div style="display: flex; align-items: center; gap: 4px; color: white; font-weight: 600">
                    <span style="width: 16px; height: 16px; background: #fbbf24; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 10px; color: #5b21b6; font-weight: 900">✓</span>
                    <span>₹0 Handling</span>
                  </div>
                  <span style="color: rgba(255,255,255,0.5)">•</span>
                  <div style="display: flex; align-items: center; gap: 4px; color: white; font-weight: 600">
                    <span style="width: 16px; height: 16px; background: #fbbf24; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 10px; color: #5b21b6; font-weight: 900">✓</span>
                    <span>₹0 Surge</span>
                  </div>
                  <span style="color: rgba(255,255,255,0.5)">•</span>
                  <div style="display: flex; align-items: center; gap: 4px; color: white; font-weight: 600">
                    <span style="width: 16px; height: 16px; background: #fbbf24; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 10px; color: #5b21b6; font-weight: 900">✓</span>
                    <span>₹0 Delivery above ₹99</span>
                  </div>
                </div>
              </div>
            </div>
            
            <!-- Top Categories -->
            <div class="scroll-smooth" style="display: flex; gap: 12px; overflow-x: auto; padding-bottom: 16px; margin-bottom: 24px">
              ${categories.map(cat => `
                <button class="category-chip" style="flex-shrink: 0; display: flex; flex-direction: column; align-items: center; gap: 8px; padding: 16px 20px; border-radius: 16px; background: ${config.secondary_color || defaultConfig.secondary_color}; border: none; cursor: pointer; min-width: 80px; box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06)">
                  <div style="font-size: 32px">${cat.emoji}</div>
                  <span style="font-size: ${baseFontSize * 0.75}px; font-weight: 700; color: ${config.text_color || defaultConfig.text_color}; text-align: center">${cat.name}</span>
                </button>
              `).join('')}
            </div>

            <!-- Promo Banner -->
            <div style="background: linear-gradient(135deg, #fef3c7 0%, #fde68a 100%); border-radius: 20px; padding: 24px; margin-bottom: 24px; display: flex; align-items: center; justify-content: space-between; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08)">
              <div style="flex: 1">
                <h2 style="font-size: ${baseFontSize * 1.5}px; font-weight: 900; color: ${config.text_color || defaultConfig.text_color}; margin: 0 0 8px 0">${config.hero_title || defaultConfig.hero_title}</h2>
                <p style="font-size: ${baseFontSize * 1.125}px; font-weight: 700; color: #f59e0b; margin: 0 0 16px 0">${config.hero_subtitle || defaultConfig.hero_subtitle}</p>
                <button style="padding: 10px 24px; border-radius: 50px; background: ${config.primary_color || defaultConfig.primary_color}; color: ${config.secondary_color || defaultConfig.secondary_color}; font-size: ${baseFontSize * 0.875}px; font-weight: 700; border: none; cursor: pointer">
                  SHOP NOW
                </button>
              </div>
              <div style="display: flex; gap: 8px">
                <img src="https://images.unsplash.com/photo-1610832958506-aa56368176cf?w=200&h=200&fit=crop" alt="Product" style="width: 80px; height: 80px; border-radius: 16px; object-cover" onerror="this.style.display='none'">
                <img src="https://images.unsplash.com/photo-1574932027991-32c566bfa527?w=200&h=200&fit=crop" alt="Product" style="width: 80px; height: 80px; border-radius: 16px; object-cover" onerror="this.style.display='none'">
              </div>
            </div>

            <!-- Zero Fee Strip -->
            <div style="background: linear-gradient(90deg, ${config.success_color || defaultConfig.success_color} 0%, #059669 100%); border-radius: 16px; padding: 16px; margin-bottom: 24px; display: flex; align-items: center; gap: 16px; box-shadow: 0 4px 12px rgba(16, 185, 129, 0.3)">
              <div style="display: flex; gap: -8px">
                <img src="https://images.unsplash.com/photo-1610832958506-aa56368176cf?w=200&h=200&fit=crop" alt="" style="width: 48px; height: 48px; border-radius: 50%; border: 2px solid white; object-cover" onerror="this.style.display='none'">
                <img src="https://images.unsplash.com/photo-1574932027991-32c566bfa527?w=200&h=200&fit=crop" alt="" style="width: 48px; height: 48px; border-radius: 50%; border: 2px solid white; object-cover; margin-left: -12px" onerror="this.style.display='none'">
              </div>
              <p style="flex: 1; font-size: ${baseFontSize * 0.875}px; font-weight: 700; color: ${config.secondary_color || defaultConfig.secondary_color}; margin: 0">₹0 Delivery Fee on orders above ₹99</p>
              <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="${config.secondary_color || defaultConfig.secondary_color}" stroke-width="2">
                <polyline points="9 18 15 12 9 6"></polyline>
              </svg>
            </div>

            <!-- Products Grid -->
            <div>
              <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px">
                <h3 style="font-size: ${baseFontSize * 1.25}px; font-weight: 800; color: ${config.text_color || defaultConfig.text_color}; margin: 0">🛒 All Products</h3>
                <span style="font-size: ${baseFontSize * 0.875}px; font-weight: 600; color: #9ca3af">${products.length} items</span>
              </div>
              <div style="display: grid; grid-template-columns: repeat(2, 1fr); gap: 16px; margin-bottom: 40px">
                ${products.map(product => `
                  <div class="product-card" style="background: ${config.secondary_color || defaultConfig.secondary_color}; border-radius: 16px; overflow: hidden; box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06)">
                    <div style="position: relative; height: 160px; display: flex; align-items: center; justify-content: center; overflow: hidden; background: ${product.category === 'Vegetables' ? '#dcfce7' : product.category === 'Fruits' ? '#fee2e2' : product.category === 'Dairy' ? '#dbeafe' : '#fef3c7'}">
                      <img src="${product.image}" alt="${product.name}" style="width: 100%; height: 100%; object-cover" onerror="this.style.display='none'; this.nextElementSibling.style.display='flex'">
                      <div style="display: none; font-size: 64px">📦</div>
                      <span class="discount-badge" style="position: absolute; top: 8px; right: 8px; padding: 4px 8px; border-radius: 20px; font-size: ${baseFontSize * 0.75}px; font-weight: 700; color: white; z-index: 10">
                        ${product.discount}
                      </span>
                    </div>
                    <div style="padding: 12px">
                      <p style="font-size: ${baseFontSize * 0.75}px; font-weight: 600; color: #9ca3af; margin: 0 0 4px 0">${product.category}</p>
                      <h4 style="font-size: ${baseFontSize * 0.875}px; font-weight: 700; color: ${config.text_color || defaultConfig.text_color}; margin: 0 0 4px 0; line-height: 1.3">${product.name}</h4>
                      <p style="font-size: ${baseFontSize * 0.75}px; color: #6b7280; margin: 0 0 8px 0">${product.weight}</p>
                      <div style="display: flex; align-items: center; gap: 8px; margin-bottom: 12px">
                        <span style="font-size: ${baseFontSize}px; font-weight: 900; color: ${config.primary_color || defaultConfig.primary_color}">₹${product.price}</span>
                        <span style="font-size: ${baseFontSize * 0.75}px; color: #9ca3af; text-decoration: line-through">₹${product.mrp}</span>
                      </div>
                      <button onclick="addToCart({id: ${product.id}, name: '${product.name}', price: ${product.price}})" style="width: 100%; padding: 10px; border-radius: 10px; background: ${config.primary_color || defaultConfig.primary_color}; color: ${config.secondary_color || defaultConfig.secondary_color}; font-size: ${baseFontSize * 0.875}px; font-weight: 700; border: none; cursor: pointer">
                        Add to Cart
                      </button>
                    </div>
                  </div>
                `).join('')}
              </div>
            </div>
          </main>

          <!-- Bottom Navigation -->
          <nav style="position: fixed; bottom: 0; left: 0; right: 0; background: white; box-shadow: 0 -4px 12px rgba(0, 0, 0, 0.08); z-index: 100">
            <div style="max-width: 1200px; margin: 0 auto; padding: 12px 20px; display: flex; justify-content: space-around; align-items: center">
              <button style="display: flex; flex-direction: column; align-items: center; gap: 4px; background: none; border: none; cursor: pointer">
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="${config.primary_color || defaultConfig.primary_color}" stroke-width="2">
                  <path d="m3 9 9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path>
                  <polyline points="9 22 9 12 15 12 15 22"></polyline>
                </svg>
                <span style="font-size: ${baseFontSize * 0.75}px; font-weight: 600; color: ${config.primary_color || defaultConfig.primary_color}">Home</span>
              </button>
              
              <button style="display: flex; flex-direction: column; align-items: center; gap: 4px; background: none; border: none; cursor: pointer">
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#9ca3af" stroke-width="2">
                  <rect x="3" y="3" width="7" height="7"></rect>
                  <rect x="14" y="3" width="7" height="7"></rect>
                  <rect x="14" y="14" width="7" height="7"></rect>
                  <rect x="3" y="14" width="7" height="7"></rect>
                </svg>
                <span style="font-size: ${baseFontSize * 0.75}px; font-weight: 500; color: #9ca3af">Categories</span>
              </button>
              
              <button style="display: flex; flex-direction: column; align-items: center; gap: 4px; background: none; border: none; cursor: pointer">
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#9ca3af" stroke-width="2">
                  <polyline points="23 6 13.5 15.5 8.5 10.5 1 18"></polyline>
                  <polyline points="17 6 23 6 23 12"></polyline>
                </svg>
                <span style="font-size: ${baseFontSize * 0.75}px; font-weight: 500; color: #9ca3af">Trending</span>
              </button>
              
              <button style="display: flex; flex-direction: column; align-items: center; gap: 4px; background: none; border: none; cursor: pointer">
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#9ca3af" stroke-width="2">
                  <path d="M18 8h1a4 4 0 0 1 0 8h-1"></path>
                  <path d="M2 8h16v9a4 4 0 0 1-4 4H6a4 4 0 0 1-4-4V8z"></path>
                  <line x1="6" y1="1" x2="6" y2="4"></line>
                  <line x1="10" y1="1" x2="10" y2="4"></line>
                  <line x1="14" y1="1" x2="14" y2="4"></line>
                </svg>
                <span style="font-size: ${baseFontSize * 0.75}px; font-weight: 500; color: #9ca3af">Café</span>
              </button>
              
              <button onclick="logout()" style="display: flex; flex-direction: column; align-items: center; gap: 4px; background: none; border: none; cursor: pointer">
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#9ca3af" stroke-width="2">
                  <path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"></path>
                  <polyline points="16 17 21 12 16 7"></polyline>
                  <line x1="21" y1="12" x2="9" y2="12"></line>
                </svg>
                <span style="font-size: ${baseFontSize * 0.75}px; font-weight: 500; color: #9ca3af">Logout</span>
              </button>
            </div>
          </nav>
        </div>
      `;
      
      updateCartBadge();
    }

    function openCart() {
      showToast('Cart feature coming soon!');
    }

    async function onConfigChange(newConfig) {
      config = { ...config, ...newConfig };
      
      const customFont = config.font_family || defaultConfig.font_family;
      const baseFontSize = config.font_size || defaultConfig.font_size;
      
      document.body.style.fontFamily = `${customFont}, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif`;
      document.body.style.fontSize = `${baseFontSize}px`;
      
      // Update all text elements
      const appNameElements = document.querySelectorAll('[data-config="app_name"]');
      appNameElements.forEach(el => {
        el.textContent = config.app_name || defaultConfig.app_name;
      });
      
      const taglineElements = document.querySelectorAll('[data-config="tagline"]');
      taglineElements.forEach(el => {
        el.textContent = config.tagline || defaultConfig.tagline;
      });
      
      const heroTitleElements = document.querySelectorAll('[data-config="hero_title"]');
      heroTitleElements.forEach(el => {
        el.textContent = config.hero_title || defaultConfig.hero_title;
      });
      
      const heroSubtitleElements = document.querySelectorAll('[data-config="hero_subtitle"]');
      heroSubtitleElements.forEach(el => {
        el.textContent = config.hero_subtitle || defaultConfig.hero_subtitle;
      });
      
      const offerBannerElements = document.querySelectorAll('[data-config="offer_banner_text"]');
      offerBannerElements.forEach(el => {
        el.textContent = config.offer_banner_text || defaultConfig.offer_banner_text;
      });
    }

    if (window.elementSdk) {
      window.elementSdk.init({
        defaultConfig,
        onConfigChange,
        mapToCapabilities: (config) => ({
          recolorables: [
            {
              get: () => config.primary_color || defaultConfig.primary_color,
              set: (value) => {
                config.primary_color = value;
                window.elementSdk.setConfig({ primary_color: value });
              }
            },
            {
              get: () => config.secondary_color || defaultConfig.secondary_color,
              set: (value) => {
                config.secondary_color = value;
                window.elementSdk.setConfig({ secondary_color: value });
              }
            },
            {
              get: () => config.accent_color || defaultConfig.accent_color,
              set: (value) => {
                config.accent_color = value;
                window.elementSdk.setConfig({ accent_color: value });
              }
            },
            {
              get: () => config.text_color || defaultConfig.text_color,
              set: (value) => {
                config.text_color = value;
                window.elementSdk.setConfig({ text_color: value });
              }
            },
            {
              get: () => config.success_color || defaultConfig.success_color,
              set: (value) => {
                config.success_color = value;
                window.elementSdk.setConfig({ success_color: value });
              }
            }
          ],
          borderables: [],
          fontEditable: {
            get: () => config.font_family || defaultConfig.font_family,
            set: (value) => {
              config.font_family = value;
              window.elementSdk.setConfig({ font_family: value });
            }
          },
          fontSizeable: {
            get: () => config.font_size || defaultConfig.font_size,
            set: (value) => {
              config.font_size = value;
              window.elementSdk.setConfig({ font_size: value });
            }
          }
        }),
        mapToEditPanelValues: (config) => new Map([
          ["app_name", config.app_name || defaultConfig.app_name],
          ["tagline", config.tagline || defaultConfig.tagline],
          ["hero_title", config.hero_title || defaultConfig.hero_title],
          ["hero_subtitle", config.hero_subtitle || defaultConfig.hero_subtitle],
          ["offer_banner_text", config.offer_banner_text || defaultConfig.offer_banner_text]
        ])
      });
    }

    // Initialize - Always start with login screen
    showLoginScreen();
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9a68bbb8f7dc549c',t:'MTc2NDQ4ODgxMS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
