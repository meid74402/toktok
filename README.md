<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>خدمة حجز التوكتوك - نظام حجز متطور</title>
  <!-- استيراد خط Tajawal -->
  <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;700&display=swap" rel="stylesheet">
  <!-- استيراد أنماط Leaflet والجيوكودر -->
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css">
  <link rel="stylesheet" href="https://unpkg.com/leaflet-control-geocoder/dist/Control.Geocoder.css">
  <style>
    /* استخدام متغيرات الألوان والخطوط */
    :root {
      --primary-color: #1a1a1a;
      --secondary-color: #16cfc0;
      --accent-color: #ffe680;
      --bg-gradient-start: rgba(255, 243, 176, 0.18);
      --bg-gradient-end: var(--secondary-color);
      --text-color: #1a1a1a;
      --font-family: 'Tajawal', sans-serif;
      --border-radius: 10px;
      --transition-speed: 0.3s;
    }
    /* إعدادات عامة */
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }
    body {
      font-family: var(--font-family);
      background: linear-gradient(135deg, var(--bg-gradient-start) 0%, var(--bg-gradient-end) 100%);
      min-height: 100vh;
      color: var(--text-color);
      line-height: 1.6;
    }
    .container {
      width: 90%;
      max-width: 1200px;
      margin: 0 auto;
      padding: 0 1rem;
    }
    /* الهيدر */
    header {
      background: var(--primary-color);
      color: #fff;
      padding: 2rem 0;
    }
    header h1 {
      font-size: 2.5rem;
      margin-bottom: 0.5rem;
    }
    header p {
      font-size: 1.2rem;
    }
    nav ul {
      list-style: none;
      display: flex;
      justify-content: center;
      gap: 2rem;
      margin-top: 1rem;
    }
    nav a {
      color: #fff;
      text-decoration: none;
      font-weight: 700;
      padding: 0.5rem 1rem;
      border-radius: var(--border-radius);
      transition: background var(--transition-speed);
    }
    nav a:hover {
      background: rgba(255, 255, 255, 0.2);
    }
    /* الأقسام الرئيسية */
    .main-content {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
      gap: 2rem;
      margin: 2rem 0;
      align-items: start;
    }
    /* بطاقة الحجز */
    .booking-card {
      background: #fff;
      border-radius: var(--border-radius);
      padding: 2rem;
      box-shadow: 0 4px 6px rgba(0,0,0,0.1);
      transition: transform var(--transition-speed);
    }
    .booking-card:hover {
      transform: translateY(-5px);
    }
    .booking-card h2 {
      margin-bottom: 1rem;
      font-size: 1.75rem;
    }
    .form-group {
      margin-bottom: 1.5rem;
    }
    .form-group label {
      display: block;
      margin-bottom: 0.5rem;
      font-weight: 700;
    }
    .form-control {
      width: 100%;
      padding: 0.8rem;
      border: 2px solid #e0e0e0;
      border-radius: var(--border-radius);
      font-size: 1rem;
      transition: border-color var(--transition-speed);
    }
    .form-control:focus {
      border-color: var(--primary-color);
      outline: none;
    }
    .btn-primary {
      background: var(--primary-color);
      color: #fff;
      padding: 1rem;
      border: none;
      border-radius: var(--border-radius);
      font-weight: 700;
      cursor: pointer;
      width: 100%;
      transition: opacity var(--transition-speed);
    }
    .btn-primary:hover {
      opacity: 0.9;
    }
    .btn-outline {
      background: transparent;
      border: 2px solid #13e60c;
      color: #e00909;
      padding: 0.8rem;
      border-radius: var(--border-radius);
      cursor: pointer;
      transition: background var(--transition-speed);
    }
    .btn-outline:hover {
      background: #57e007;
      color: var(--primary-color);
    }
    /* الخريطة */
    #map {
      height: 400px;
      border-radius: var(--border-radius);
      box-shadow: 0 4px 6px rgba(0,0,0,0.1);
      position: relative;
    }
    #mapLoading {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      background: rgba(255,255,255,0.8);
      padding: 1rem 2rem;
      border-radius: var(--border-radius);
      display: none;
    }
    /* رسائل التنبيه */
    .alert {
      padding: 1rem;
      border-radius: var(--border-radius);
      margin-top: 1rem;
      text-align: center;
    }
    .alert-success {
      background: #d4edda;
      color: #155724;
      border: 1px solid #c3e6cb;
    }
    .alert-error {
      background: #f8d7da;
      color: #721c24;
      border: 1px solid #f5c6cb;
    }
    .hidden {
      display: none;
    }
    /* الأقسام المساعدة */
    section {
      padding: 3rem 0;
    }
    .section-title {
      text-align: center;
      font-size: 2rem;
      margin-bottom: 1.5rem;
    }
    /* قسم الخدمات */
    .services-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 2rem;
      margin-top: 2rem;
    }
    .service-card {
      background: #fff3b0;
      padding: 1.5rem;
      border-radius: var(--border-radius);
      text-align: center;
      transition: transform var(--transition-speed);
    }
    .service-card:hover {
      transform: translateY(-5px);
    }
    .service-icon {
      font-size: 2.5rem;
      margin-bottom: 1rem;
    }
    .service-title {
      font-size: 1.25rem;
      font-weight: 700;
      margin-bottom: 0.5rem;
    }
    .service-description {
      margin-bottom: 1rem;
    }
    .service-price {
      font-size: 1.5rem;
      font-weight: 700;
    }
    /* قسم التسعير */
    .pricing-section {
      background: var(--primary-color);
      color: #fff;
      border-radius: var(--border-radius);
    }
    .pricing-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 2rem;
      margin-top: 2rem;
    }
    .pricing-card {
      background: rgba(255,255,255,0.1);
      padding: 2rem;
      border-radius: var(--border-radius);
      text-align: center;
      transition: transform var(--transition-speed);
    }
    .pricing-card:hover {
      transform: translateY(-5px);
    }
    .plan-name {
      font-size: 1.5rem;
      font-weight: 700;
      margin-bottom: 1rem;
    }
    .price {
      font-size: 2.5rem;
      font-weight: 700;
      margin: 1rem 0;
    }
    .price span {
      font-size: 0.8rem;
      vertical-align: super;
    }
    .features-list {
      list-style: none;
      padding: 0;
      margin: 1.5rem 0;
    }
    .features-list li {
      padding: 0.5rem 0;
      border-bottom: 1px solid rgba(255,255,255,0.2);
    }
    /* قسم حساب صاحب التوكتوك */
    .owner-section .container {
      background: #f9f9f9;
      border-radius: var(--border-radius);
      padding: 2rem;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }
    /* الفوتر */
    footer {
      background: var(--primary-color);
      color: #fff;
      padding: 2rem 0;
      margin-top: 3rem;
      text-align: center;
      font-size: 0.9rem;
    }
    footer a {
      color: var(--accent-color);
      text-decoration: none;
      margin: 0 0.5rem;
    }
    footer a:hover {
      text-decoration: underline;
    }
    .contact-info a {
      margin: 0 1rem;
      display: inline-block;
    }
    /* استجابة الأجهزة */
    @media (max-width: 768px) {
      header h1 {
        font-size: 2rem;
      }
      .section-title {
        font-size: 1.75rem;
      }
    }
  </style>
