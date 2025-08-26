<script lang="ts">
  import emblaCarouselSvelte from "embla-carousel-svelte";
  import Autoplay from "embla-carousel-autoplay";
  import type { EmblaCarouselType } from "embla-carousel";
  import {
    ChevronLeftOutline,
    ChevronRightOutline,
  } from "flowbite-svelte-icons";

  import images from "./carouselData/carouselData.json";
  import type { HTMLImgAttributes } from "svelte/elements";

  let image: HTMLImgAttributes | undefined = $state();
  let selectedIndex = $state(0);

  // ✅ SOLUZIONE: Variabili per gestire lo stato
  let emblaApi: EmblaCarouselType;
  let videoElements: HTMLVideoElement[] = [];
  let isPlaying: boolean[] = images.map(() => false);
  let canScrollPrev = $state(false);
  let canScrollNext = $state(false);

  // ✅ SOLUZIONE: Variabile per tracciare se è la prima inizializzazione
  let isFirstInit = true;
  let lastSelectedIndex = 0;

  const { autoplayEnabled = true, stopAutoplayOnInteraction = true } = $props();

  // ✅ SOLUZIONE: Inizializzazione immagine
  image = images[0];

  // ✅ SOLUZIONE: Plugin autoplay creato una sola volta
  const autoplayPlugin = Autoplay({
    delay: 3000, // Cambiato da 0 a 3000ms per un comportamento più prevedibile
    stopOnInteraction: stopAutoplayOnInteraction,
    stopOnMouseEnter: true,
    playOnInit: autoplayEnabled,
  });

  // ✅ SOLUZIONE: Configurazioni statiche
  const emblaOptions = {
    loop: true,
    dragFree: false,
    containScroll: "trimSnaps" as const,
    skipSnaps: false,
    align: "start" as const,
  };

  const emblaPlugins = autoplayEnabled ? [autoplayPlugin] : [];
  let isAutoplayActive = $state(autoplayEnabled);

  // ✅ SOLUZIONE: Funzioni di navigazione con controllo di esistenza API
  const scrollPrev = () => {
    if (emblaApi && emblaApi.canScrollPrev()) {
      emblaApi.scrollPrev();
    }
  };

  const scrollNext = () => {
    if (emblaApi && emblaApi.canScrollNext()) {
      emblaApi.scrollNext();
    }
  };

  const updateButtons = () => {
    if (!emblaApi) return;
    canScrollPrev = emblaApi.canScrollPrev();
    canScrollNext = emblaApi.canScrollNext();
  };

  // ✅ SOLUZIONE: Gestione centralizzata della selezione
  const updateSelection = (newIndex?: number) => {
    if (!emblaApi) return;

    const currentIndex = newIndex ?? emblaApi.selectedScrollSnap();
    selectedIndex = currentIndex;
    lastSelectedIndex = currentIndex;
    image = images[currentIndex];
    updateButtons();
  };

  // ✅ SOLUZIONE: Inizializzazione migliorata
  const onInit = (embla: EmblaCarouselType) => {
    // Pulisci listener precedenti se esistono
    if (emblaApi) {
      emblaApi.off("select", onSelect);
      emblaApi.off("reInit", onReInit);
    }

    emblaApi = embla;

    // ✅ SOLUZIONE: Solo alla prima inizializzazione vai al primo slide
    if (isFirstInit) {
      isFirstInit = false;
      updateSelection(0);
    } else {
      // ✅ SOLUZIONE: Nelle reinizializzazioni, mantieni l'ultimo indice
      // Usa setTimeout per assicurarsi che Embla sia completamente inizializzato
      setTimeout(() => {
        if (emblaApi && lastSelectedIndex < images.length) {
          emblaApi.scrollTo(lastSelectedIndex, true); // true = istantaneo
          updateSelection(lastSelectedIndex);
        }
      }, 10);
    }

    // Aggiungi listener
    embla.on("select", onSelect);
    embla.on("reInit", onReInit);
  };

  // ✅ SOLUZIONE: Handler per eventi select
  const onSelect = () => {
    updateSelection();
  };

  // ✅ SOLUZIONE: Handler per eventi reInit (quando Embla si reinizializza)
  const onReInit = () => {
    setTimeout(() => {
      if (emblaApi && lastSelectedIndex < images.length) {
        emblaApi.scrollTo(lastSelectedIndex, true);
        updateSelection(lastSelectedIndex);
      }
    }, 10);
  };

  // Gestione eventi video
  const handleVideoPlay = (index: number) => {
    isPlaying[index] = true;
    isPlaying = [...isPlaying];

    if (autoplayPlugin) {
      autoplayPlugin.stop();
      isAutoplayActive = false;
    }
  };

  const handleVideoEnded = (index: number) => {
    isPlaying[index] = false;
    isPlaying = [...isPlaying];

    if (autoplayPlugin && autoplayEnabled) {
      autoplayPlugin.play();
      isAutoplayActive = true;
    }
  };

  const handleVideoPause = (index: number) => {
    isPlaying[index] = false;
    isPlaying = [...isPlaying];
  };

  // ✅ SOLUZIONE: Navigazione diretta migliorata
  const goToSlide = (index: number) => {
    if (emblaApi && index >= 0 && index < images.length) {
      lastSelectedIndex = index;
      emblaApi.scrollTo(index);
    }
  };

  // ✅ SOLUZIONE: Gestione del cleanup quando il componente viene distrutto
  $effect(() => {
    return () => {
      if (emblaApi) {
        emblaApi.off("select", onSelect);
        emblaApi.off("reInit", onReInit);
      }
    };
  });
