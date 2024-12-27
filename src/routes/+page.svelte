<script>
  import { goto } from '$app/navigation'; // Navigation for buttons
</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&family=Poppins:wght@400;600;700&display=swap');

  body {
    margin: 0;
    font-family: 'Inter', 'Poppins', sans-serif;
  }

  .navbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 15px 30px;
    background: linear-gradient(45deg, #6a5acd, #8a2be2);
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
    position: sticky;
    top: 0;
    z-index: 1000;
  }

  .navbar h1 {
    color: white;
    font-size: 1.8rem;
    font-weight: 800;
    margin: 0;
    cursor: pointer;
  }

  .navbar nav {
    display: flex;
    gap: 20px;
  }

  .navbar a {
    color: white;
    font-size: 1rem;
    font-weight: 600;
    text-decoration: none;
    padding: 8px 12px;
    border-radius: 20px;
    transition: background-color 0.3s ease;
  }

  .navbar a:hover {
    background-color: rgba(255, 255, 255, 0.2);
  }

  .home-container {
    text-align: center;
    padding: 40px 20px;
    font-family: 'Poppins', sans-serif;
    background: linear-gradient(135deg, #fdfcfb, #e2d1f9, #b19cd9);
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  header h1 {
    font-size: 3rem;
    color: #4b0082;
    font-weight: 800;
    margin-bottom: 10px;
    text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.2);
  }

  .motto {
    font-size: 1.4rem;
    color: #8b008b;
    margin-bottom: 20px;
    font-weight: 600;
    text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.1);
  }

  .cta button {
    padding: 12px 24px;
    margin: 10px;
    background: linear-gradient(45deg, #6a5acd, #8a2be2);
    color: white;
    border: none;
    border-radius: 25px;
    font-size: 1rem;
    cursor: pointer;
    font-weight: 600;
    transition: transform 0.2s ease, box-shadow 0.2s ease;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
  }

  .cta button:hover {
    transform: scale(1.1);
    box-shadow: 0 6px 8px rgba(0, 0, 0, 0.3);
  }

  .features {
    margin-top: 30px;
    padding: 20px;
    background: rgba(255, 255, 255, 0.9);
    border-radius: 15px;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
    max-width: 900px;
    width: 90%;
    text-align: left;
  }

  .features h2 {
    color: #4b0082;
    margin-bottom: 15px;
    font-size: 2rem;
    font-weight: 700;
  }

  .feature-card {
    margin: 20px auto;
    padding: 15px;
    border: 1px solid #ddd;
    border-radius: 8px;
    background: #f8f8ff;
    max-width: 400px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    transition: transform 0.3s ease, box-shadow 0.3s ease;
  }

  .feature-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
  }

  .faq-cards {
    margin-top: 40px; /* Add margin to separate from features */
  }

  @media (max-width: 768px) {
    .navbar {
      flex-direction: column; /* Stack navbar items vertically on mobile */
      align-items: flex-start; /* Align items to the start */
    }

    .navbar nav {
      flex-direction: column; /* Stack nav items vertically */
      gap: 10px; /* Reduce gap for mobile */
    }

    .home-container {
      padding: 20px; /* Reduce padding for mobile */
    }

    .features, .contact, .faq-cards {
      width: 100%; /* Full width on mobile */
      padding: 10px; /* Reduce padding for mobile */
    }

    .faq-card {
      min-width: 100%; /* Full width for FAQ cards on mobile */
    }
  }

  .faq-card {
    flex: 1;
    min-width: 200px;
    max-width: 300px;
    background: rgba(255, 255, 255, 0.8);
    border-radius: 12px;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
    padding: 15px;
    text-align: left;
    transition: transform 0.3s ease, box-shadow 0.3s ease;
  }

  .faq-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
  }

  .faq-card h3 {
    margin-bottom: 10px;
    color: #4b0082;
    font-size: 1.2rem;
    font-weight: 700;
  }

  footer {
    background: #4b0082;
    color: white;
    padding: 15px;
    text-align: center;
    font-size: 0.9rem;
    margin-top: 40px;
  }

  footer a {
    color: #fdfcfb;
    text-decoration: underline;
  }

  .contact {
    margin-top: 40px;
    padding: 20px;
    background: rgba(255, 255, 255, 0.9);
    border-radius: 15px;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
    max-width: 900px;
    width: 90%;
    text-align: left;
  }

  .contact h2 {
    color: #4b0082;
    margin-bottom: 15px;
    font-size: 2rem;
    font-weight: 700;
  }

  .contact p {
    margin-bottom: 10px;
    font-size: 1rem;
    color: #333;
  }
</style>

<div class="navbar">
  <h1>CloudSapphire-Neo</h1>
  <nav>
    <a href="#features">Features</a>
    <a href="#faq">FAQs</a>
    <a href="#contact">Contact</a>
  </nav>
</div>

<div class="home-container">
  <header>
    <h1>CloudSapphire-Neo</h1>
    <p class="motto">Storage Redefined, Your Way.</p>
  </header>
  <div class="cta">
    <button on:click={() => goto('/login')}>Login</button>
    <button on:click={() => goto('/register')}>Register</button>
  </div>
  <section id="features" class="features">
    <h2>Why Choose CloudSapphire-Neo?</h2>
    <div class="feature-card">
      <h3>Free Cloud Storage</h3>
      <p>Upload and store your files for free with basic features.</p>
    </div>
    <div class="feature-card">
      <h3>Premium Storage</h3>
      <p>Advanced storage options with Supabase for premium users.</p>
    </div>
    <div class="feature-card">
      <h3>File History</h3>
      <p>Keep track of all your uploaded files effortlessly.</p>
    </div>
    <div class="feature-card">
      <h3>Real-Time Upload Progress</h3>
      <p>Monitor file uploads with real-time progress tracking.</p>
    </div>
  </section>

  <section id="faq" class="faq-cards">
    <div class="faq-card">
      <h3>How secure is CloudSapphire-Neo?</h3>
      <p>We use advanced encryption to ensure your files are safe.</p>
    </div>
      <div class="faq-card">
      <h3>What is the file size limit?</h3>
      <p>Free users can upload files up to 10 MB; premium users have no limit.</p>
    </div>
    <div class="faq-card">
      <h3>How to switch to premium?</h3>
      <p>Log in, navigate to the dashboard, and upgrade to premium.</p>
    </div>
  </section>

  <section id="contact" class="contact">
    <h2>Contact Us</h2>
    <p>If you have any questions or need assistance, feel free to reach out to us!</p>
    <p>Email: cloudsapphire.proton.me </p>
    <p>discord: xtro_kai</p>
    <p>Follow us on social media for updates:</p>
    <p>
      <a href="https://twitter.com/teenagerofassam" target="_blank">Twitter</a> | 
      <a href="https://facebook.com/notfound" target="_blank">Facebook</a> | 
      <a href="https://instagram.com/zen_ka.1" target="_blank">Instagram</a>
    </p>
  </section>

  <footer>
    &copy; 2024 Team Reform. All Rights Reserved. | <a href="#contact">Contact</a>
  </footer>
</div>

