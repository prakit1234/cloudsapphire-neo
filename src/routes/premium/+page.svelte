<script>
  import { onMount } from 'svelte';
  import { supabase } from '$lib/supabase';
  import Cookies from 'js-cookie'; 

  let selectedFile = null;
  let downloadLink = '';
  let uploadError = '';
  let fileHistory = [];

  // Handle file selection
  function handleFileSelect(event) {
    selectedFile = event.target.files[0];
    uploadError = '';
    downloadLink = '';
  }

  // Handle file upload
  async function uploadFile() {
    if (!selectedFile) {
      uploadError = 'Please select a file to upload.';
      return;
    }

    const filePath = `uploads/${Date.now()}_${selectedFile.name}`;

    try {
      // Attempt to upload the file
      const { data, error } = await supabase.storage.from('uploads').upload(filePath, selectedFile, {
        cacheControl: '3600',
        upsert: false,
      });

      if (error) {
        console.error('Upload Error:', error);
        uploadError = `Failed to upload file: ${error.message}`;
        return;
      }

      // Attempt to retrieve the public URL
      const { data: publicUrlData, error: publicUrlError } = supabase.storage.from('uploads').getPublicUrl(filePath);

      if (publicUrlError || !publicUrlData?.publicUrl) {
        console.error('Public URL Error:', publicUrlError);
        uploadError = 'Failed to generate download link. Please check your bucket configuration.';
        return;
      }

      downloadLink = publicUrlData.publicUrl;
      console.log('File uploaded successfully:', publicUrlData.publicUrl);

      // Update file history
      updateFileHistory(downloadLink);
    } catch (err) {
      console.error('Unexpected Error:', err);
      uploadError = 'An unexpected error occurred. Please try again later.';
    }
  }

  // Update file history in cookies
  function updateFileHistory(link) {
    fileHistory.push(link);
    Cookies.set('fileHistory', JSON.stringify(fileHistory), { expires: 7 }); // Store for 7 days
  }

  // Load file history from cookies
  onMount(() => {
    const history = Cookies.get('fileHistory');
    if (history) {
      fileHistory = JSON.parse(history);
    }
  });
</script>

<style>
   @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700&family=Poppins:wght@400;600&display=swap');

  :global(body) {
    margin: 0;
    font-family: 'Inter', sans-serif;
    background: linear-gradient(135deg, #001f3f, #0074D9);
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
  }

  .container {
    max-width: 600px;
    margin: auto;
    padding: 30px;
    text-align: center;
    background: rgba(0, 34, 85, 0.8);
    border-radius: 16px;
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.5);
    border: 2px solid #4f8cf5;
  }

  .logo {
    width: 120px;
    margin: 20px auto;
    display: block;
  }

  .file-input {
    margin: 20px 0;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .file-label {
    font-family: 'Poppins', sans-serif;
    font-size: 1.3rem;
    margin-bottom: 15px;
    color: #cce4ff;
  }

  input[type="file"] {
    display: none;
  }

  .custom-file-input {
    background-color: #004080;
    color: white;
    padding: 10px 15px;
    border-radius: 8px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    font-family: 'Poppins', sans-serif;
  }

  .custom-file-input:hover {
    background-color: #0066cc;
  }

  .file-button {
    padding: 12px 24px;
    background: linear-gradient(135deg, #0074D9, #001f3f);
    border: none;
    border-radius: 8px;
    color: white;
    font-size: 1.1rem;
    cursor: pointer;
    font-family: 'Poppins', sans-serif;
    margin-top: 20px;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.3);
  }

  .file-button:hover {
    background: linear-gradient(135deg, #005bb5, #001433);
  }

  .download-link {
    margin-top: 20px;
    font-size: 1rem;
    text-decoration: underline;
    color: #4f8cf5;
    cursor: pointer;
  }

  .error {
    color: #ff6b6b;
    margin-top: 10px;
  }
</style>

<div class="container">
  <img src="/CloudSaphhire-Neo.png" alt="CloudSaphire Neo" class="logo" />
  <h1 style="font-family: 'Poppins', sans-serif; font-size: 2rem; margin-bottom: 20px;">CloudSaphire Neo</h1>
  <p style="font-family: 'Inter', sans-serif; font-size: 1.1rem; margin-bottom: 30px;">Upload your files with no limits of uploads and file size and get a file history!</p>

  <div class="file-input">
    <label for="file" class="file-label">Select a file to upload</label>
    <label for="file" class="custom-file-input">Choose File</label>
    <input type="file" id="file" on:change={handleFileSelect} />
  </div>

  {#if selectedFile}
    <p class="file-label">File selected: {selectedFile.name}</p>
  {/if}

  <button class="file-button" on:click={uploadFile}>Upload</button>

  {#if uploadError}
    <p class="error">{uploadError}</p>
  {/if}

  {#if downloadLink}
    <div>
      <p style="font-family: 'Inter', sans-serif; margin-top: 20px;">Download Link:</p>
      <a href={downloadLink} target="_blank" class="download-link">{downloadLink}</a>
      <button class="file-button" on:click={() => navigator.clipboard.writeText(downloadLink)}>Copy Link</button>
    </div>
  {/if}

  <div>
    <h2 style="font-family: 'Poppins', sans-serif; margin-top: 30px;">File History</h2>
    {#if fileHistory.length > 0}
      <ul>
        {#each fileHistory as link}
          <li>
            <a href={link} target="_blank" class="download-link">{link}</a>
          </li>
        {/each}
      </ul>
    {:else}
      <p>No file history available.</p>
    {/if}
  </div>
</div>
