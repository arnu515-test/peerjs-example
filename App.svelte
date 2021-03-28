<script lang="ts">
  import Caller from "./components/Caller.svelte";

  let callerId = null;
</script>

{#if callerId === null}
  <p class="w3-center">
    <button on:click={() => callerId = 1} class="w3-button w3-blue w3-margin-right">1</button>
    <button on:click={() => callerId = 2} class="w3-button w3-blue">2</button>
  </p>
{:else}
  <Caller id={callerId} />
{/if}
