<!--

WEBSITE MADE WITH SVELTE TS, FLOWBITE, AND TAILWIND CSS
Testing also Claude AI and Copilot for code generation

-->

<script lang="ts">
  import favicon from "$lib/assets/favicon.svg";
  import "../style/main.css";
  import "../app.css";
  let { children } = $props();
</script>

<svelte:head>
  <link rel="icon" href={favicon} />
</svelte:head>

<h1
  class="ibm-plex-mono-medium text-xl absolute top-16 lg:top-10 left-0 px-4 lg:px-10 z-10! flex flex-row items-start gap-4 w-full"
>
  <a href="/" class="underline flex-none">Marco Gorgetti</a>
  <a href="/cv" class="underline flex-none">CV</a>
  <a
    target="_blank"
    rel="noopener noreferrer"
    href="https://www.linkedin.com/in/marco-gorgetti-192053249/"
    class="underline flex-none">In</a
  >
  <a
    target="_blank"
    rel="noopener noreferrer"
    href="https://www.behance.net/marcogorgetti1"
    class="underline flex-none">Behance</a
  >
</h1>
{@render children?.()}
