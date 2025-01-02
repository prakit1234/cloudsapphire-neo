<script lang="ts">
  import { supabase } from '$lib/supabase';
  import { writable } from 'svelte/store';

  let file: File | null = null;
  const uploadProgress = writable(0);
  const downloadLink = writable('');
  const errorMessage = writable('');

  const handleFileUpload = async () => {
    errorMessage.set('');
    uploadProgress.set(0);

    if (!file) {
      errorMessage.set('Please select a file to upload.');
      return;
    }

    try {
      // Define the file path
      const filePath = `uploads/${Date.now()}_${file.name}`;

      // Upload the file to Supabase
      const { data, error } = await supabase.storage
        .from('uploads')
        .upload(filePath, file);

      if (error) {
        console.error('Upload Error:', error);
        errorMessage.set(`Failed to upload file: ${error.message}`);
        return;
      }

      // Generate a public URL for the uploaded file
      const { data: publicUrlData, error: publicUrlError } = supabase.storage
        .from('uploads')
        .getPublicUrl(filePath);

      if (publicUrlError) {
        console.error('Public URL Error:', publicUrlError);
        errorMessage.set(`Failed to generate public URL: ${publicUrlError.message}`);
        return;
      }

      // Update the download link
      downloadLink.set(publicUrlData.publicUrl);
      uploadProgress.set(100);
    } catch (err) {
      console.error('Unexpected Error:', err);
      errorMessage.set('An unexpected error occurred.');
    }
  };

  const copyToClipboard = async () => {
    if (!downloadLink) return;
    try {
      await navigator.clipboard.writeText($downloadLink);
      alert('Download link copied to clipboard!');
    } catch (err) {
      console.error('Failed to copy link:', err);
      alert('Failed to copy link. Please try again.');
    }
  };
</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&family=Poppins:wght@400;500;600&display=swap');

  :global(body) {
    margin: 0;
    font-family: 'Inter', sans-serif;
    background: linear-gradient(135deg, #1a1a2e, #16213e);
    color: #fff;
    height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .fullscreen-container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100vh;
    padding: 2rem;
    background: rgba(0, 0, 0, 0.7);
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.6);
    border-radius: 12px;
  }

 .logo {
    width: 120px;
    margin: 20px auto;
    display: block;
  }
  
  h1 {
    font-size: 2rem;
    font-weight: 600;
    text-align: center;
    margin-bottom: 2rem;
    font-family: 'Poppins', sans-serif;
  }

  input[type='file'] {
    display: none;
  }

  label {
    display: inline-block;
    background: linear-gradient(90deg, #6a11cb, #2575fc);
    border: none;
    color: #fff;
    padding: 0.8rem 1.6rem;
    border-radius: 8px;
    cursor: pointer;
    transition: transform 0.2s ease, background 0.3s ease;
    font-family: 'Poppins', sans-serif;
    font-size: 1rem;
    font-weight: 500;
  }

  label:hover {
    transform: scale(1.05);
    background: linear-gradient(90deg, #2575fc, #6a11cb);
  }

  .selected-file {
    margin-top: 1rem;
    font-size: 0.9rem;
    color: #d3d3d3;
    font-family: 'Inter', sans-serif;
    text-align: center;
  }

  button {
    margin-top: 1.5rem;
    background: linear-gradient(90deg, #6a11cb, #2575fc);
    border: none;
    color: #fff;
    padding: 0.8rem 1.6rem;
    border-radius: 8px;
    cursor: pointer;
    transition: transform 0.2s ease, background 0.3s ease;
    font-family: 'Inter', sans-serif;
    font-size: 1rem;
    font-weight: 500;
  }

  button:hover {
    transform: scale(1.05);
    background: linear-gradient(90deg, #2575fc, #6a11cb);
  }

  .progress-bar {
    margin-top: 1.5rem;
    width: 100%;
    max-width: 400px;
    height: 12px;
    background: rgba(255, 255, 255, 0.2);
    border-radius: 6px;
    overflow: hidden;
  }

  .progress {
    height: 100%;
    background: linear-gradient(90deg, #6a11cb, #2575fc);
    transition: width 0.3s;
  }

  .error-message {
    color: #ff4d4d;
    margin-top: 1rem;
    font-size: 1rem;
    font-weight: 500;
    text-align: center;
  }

  .success-message {
    color: #4caf50;
    margin-top: 1rem;
    font-size: 1rem;
    font-weight: 500;
    text-align: center;
  }

  .link-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-top: 1rem;
  }

  a.download-link {
    display: inline-block;
    margin-bottom: 0.5rem;
    text-decoration: none;
    background: linear-gradient(90deg, #2575fc, #6a11cb);
    color: #fff;
    padding: 0.8rem 1.6rem;
    border-radius: 6px;
    font-family: 'Inter', sans-serif;
    font-size: 1rem;
    transition: background 0.3s;
  }

  a.download-link:hover {
    background: linear-gradient(90deg, #6a11cb, #2575fc);
  }

  button.copy-link {
    background: #444;
    border: 1px solid #6a11cb;
    margin-top: 0.5rem;
  }

  button.copy-link:hover {
    background: #6a11cb;
    color: #fff;
  }
</style>

<div class="fullscreen-container">
  <img src="/Cloudsapphire-Neo.png" alt="Logo" class="logo" />
  <h1>Upload Your File</h1>
  
  <label for="file-input">Choose File</label>
  <input id="file-input" type="file" on:change="{(e) => (file = e.target.files[0])}" />
  
  {#if file}
    <p class="selected-file">Selected File: {file.name}</p>
  {/if}

  <button on:click="{handleFileUpload}">Upload</button>

  <div class="progress-bar">
    <div class="progress" style="width: {$uploadProgress}%"></div>
  </div>

  {#if $errorMessage}
    <p class="error-message">{$errorMessage}</p>
  {/if}

  {#if $downloadLink}
    <div class="link-container">
      <p class="success-message">File uploaded successfully!</p>
      <a class="download-link" href="{$downloadLink}" target="_blank" rel="noopener noreferrer">Download File</a>
      <button class="copy-link" on:click="{copyToClipboard}">Copy Link</button>
    </div>
  {/if}
</div>
