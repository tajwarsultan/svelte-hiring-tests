<script lang="ts">
  import { goto } from '$app/navigation';
  
  export let item;
  
  function navigateToDetail(id:string) {
    // TODO: Implement navigation
    goto(`/items/${id}`);

  }
</script>

<div class="card">
  <h2>{item.title}</h2>
  <p>{item.description}</p>
  <button class="view-details" on:click={() => navigateToDetail(item.id)}>
    View Details →
  </button>
</div>

<style>
  .card {
    background: white;
    border: 1px solid #ddd;
    border-radius: 8px;
    padding: 20px;
  }
  
  h2 {
    font-size: 1.25rem;
    margin-bottom: 0.5rem;
  }
  
  p {
    color: #666;
    margin-bottom: 1rem;
  }
  
  .view-details {
    background: none;
    border: none;
    color: #4169e1;
    cursor: pointer;
    padding: 0;
    font-size: 1rem;
  }
  
  .view-details:hover {
    text-decoration: underline;
  }
</style>