</script>

<!-- ✅ SOLUZIONE: Configurazione Embla semplificata e statica -->
<div
  class="embla absolute h-screen w-screen"
  use:emblaCarouselSvelte={{
    options: emblaOptions,
    plugins: emblaPlugins,
  }}
  onemblaInit={(evt) => onInit(evt.detail)}
>
  <div class="embla__container h-screen w-screen">
    {#each images as item, index}
      <div class="embla__slide h-screen w-screen">
        {#if item.isVideo}
          <video
            bind:this={videoElements[index]}
            class="h-screen w-screen object-cover"
            autoplay
            muted
            loop
            playsinline
            src={item.src}
            onplay={() => handleVideoPlay(index)}
            onended={() => handleVideoEnded(index)}
            onpause={() => handleVideoPause(index)}
          ></video>
        {:else}
          <img
            alt={item.alt || ""}
            src={item.src}
            class="h-screen w-screen object-cover"
          />
        {/if}
      </div>
    {/each}
  </div>
</div>

<!-- Controlli laterali per navigazione -->
{#if images.length > 1}
  <button
    class="fixed top-1/2 left-6 transform -translate-y-1/2 bg-white/10 hover:bg-white/20 text-[#ffce0a] rounded-full p-3 transition-all duration-300 z-20 backdrop-blur-sm disabled:opacity-50 disabled:cursor-not-allowed"
    onclick={scrollPrev}
    type="button"
    title="Slide precedente"
    aria-label="Slide precedente"
  >
    <ChevronLeftOutline class="w-6 h-6" />
  </button>

  <button
    class="fixed top-1/2 right-6 transform -translate-y-1/2 bg-white/10 hover:bg-white/20 text-[#ffce0a] rounded-full p-3 transition-all duration-300 z-20 backdrop-blur-sm disabled:opacity-50 disabled:cursor-not-allowed"
    onclick={scrollNext}
    type="button"
    title="Slide successiva"
    aria-label="Slide successiva"
  >
    <ChevronRightOutline class="w-6 h-6" />
  </button>
{/if}

<!-- Indicatori slide -->
{#if images.length > 1}
  <div
    class="fixed bottom-6 left-1/2 transform -translate-x-1/2 flex space-x-3 z-20"
  >
    {#each images as _, index}
      <button
        class="w-2 h-2 rounded-full transition-all duration-300 {index ===
        selectedIndex
          ? 'bg-[#ffce0a] w-8'
          : 'bg-[#ffce0a] bg-opacity-50 hover:bg-opacity-80'}"
        onclick={() => goToSlide(index)}
        type="button"
        title={`Vai alla slide ${index + 1}`}
        aria-label={`Vai alla slide ${index + 1}`}
      ></button>
    {/each}
  </div>
{/if}

<div class="h-screen w-screen flex items-end justify-left">
  <div
    class="w-full lg:max-w-[30vw] my-18 mx-4 lg:m-10 z-10! flex flex-col gap-4"
  >
    <h1 class="ibm-plex-mono-medium text-2xl">
      {image?.title || ""}
    </h1>
    <h2 class="ibm-plex-mono-regular stroke-black">
      {image?.alt || ""}
    </h2>
  </div>
</div>

<style>
  .embla {
    overflow: hidden;
  }
  .embla__container {
    display: flex;
  }
  .embla__slide {
    flex: 0 0 100%;
    min-width: 0;
  }
</style>
