<script>
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';
  
  // Get the ID from the URL parameter
  $: id = $page.params.id;
  
  // Mock data - in real app would fetch based on ID
  $: item = {
    id,
    title: `Item ${id}`,
    description: `This is the detailed description for item ${id}. The candidate needs to implement proper navigation to get here and return to the home page.`
  };
  
  function goBack() {
    // TODO: Student needs to implement navigation back to home
    // Uncomment and fix this:
    goto('/');
  }
</script>

<div class="container">
  <div class="detail-card">
    <h1>{item.title}</h1>
    <p>{item.description}</p>
    
    <!-- TODO: Student needs to implement back navigation -->
    <button class="back-button" on:click={goBack}>
      Back to Home
    </button>
  </div>
</div>

<style>
  .container {
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
  }
  
  .detail-card {
    background: #fff;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  
  h1 {
    font-size: 1.8rem;
    margin-bottom: 1rem;
  }
  
  p {
    color: #666;
    line-height: 1.6;
    margin-bottom: 1.5rem;
  }
  
  .back-button {
    padding: 8px 16px;
    background: #007bff;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
  
  .back-button:hover {
    background: #0056b3;
  }
</style>