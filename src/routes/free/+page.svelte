<script lang="ts">
  import { writable } from "svelte/store";
  import { getAuth } from "firebase/auth";
  import { supabase } from "$lib/supabase.js";

  const auth = getAuth();

  // State management
  let file: File | null = null;
  const uploadProgress = writable(0);
  const uploadComplete = writable(false);
  const errorMessage = writable("");
  const downloadLink = writable("");

  // Max file size (500MB)
  const MAX_FILE_SIZE = 500 * 1024 * 1024;

  const handleFileUpload = async () => {
    errorMessage.set("");
    uploadComplete.set(false);

    if (!file) {
      errorMessage.set("Please select a file to upload.");
      return;
    }

    // Check file size
    if (file.size > MAX_FILE_SIZE) {
      errorMessage.set("File size exceeds 500 MB. Please choose a smaller file.");
      return;
    }

    try {
      // Get authenticated user
      const user = auth.currentUser;
      if (!user) {
        errorMessage.set("You need to be logged in to upload files.");
        return;
      }

      const userId = user.uid;

      // Define file path
      const filePath = `public/${userId}/${Date.now()}_${file.name}`;

      // Upload the file to Supabase Storage in the 'uploads' bucket
      const { data: uploadData, error: uploadFileError } = await supabase.storage
        .from("uploads") // Ensure the bucket name is 'uploads'
        .upload(filePath, file, {
          cacheControl: '3600',
          upsert: false
        });

      if (uploadFileError) {
        errorMessage.set("Failed to upload file. Please try again.");
        console.error("Upload Error:", uploadFileError);
        return;
      }

      // Get the public URL of the uploaded file
      const { data: publicUrlData, error: publicUrlError } = supabase.storage
        .from("uploads")
        .getPublicUrl(filePath);

      if (publicUrlError) {
        errorMessage.set("Failed to generate a public download link.");
        console.error("Public URL Error:", publicUrlError);
        return;
      }

      const publicUrl = publicUrlData.publicUrl;

      if (!publicUrl) {
        errorMessage.set("Failed to retrieve the public URL.");
        return;
      }

      // Save metadata to the database
      const metadata = {
        user_id: userId,
        file_name: file.name,
        file_url: publicUrl,
        created_at: new Date().toISOString(),
        is_public: true // Indicate the file is public
      };

      const { error: dbError } = await supabase.from("uploads").insert([metadata]);

      if (dbError) {
        errorMessage.set("Failed to save file information.");
        console.error("Database Error:", dbError);
        return;
      }

      // Update UI with success state
      downloadLink.set(publicUrl);
      uploadProgress.set(100);
      uploadComplete.set(true);
      errorMessage.set("");
    } catch (err) {
      console.error("Unexpected Error:", err);
      errorMessage.set("An unexpected error occurred. Please try again.");
    }
  };
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

  .upload-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    padding: 2rem;
    text-align: center;
  }

  .upload-box {
    background: #181818;
    padding: 2rem;
    border-radius: 16px;
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.7);
    max-width: 500px;
    width: 100%;
  }

  .progress-bar-container {
    width: 100%;
    background: #333;
    border-radius: 8px;
    margin: 1rem 0;
  }

  .progress-bar {
    height: 20px;
    background: #00aaff;
    border-radius: 8px;
    width: 0%;
    transition: width 0.4s ease;
  }

  .button {
    margin-top: 1rem;
    padding: 1rem 2rem;
    border: none;
    border-radius: 8px;
    background-color: #00aaff;
    color: #ffffff;
    font-size: 1.2rem;
    font-weight: bold;
    cursor: pointer;
    text-transform: uppercase;
  }

  .button:hover {
    background-color: #0088cc;
  }

  .error-message {
    color: #ff4c4c;
    font-size: 1.2rem;
    margin-top: 1rem;
  }

  .link {
    color: #00ffaa;
    text-decoration: none;
    font-weight: bold;
  }

  .link:hover {
    text-decoration: underline;
  }

  .title {
    font-size: 2rem;
    margin-bottom: 1rem;
    font-weight: bold;
  }

  .subtitle {
    font-size: 1.2rem;
    margin-bottom: 2rem;
    color: #bbbbbb;
  }
</style>

<div class="upload-container">
  <img src="/static/logo.png" alt="Logo" style="max-width: 200px; margin-bottom: 20px;" />
  <div class="upload-box">
    <h1 class="title">Upload Your File</h1>
    <p class="subtitle">Upload files up to 500 MB and get a shareable link.</p>
    <input
      type="file"
      on:change={(e) => {
        const target = e.target as HTMLInputElement;
        file = target.files ? target.files[0] : null;
      }}
    />
    <button class="button" on:click={handleFileUpload}>
      Upload File
    </button>
    {#if $errorMessage}
      <p class="error-message">{$errorMessage}</p>
    {/if}
    <div class="progress-bar-container">
      <div
        class="progress-bar"
        style="width: {$uploadProgress}%"
      ></div>
    </div>
    {#if $uploadComplete}
      <p>
        Upload complete! Your file link: 
        <a href={$downloadLink} class="link" target="_blank">Download File</a>
      </p>
    {/if}
  </div>
</div>
