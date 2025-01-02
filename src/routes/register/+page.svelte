
<script>
import { getAuth, createUserWithEmailAndPassword, updateProfile } from 'firebase/auth';
import { auth } from '$lib/firebase';
import { writable } from 'svelte/store';
import { goto } from '$app/navigation';

const error = writable(null);
let email = '';
let password = '';
let displayName = '';

async function register() {
    try {
        const userCredential = await createUserWithEmailAndPassword(auth, email, password);
        const user = userCredential.user;
        await updateProfile(user, { displayName });
        console.log('User registered:', user);
        goto('/dashboard'); // Redirect to dashboard after successful registration
    } catch (err) {
        console.error('Registration error:', err);
        error.set(err.message);
    }
}
</script>

<svelte:head>
    <title>Register</title>
</svelte:head>

<main>
    <div class="container">
         <img src="/CloudSapphire-Neo.png" alt="Logo" class="logo" />
        <h1>Register</h1>

        <form on:submit|preventDefault={register}>
            <input type="text" bind:value={displayName} placeholder="Display Name" required />
            <input type="email" bind:value={email} placeholder="Email" required />
            <input type="password" bind:value={password} placeholder="Password" required />
            <button type="submit">Register</button>
        </form>

        {#if $error}
            <p class="error">{$error}</p>
        {/if}
    </div>
</main>

<style>
    @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap');

    :global(body) {
        margin: 0;
        font-family: 'Poppins', sans-serif;
        background-color: #121212;
        color: #ffffff;
    }

    main {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
    }

    .container {
        background: #1e1e1e;
        padding: 20px;
        border-radius: 8px;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        max-width: 400px;
        width: 100%;
        text-align: center;
    }

  .logo {
    width: 100px; /* Adjust as needed */
    margin-bottom: 20px;
  }

    h1 {
        font-size: 28px;
        margin-bottom: 20px;
        color: #ffffff;
    }

    form {
        margin-top: 20px;
    }

    input {
        display: block;
        margin: 15px auto;
        padding: 15px;
        width: 100%;
        border: 1px solid #ccc;
        border-radius: 4px;
        font-size: 16px;
        background: #2c2c2c;
        color: #ffffff;
    }

    input::placeholder {
        font-size: 16px;
        color: #b0b0b0;
    }

    input:focus {
        border-color: #007BFF;
        outline: none;
    }

    button {
        background-color: #007BFF;
        color: #ffffff;
        border: none;
        padding: 15px;
        border-radius: 5px;
        font-size: 18px;
        cursor: pointer;
        width: 100%;
        transition: background-color 0.3s ease;
    }

    button:hover {
        background-color: #0056b3;
    }

    .error {
        margin-top: 15px;
        color: red;
        font-size: 14px;
    }
</style>
