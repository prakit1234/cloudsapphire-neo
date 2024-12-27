
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
    console.log('User logged in with Google:', result.user);
    goto('/dashboard'); // Redirect to dashboard after successful login
  } catch (err) {
    console.error('Google login error:', err);
    error.set(err.message);
  }
}

async function loginWithEmail() {
  try {
    const userCredential = await signInWithEmailAndPassword(auth, email, password);
    console.log('User logged in with email:', userCredential.user);
    goto('/dashboard'); // Redirect to dashboard after successful login
  } catch (err) {
    console.error('Email login error:', err);
    error.set(err.message);
  }
}
</script>

<svelte:head>
  <title>Login</title>
</svelte:head>

<main>
  <div class="container">
    <h1>Login</h1>

    <div class="google-login">
      <button on:click={loginWithGoogle}>Login with Google</button>
    </div>

    <form on:submit|preventDefault={loginWithEmail} class="login-form">
      <input type="email" bind:value={email} placeholder="Email" required />
      <input type="password" bind:value={password} placeholder="Password" required />
      <button type="submit">Login</button>
    </form>

    {#if $error}
      <p class="error">{$error}</p>
    {/if}
  </div>
</main>

<style>
  main {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background-color: #f9f9f9;
    font-family: Arial, sans-serif;
  }

  .container {
    background: #fff;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    max-width: 400px;
    width: 100%;
    text-align: center;
  }

  h1 {
    font-size: 24px;
    margin-bottom: 20px;
    color: #333;
  }

  .google-login {
    margin-bottom: 20px;
  }

  button {
    background-color: #007BFF;
    color: #fff;
    border: none;
    padding: 10px 15px;
    border-radius: 5px;
    font-size: 16px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }

  button:hover {
    background-color: #0056b3;
  }

  .login-form {
    margin-top: 10px;
  }

  input {
    display: block;
    margin: 10px 0;
    padding: 10px;
    width: 100%;
    border: 1px solid #ccc;
    border-radius: 4px;
    font-size: 14px;
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
