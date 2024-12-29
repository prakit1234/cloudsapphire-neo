<script lang="ts">
  import { onMount } from "svelte";

  let fileUrl: string = "";
  let errorMessage: string = "";

  const fetchFileUrl = async (id: string) => {
    try {
      const res = await fetch(`https://your-webhook-url.com/files/${id}`);
      const data = await res.json();
      if (res.ok && data.file_url) {
        fileUrl = data.file_url;
      } else {
        errorMessage = "Invalid download link.";
      }
    } catch (error) {
      errorMessage = "Failed to retrieve the file.";
    }
  };

  onMount(() => {
    const params = new URLSearchParams(window.location.search);
    const id = params.get("id");
    if (id) fetchFileUrl(id);
  });
</script>

<style>
  .container {
    text-align: center;
    padding: 2rem;
    color: white;
    background: linear-gradient(135deg, #2575fc, #6a11cb);
    height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }

  button {
    padding: 10px 20px;
    border: none;
    border-radius: 10px;
    background: linear-gradient(90deg, #ff7eb3, #ff758c);
    color: white;
    font-weight: bold;
    cursor: pointer;
    transition: transform 0.3s;
  }

  button:hover {
    transform: scale(1.05);
  }
</style>

<div class="container">
  {#if errorMessage}
    <h2>{errorMessage}</h2>
  {:else if fileUrl}
    <h2>Your file is ready to download!</h2>
    <a href="{fileUrl}" target="_blank">
      <button>Download Now</button>
    </a>
  {:else}
    <h2>Loading...</h2>
  {/if}
</div>
