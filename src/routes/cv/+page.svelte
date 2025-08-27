<script lang="ts">
  import { Hr } from "flowbite-svelte";
  import aboutJSON from "./about.json";
  import pathJSON from "./path.json";
  import skillsJSON from "./skills.json";

  type ContentItem = {
    isHighlighted?: boolean;
    period?: number[];
    title: string;
    link?: string;
    role?: string;
    sector: string;
  };

  type DataItem = {
    title: string;
    content: ContentItem[];
  };

  const path = pathJSON as DataItem[];
  const skills = skillsJSON;
  const about = aboutJSON;
</script>

<div
  class="flex flex-col lg:flex-row
px-4 lg:px-10 pb-2 gap-10 pt-[25vh]
"
>
  <div class="flex flex-col w-full lg:max-w-1/2 lg:w-fit">
    {#each about as item}
      <h2 class="ibm-plex-mono-medium text-l uppercase py-4">{item.title}</h2>
      <div class="flex flex-row flex-wrap gap-4 pb-4 px-4 items-center">
        <div class="flex flex-col gap-4">
          <p>
            Im a 21y/o product designer based in <a
              target="_blank"
              rel="noopener noreferrer"
              href="https://maps.app.goo.gl/kmHNkEpUKV5xwsE49"
              class="underline ibm-plex-mono-medium">Padua</a
            >. I have developed versatile skills and, with a curious,
            methodical, and detail-oriented approach, I see design as a tool to
            connect aesthetics, functionality, and meaning, while valuing both
            innovation and the creative process.
          </p>
        </div>
        {#each item.langs as lang}
          <p
            class="text-sm bg-[#141414] p-1 px-3 text-[#ffce0a]! border-1 rounded-full w-fit! h-fit!"
          >
            {lang}
          </p>
        {/each}
      </div>
    {/each}
    {#each skills as skill}
      {#if !Array.isArray(skill)}
        <h2 class="ibm-plex-mono-medium text-l uppercase py-4">Hard Skills</h2>
        <div class="flex flex-row flex-wrap gap-4 p-4">
          {#each skill.sw as software}
            <p
              class="text-sm bg-[#ffce0a] p-1 px-3 text-[#141414]! rounded-full"
            >
              {software}
            </p>
          {/each}
          {#each skill.plang as plang}
            <p
              class="text-sm bg-[#141414] p-1 px-3 text-[#ffce0a]! border-1 rounded-full"
            >
              {plang}
            </p>
          {/each}
          {#each skill.fw as fw}
            <p
              class="text-sm bg-[#141414] p-1 px-3 text-[#ffce0a]! border-1 rounded-full"
            >
              {fw}
            </p>
          {/each}
        </div>
      {:else}
        <h2 class="ibm-plex-mono-medium text-l uppercase py-4">Soft Skills</h2>
        <div class="flex flex-col gap-4 p-4">
          {#each skill as subSkill}
            <p>{subSkill}</p>
          {/each}
        </div>
      {/if}
    {/each}
  </div>
  <div class="flex grow"></div>
  <div class="flex flex-col w-full lg:max-w-1/2 lg:w-fit">
    {#each path as item}
      <h2 class="ibm-plex-mono-medium text-l uppercase py-4">{item.title}</h2>
      <div class="flex flex-col gap-4">
        {#each item.content as contentItem}
          <div
            class={"m-2 w-fit text-[#ffce0a] " +
              (contentItem.isHighlighted
                ? " bg-[#ffce0a] p-4 text-[#141414]! rounded-lg"
                : "")}
          >
            {#if contentItem.period}
              <p class="ibm-plex-mono-medium text-sm text-inherit!">
                {contentItem.period[0]}-{contentItem.period[1] || "Present"}
              </p>
            {/if}
            {#if contentItem.link}
              <h1 class="ibm-plex-mono-medium text-xl underline text-inherit!">
                <a
                  target="_blank"
                  rel="noopener noreferrer"
                  href={contentItem.link}
                >
                  {contentItem.title}
                </a>
              </h1>
            {:else}
              <h1 class="ibm-plex-mono-medium text-xl text-inherit!">
                {contentItem.title}
              </h1>
            {/if}
            {#if contentItem.role}
              <h1 class="ibm-plex-mono-medium text-xl text-inherit!">
                {contentItem.role}
              </h1>
            {/if}

            <div class="flex flex-row gap-2">
              <p class="text-inherit!">{contentItem.sector}</p>
            </div>
          </div>
        {/each}
      </div>
      <!-- <Hr class="border-[#ffce0a] border-2 rounded-full" /> -->
    {/each}
  </div>
</div>
