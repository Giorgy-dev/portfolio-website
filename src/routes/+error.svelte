<script lang="ts">
  // if 404 then go to home automatically
  import { goto } from "$app/navigation";
  import { onMount } from "svelte";
  onMount(() => {
    goto("/");
  });
</script>
