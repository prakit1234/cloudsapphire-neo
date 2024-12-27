<script lang="ts">
  import { onMount } from "svelte";
  import { page } from "$app/stores";
  import { supabase } from "$lib/supabase.js";

  let fileUrl = "";
  let fileName = "";
  let errorMessage = "";

  onMount(async () => {
    try {
      const params = new URLSearchParams($page.url.search);
      const fileParam = params.get("file");

      if (!fileParam) {
        errorMessage = "Invalid or missing file link.";
        return;
      }

      // Decode the file URL passed via query params
      const decodedUrl = decodeURIComponent(fileParam);

      // Fetch file metadata from Supabase (optional but adds security)
      const { data, error } = await supabase
        .from("uploads")
        .select("file_name, file_url")
        .eq("file_url", decodedUrl)
        .single();

      if (error || !data) {
        errorMessage = "File not found or the link is invalid.";
        return;
      }

      fileUrl = data.file_url;
      fileName = data.file_name;
    } catch (err) {
      errorMessage = "An unexpected error occurred. Please try again later.";
    }
  });
</script>

<style>
  @import url("https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&family=Poppins:wght@400;600;800&display=swap");

  :global(body) {
    font-family: "Poppins", "Inter", sans-serif;
    background-color: #101010;
    color: #ffffff;
    margin: 0;
    padding: 0;
  }

  .download-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    padding: 2rem;
    text-align: center;
  }

  .download-box {
    background: #181818;
    padding: 2rem;
    border-radius: 16px;
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.7);
    max-width: 500px;
    width: 100%;
  }

  .title {
    font-size: 2rem;
    font-weight: 800;
    margin-bottom: 1rem;
  }

  .subtitle {
    font-size: 1rem;
    font-weight: 600;
    color: #bbbbbb;
    margin-bottom: 2rem;
  }

  .button {
    padding: 1rem 2rem;
    border: none;
    border-radius: 8px;
    background-color: #00aaff;
    color: #ffffff;
    font-size: 1.2rem;
    font-weight: bold;
    cursor: pointer;
    transition: background-color 0.3s ease;
    text-decoration: none;
    display: inline-block;
    margin-top: 1rem;
  }

  .button:hover {
    background-color: #0088cc;
  }

  .error-message {
    color: #ff4444;
    font-size: 1.2rem;
    margin-top: 1rem;
  }

  .file-info {
    font-size: 1.2rem;
    color: #ffffff;
    margin-bottom: 1rem;
  }

  .logo {
    max-width: 150px;
    margin-bottom: 20px;
  }
</style>


<div class="download-container">
  <img src="/static/logo-dark.png" alt="Logo" class="logo" />

  {#if errorMessage}
    <p class="error-message">{errorMessage}</p>
  {:else if fileUrl}
    <div class="download-box">
      <h1 class="title">Your File is Ready</h1>
      <p class="file-info">{fileName}</p>
      <a href="{fileUrl}" class="button" download>
        Download File
      </a>
      <p class="subtitle">Share this link to allow others to download:</p>
      <p class="file-info">
        <a href="/download?file={encodeURIComponent(fileUrl)}" class="button">
          Share Link
        </a>
      </p>
    </div>
  {/if}
</div>
