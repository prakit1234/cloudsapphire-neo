<script>
  import { onMount } from "svelte";
  import { goto } from "$app/navigation";

  // Mock data for user activity (replace with a real database in production)
  let userActivity = JSON.parse(localStorage.getItem("userActivity")) || {
    daysUsed: 0,
    lastAccess: null,
  };

  const updateActivity = () => {
    const today = new Date().toISOString().split("T")[0];
    if (userActivity.lastAccess !== today) {
      userActivity.daysUsed += 1;
      userActivity.lastAccess = today;
      localStorage.setItem("userActivity", JSON.stringify(userActivity));
    }
  };

  onMount(() => {
    updateActivity();
  });

  const handleFreeUpload = () => {
    // Redirect to the local /free route
    goto("/free");
  };

  const handlePremiumUpload = () => {
    if (userActivity.daysUsed >= 5) {
      goto("/premium-upload"); // Redirect to the premium upload page
    } else {
      alert(
        `Premium access is unlocked after 5 days of usage. You have used the app for ${userActivity.daysUsed} days.`
      );
    }
  };
</script>

<style>
  .dashboard {
    font-family: Poppins, sans-serif;
    padding: 2rem;
    text-align: center;
  }

  .option-card {
    border: 1px solid #ccc;
    border-radius: 8px;
    margin: 1rem auto;
    padding: 1.5rem;
    max-width: 400px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  }

  .button {
    margin-top: 1rem;
    padding: 0.5rem 1.5rem;
    border: none;
    border-radius: 4px;
    background-color: #6200ea;
    color: #fff;
    cursor: pointer;
    font-size: 1rem;
  }

  .button:hover {
    background-color: #3700b3;
  }
</style>

<div class="dashboard">
  <h1>Welcome to CloudSapphire Neo</h1>
  <p>Choose your storage option below:</p>

  <div class="option-card">
    <h2>Free Plan</h2>
    <p>Upload files with temporary storage i.e only storage size of 500 mb and only 5 uploads per day.</p>
    <button class="button" on:click={handleFreeUpload}>Upload for Free</button>
  </div>

  <div class="option-card">
    <h2>Premium Plan</h2>
    <p>
      Unlock premium access by using the app for 5 days and this plan has unlimited storage and unlimited uploads<br />
      <strong>You have used the app for {userActivity.daysUsed} days.</strong>
    </p>
    <button class="button" on:click={handlePremiumUpload}>Go Premium</button>
  </div>
</div>
