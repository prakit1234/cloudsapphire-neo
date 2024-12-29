<script>
import { getAuth, signInWithPopup, GoogleAuthProvider, signInWithEmailAndPassword } from 'firebase/auth';
import { auth } from '$lib/firebase';
import { writable } from 'svelte/store';
import { goto } from '$app/navigation';

const error = writable(null);
let email = '';
let password = '';

async function loginWithGoogle() {
  const provider = new GoogleAuthProvider();
  try {
    const result = await signInWithPopup(auth, provider);
    console.log('User  logged in with Google:', result.user);
    goto('/dashboard'); // Redirect to dashboard after successful login
  } catch (err) {
    console.error('Google login error:', err);
    error.set(err.message);
  }
}

async function loginWithEmail() {
  try {
    const userCredential = await signInWithEmailAndPassword(auth, email, password);
    console.log('User  logged in with email:', userCredential.user);
    goto('/dashboard'); // Redirect to dashboard after successful login
  } catch (err) {
    console.error('Email login error:', err);
    error.set(err.message);
  }
}
</script>

<svelte:head>
  <title>Login</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&family=Poppins:wght@600&display=swap" rel="stylesheet">
</svelte:head>

<main>
  <div class="container">
    <img src="/Cloudsapphire-Neo.png" alt="Logo" class="logo" />
    <h1>Login</h1>

    <div class="google-login">
      <button on:click={loginWithGoogle} class="google-button">
        <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSiEndPkpxU-FDOQK0acJ6iuFECTI914xOelQ&s" alt="Google Icon" class="google-icon" />
        Login with Google
      </button>
    </div>

    <form on:submit|preventDefault={loginWithEmail} class="login-form">
      <input type="email" bind:value={email} placeholder="Email" required />
      <input type="password" bind:value={password} placeholder="Password" required />
      <button type="submit" class="submit-button">Login</button>
    </form>

    {#if $error}
      <p class="error">{$error}</p>
    {/if}
  </div>
</main>

<style>
  body {
    margin: 0;
    padding: 0;
    font-family: 'Inter', sans-serif;
    background: url('https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQ4X_We7tZt08Imv0lCYHMLVN-hJbTCnb_bGw&s') no-repeat center center fixed;
    background-size: cover;
    color: #ffffff; /* Light text color */
  }

  main {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    backdrop-filter: blur(5px); /* Optional: adds a blur effect to the background */
  }

  .container {
    background: rgba(0, 0, 0, 0.7); /* Semi-transparent background */
    padding: 40px;
    border-radius: 8px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.5);
    max-width: 400px;
    width: 100%;
    text-align: center;
  }

  .logo {
    width: 100px; /* Adjust as needed */
    margin-bottom: 20px;
  }

  h1 {
    font-family: 'Poppins', sans-serif;
    font-size: 28px;
    margin-bottom: 20px;
    color: #ffffff;
  }

  .google-login {
    margin-bottom: 20px;
  }

  .google-button {
    display: flex;
    align-items: center;
    justify-content: center;
    background: linear-gradient(90deg, #4285F4, #DB4437);
    color: #fff;
    border: none;
    padding: 15px 20px;
    border-radius: 5px;
    font-family: 'Poppins', sans-serif;
    font-size: 18px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    width: 100%;
  }

  .google-button:hover {
    background: linear-gradient(90deg, #357 AE8, #C1351D);
  }

  .google-icon {
    width: 20px; /* Adjust as needed */
    margin-right: 10px;
  }

  .login-form {
    margin-top: 10px;
  }

  .submit-button {
    background: linear-gradient(90deg, #007BFF, #0056b3);
    color: #fff;
    border: none;
    padding: 15px 20px;
    border-radius: 5px;
    font-family: 'Poppins', sans-serif;
    font-size: 18px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    width: 100%;
  }

  .submit-button:hover {
    background: linear-gradient(90deg, #0056b3, #004494);
  }

  input {
    display: block;
    margin: 10px 0;
    padding: 15px;
    width: 100%;
    border: 1px solid #ccc;
    border-radius: 4px;
    font-size: 16px;
    background: rgba(255, 255, 255, 0.2); /* Semi-transparent input background */
    color: #ffffff; /* Light text color for input */
  }

  input::placeholder {
    color: #cccccc; /* Placeholder color */
    font-size: 18px; /* Larger placeholder text */
  }

  input:focus {
    border-color: #007BFF;
    outline: none;
  }

  .error {
    margin-top: 15px;
    color: red;
    font-size: 14px;
  }
</style>