</head>
<body>
  <!-- الهيدر -->
  <header>
    <div class="container">
      <h1>MnEt</h1>
      <p>خدمة حجز التوكتوك الأسرع في منطقة كفر الشيخ</p>
      <nav aria-label="القائمة الرئيسية">
        <ul class="nav-links">
          <li><a href="#">الرئيسية</a></li>
          <li><a href="#services">الخدمات</a></li>
          <li><a href="#pricing">التسعير</a></li>
          <li><a href="#contact">اتصل بنا</a></li>
          <li><a href="#owner">حساب صاحب التوكتوك</a></li>
        </ul>
      </nav>
    </div>
  </header>

  <!-- المحتوى الرئيسي -->
  <div class="container">
    <div class="main-content">
      <!-- نموذج الحجز -->
      <div class="booking-card" role="form" aria-labelledby="bookingHeading">
        <h2 id="bookingHeading">احجز رحلتك الآن</h2>
        <form id="bookingForm">
          <div class="form-group">
            <label for="pickup">موقع البداية</label>
            <input type="text" id="pickup" class="form-control" placeholder="حدد موقعك الحالي" required aria-required="true">
          </div>
          <div class="form-group">
            <label for="destination">الوجهة</label>
            <input type="text" id="destination" class="form-control" placeholder="أين تريد الذهاب؟" required aria-required="true">
          </div>
          <!-- حقل رقم الهاتف مع تحقق -->
          <div class="form-group">
            <label for="clientPhone">رقم الهاتف</label>
            <input type="tel" id="clientPhone" class="form-control" placeholder="ادخل رقم هاتفك" pattern="^[0-9]{10,15}$" required aria-required="true">
            <small>يجب أن يكون رقم الهاتف من 10 إلى 15 رقمًا</small>
          </div>
          <button type="submit" class="btn-primary">حجز الآن</button>
        </form>
        <div id="confirmationMessage" class="alert hidden" role="alert"></div>
      </div>
      <!-- الخريطة -->
      <div id="map" aria-label="الخريطة">
        <div id="mapLoading">جاري تحميل الخريطة...</div>
      </div>
    </div>

    <!-- قسم الخدمات -->
    <section class="services-section" id="services">
      <div class="container">
        <h2 class="section-title">خدماتنا المميزة</h2>
        <div class="services-grid">
          <div class="service-card">
            <div class="service-icon" aria-hidden="true">🚀</div>
            <div class="service-title">خدمة سريعة</div>
            <div class="service-description">وصول سريع خلال 5 دقائق</div>
            <div class="service-price">15 جنيه</div>
          </div>
          <div class="service-card">
            <div class="service-icon" aria-hidden="true">🌟</div>
            <div class="service-title">خدمة مميزة</div>
            <div class="service-description">توك توك فاخر مع اغاني</div>
            <div class="service-price">25 جنيه</div>
          </div>
          <div class="service-card">
            <div class="service-icon" aria-hidden="true">📦</div>
            <div class="service-title">نقل البضائع</div>
            <div class="service-description">شحن حتى 100 كجم</div>
            <div class="service-price">35 جنيه</div>
          </div>
        </div>
      </div>
    </section>

    <!-- قسم التسعير -->
    <section class="pricing-section" id="pricing">
      <div class="container">
        <h2 class="section-title">خطط التسعير</h2>
        <div class="pricing-grid">
          <div class="pricing-card">
            <div class="plan-name">أساسي</div>
            <div class="price">10 - 200<span>جنيه</span></div>
            <ul class="features-list">
              <li>مسافة حتى 50 كم</li>
              <li>بدون اغاني</li>
              <li>وقت انتظار 20 دقيقة</li>
            </ul>
            <button class="btn-outline select-plan" data-plan="أساسي" data-price="200" data-features="مسافة حتى 50 كم, بدون تكييف, وقت انتظار 20 دقيقة">اختر الخطة</button>
          </div>
          <div class="pricing-card">
            <div class="plan-name">مميز</div>
            <div class="price">200 - 500<span>جنيه</span></div>
            <ul class="features-list">
              <li>مسافة حتى 100 كم</li>
              <li>اغاني متوفر</li>
              <li>وقت انتظار 30 دقيقة</li>
            </ul>
            <button class="btn-outline select-plan" data-plan="مميز" data-price="500" data-features="مسافة حتى 100 كم, تكييف متوفر, وقت انتظار 30 دقيقة">اختر الخطة</button>
          </div>
          <div class="pricing-card">
            <div class="plan-name">Premium</div>
            <div class="price">500 - 1000<span>جنيه</span></div>
            <ul class="features-list">
              <li>مسافة حتى 200 كم</li>
              <li>اغاني + شحن USB</li>
              <li>وقت انتظار 60 دقيقة</li>
            </ul>
            <button class="btn-outline select-plan" data-plan="Premium" data-price="1000" data-features="مسافة حتى 200 كم, تكييف + شحن USB, وقت انتظار 60 دقيقة">اختر الخطة</button>
          </div>
        </div>
      </div>
    </section>

    <!-- قسم حساب صاحب التوكتوك -->
    <section id="owner" class="owner-section">
      <div class="container">
        <h2 class="section-title">حساب صاحب التوكتوك</h2>
        <div id="owner-auth">
          <h3 id="authTitle">تسجيل حساب جديد</h3>
          <form id="ownerForm">
            <div class="form-group">
              <label for="ownerUsername">USER NAME</label>
              <input type="text" id="ownerUsername" class="form-control" placeholder="ادخل اسم المستخدم" required aria-required="true">
            </div>
            <div class="form-group">
              <label for="ownerPassword">PASSWORD</label>
              <input type="password" id="ownerPassword" class="form-control" placeholder="ادخل كلمة المرور" required aria-required="true">
            </div>
            <button type="submit" class="btn-primary" id="ownerSubmit">تسجيل حساب جديد</button>
          </form>
          <button id="toggleAuth" class="btn-outline" style="margin-top:1rem;">لدي حساب - تسجيل الدخول</button>
        </div>
        <div id="owner-dashboard" class="hidden">
          <h3>لوحة التحكم</h3>
          <p>الحجوزات الواردة:</p>
          <ul id="bookingList"></ul>
        </div>
      </div>
    </section>

    <!-- قسم اتصل بنا -->
    <section id="contact" class="contact-section">
      <div class="container">
        <h2 class="section-title">اتصل بنا</h2>
        <div class="contact-info">
          <p>تواصل معنا عبر الواتساب:</p>
          <a href="https://wa.me/+201017958426" class="btn-outline">+201017958426</a>
          <a href="https://wa.me/+201017917781" class="btn-outline">+201017917781</a>
        </div>
      </div>
    </section>
  </div>

  <!-- الفوتر -->
  <footer>
    <div class="container">
      <p>&copy; 2025 خدمه حجز التوكتوك الأسرع في منطقة كفر الشيخ. جميع الحقوق محفوظة.</p>
      <div>
        <a href="#">سياسة الخصوصية</a>
        <a href="#">شروط الخدمة</a>
      </div>
      <div class="contact-info">
        <p>أرقام الواتساب:</p>
        <a href="https://wa.me/+201017958426">+201017958426</a>
        <a href="https://wa.me/+201017917781">+201017917781</a>
      </div>
    </div>
  </footer>

  <!-- تضمين مكتبات Leaflet والجيوكودر -->
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  <script src="https://unpkg.com/leaflet-control-geocoder/dist/Control.Geocoder.js"></script>
  <script>
    document.addEventListener('DOMContentLoaded', () => {
      // تهيئة الخريطة مع مؤشر التحميل
      const map = L.map('map');
      const mapLoading = document.getElementById('mapLoading');
      mapLoading.style.display = 'block';
      map.setView([30.0444, 31.2357], 13);
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '© OpenStreetMap contributors'
      }).addTo(map);
      map.whenReady(() => {
        mapLoading.style.display = 'none';
      });

      // إضافة متحكم البحث عن العناوين
      const geocoder = L.Control.geocoder({
        defaultMarkGeocode: false,
        placeholder: "ابحث عن موقع..."
      }).addTo(map);

      // تخزين الدبابيس
      let pickupMarker, destinationMarker;
      function addMarker(address, type) {
        geocoder.geocode(address, (results) => {
          if (results.length > 0) {
            const { lat, lng } = results[0].center;
            const icon = L.icon({
              iconUrl: type === 'pickup'
                ? 'https://unpkg.com/leaflet@1.9.4/dist/images/marker-icon.png'
                : 'https://unpkg.com/leaflet@1.9.4/dist/images/marker-icon-red.png',
              iconSize: [25, 41],
              iconAnchor: [12, 41]
            });
            if (type === 'pickup') {
              if (pickupMarker) map.removeLayer(pickupMarker);
              pickupMarker = L.marker([lat, lng], { icon }).addTo(map)
                .bindPopup('موقع البداية')
                .openPopup();
            } else {
              if (destinationMarker) map.removeLayer(destinationMarker);
              destinationMarker = L.marker([lat, lng], { icon }).addTo(map)
                .bindPopup('الوجهة')
                .openPopup();
            }
            if (pickupMarker && destinationMarker) {
              const group = L.featureGroup([pickupMarker, destinationMarker]);
              map.fitBounds(group.getBounds());
            }
          } else {
            displayAlert('لم يتم العثور على الموقع!', 'error');
          }
        });
      }

      // دالة لعرض رسائل التنبيه
      function displayAlert(message, type) {
        const alertDiv = document.getElementById('confirmationMessage');
        alertDiv.className = 'alert';
        alertDiv.textContent = message;
        alertDiv.classList.remove('hidden');
        if (type === 'error') {
          alertDiv.classList.add('alert-error');
          alertDiv.classList.remove('alert-success');
        } else {
          alertDiv.classList.add('alert-success');
          alertDiv.classList.remove('alert-error');
        }
        setTimeout(() => {
          alertDiv.classList.add('hidden');
        }, 5000);
      }

      // إضافة الدبابيس عند انتهاء المستخدم من إدخال العناوين
      document.getElementById('pickup').addEventListener('blur', (e) => {
        const address = e.target.value;
        if (address) addMarker(address, 'pickup');
      });
      document.getElementById('destination').addEventListener('blur', (e) => {
        const address = e.target.value;
        if (address) addMarker(address, 'destination');
      });

      // تخزين بيانات الخطة المختارة
      let selectedPlan = { name: '', price: '', features: [] };
      document.querySelectorAll('.select-plan').forEach(button => {
        button.addEventListener('click', (e) => {
          e.preventDefault();
          const plan = e.target.dataset.plan;
          const price = e.target.dataset.price;
          const features = e.target.dataset.features.split(', ');
          selectedPlan = { name: plan, price: price, features: features };
          displayAlert(`تم اختيار خطة ${plan} بنجاح!`, 'success');
        });
      });

      // معالجة نموذج الحجز مع التحقق من صحة المدخلات
      const bookingForm = document.getElementById('bookingForm');
      bookingForm.addEventListener('submit', (e) => {
        e.preventDefault();
        const pickup = document.getElementById('pickup').value.trim();
        const destination = document.getElementById('destination').value.trim();
        const phone = document.getElementById('clientPhone').value.trim();
        const phonePattern = /^[0-9]{10,15}$/;
        if (!phonePattern.test(phone)) {
          displayAlert('يرجى إدخال رقم هاتف صالح (10-15 رقمًا).', 'error');
          return;
        }
        if (pickup && destination && phone) {
          const message = `
🚗 MnET حجز جديد من توك توك 🚗

موقع البداية: ${pickup}
الوجهة: ${destination}
رقم الهاتف: ${phone}
الخطة المختارة: ${selectedPlan.name || 'غير محددة'}
النطاق السعري: ${selectedPlan.price ? selectedPlan.price + ' جنيه' : 'غير محدد'}
المميزات: ${selectedPlan.features.join(', ') || 'لا يوجد'}
          `;
          const whatsappLink = `https://wa.me/+201017958426?text=${encodeURIComponent(message)}`;
          window.open(whatsappLink, '_blank');
          displayAlert(`تم الحجز بنجاح! الخطة: ${selectedPlan.name || 'غير محددة'} - الحد الأقصى للسعر: ${selectedPlan.price || '0'} جنيه`, 'success');
          bookingForm.reset();
          let bookings = JSON.parse(localStorage.getItem('bookings') || '[]');
          bookings.push({
            pickup,
            destination,
            phone,
            plan: selectedPlan,
            timestamp: new Date().toLocaleString()
          });
          localStorage.setItem('bookings', JSON.stringify(bookings));
          selectedPlan = { name: '', price: '', features: [] };
          if (pickupMarker) { map.removeLayer(pickupMarker); pickupMarker = null; }
          if (destinationMarker) { map.removeLayer(destinationMarker); destinationMarker = null; }
        }
      });

      // نظام تسجيل دخول/تسجيل حساب صاحب التوكتوك
      let isOwnerLoggedIn = false;
      const ownerAuthDiv = document.getElementById('owner-auth');
      const ownerDashboardDiv = document.getElementById('owner-dashboard');
      const ownerForm = document.getElementById('ownerForm');
      const toggleAuthBtn = document.getElementById('toggleAuth');
      const authTitle = document.getElementById('authTitle');
      const ownerSubmitBtn = document.getElementById('ownerSubmit');
      let isLoginMode = false; // false: تسجيل حساب جديد، true: تسجيل دخول

      toggleAuthBtn.addEventListener('click', () => {
        isLoginMode = !isLoginMode;
        if (isLoginMode) {
          authTitle.textContent = 'تسجيل الدخول';
          ownerSubmitBtn.textContent = 'تسجيل الدخول';
          toggleAuthBtn.textContent = 'ليس لدي حساب - تسجيل حساب جديد';
        } else {
          authTitle.textContent = 'تسجيل حساب جديد';
          ownerSubmitBtn.textContent = 'تسجيل حساب جديد';
          toggleAuthBtn.textContent = 'لدي حساب - تسجيل الدخول';
        }
      });

      ownerForm.addEventListener('submit', (e) => {
        e.preventDefault();
        const username = document.getElementById('ownerUsername').value.trim();
        const password = document.getElementById('ownerPassword').value.trim();
        if (!username || !password) {
          displayAlert('يرجى إدخال اسم المستخدم وكلمة المرور.', 'error');
          return;
        }
        if (isLoginMode) {
          const storedOwner = JSON.parse(localStorage.getItem('ownerCredentials'));
          if (storedOwner && storedOwner.username === username && storedOwner.password === password) {
            displayAlert('تم تسجيل الدخول بنجاح!', 'success');
            isOwnerLoggedIn = true;
            ownerAuthDiv.classList.add('hidden');
            ownerDashboardDiv.classList.remove('hidden');
            updateBookingList();
          } else {
            displayAlert('اسم المستخدم أو كلمة المرور غير صحيحة!', 'error');
          }
        } else {
          // تنويه: يجب استخدام طرق تشفير آمنة في بيئة الإنتاج
          localStorage.setItem('ownerCredentials', JSON.stringify({ username, password }));
          displayAlert('تم إنشاء الحساب بنجاح! يمكنك الآن تسجيل الدخول.', 'success');
          isLoginMode = true;
          authTitle.textContent = 'تسجيل الدخول';
          ownerSubmitBtn.textContent = 'تسجيل الدخول';
          toggleAuthBtn.textContent = 'ليس لدي حساب - تسجيل حساب جديد';
        }
        ownerForm.reset();
      });

      // تحديث لوحة تحكم صاحب التوكتوك
      function updateBookingList() {
        const bookingListUl = document.getElementById('bookingList');
        bookingListUl.innerHTML = '';
        const bookings = JSON.parse(localStorage.getItem('bookings') || '[]');
        if (bookings.length === 0) {
          bookingListUl.innerHTML = '<li>لا توجد حجوزات حالياً.</li>';
        } else {
          bookings.forEach((b, index) => {
            const li = document.createElement('li');
            li.style.marginBottom = '1rem';
            li.innerHTML = `
<strong>طلب ${index + 1}</strong>: من ${b.pickup} إلى ${b.destination} <br>
رقم الهاتف: ${b.phone} <br>
الخطة: ${b.plan.name || 'غير محددة'} <br>
${b.timestamp}
            `;
            const contactBtn = document.createElement('button');
            contactBtn.textContent = 'تواصل';
            contactBtn.className = 'btn-outline';
            contactBtn.style.marginTop = '0.5rem';
            contactBtn.addEventListener('click', () => {
              window.open(`https://wa.me/${b.phone}`, '_blank');
            });
            li.appendChild(contactBtn);
            bookingListUl.appendChild(li);
          });
        }
      }
    });
  </script>
</body>
</html>
