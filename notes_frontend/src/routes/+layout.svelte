<script lang="ts">
  import '../app.css';
  let { children } = $props();
</script>

<main>
  {@render children()}
</main>

<style>
  main {
    display: flex;
    min-height: 100vh;
    background: var(--color-background);
    color: var(--color-text-primary);
  }
</style>
