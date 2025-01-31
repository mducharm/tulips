<script lang="ts">
  import Tailwind from "./Tailwind.svelte";
  import { currentPage } from "./stores/router";
  import Dashboard from "./pages/Dashboard.svelte";
  import Settings from "./pages/Settings.svelte";
  import NewEntry from "./pages/NewEntry.svelte";
  import ViewEntries from "./pages/ViewEntries.svelte";
  import About from "./pages/About.svelte";
  import ServiceWorkerIntegration from "components/ServiceWorkerIntegration.svelte";
  import { onMount } from "svelte";
  import { db } from "./db/Database";
  import { seed } from "./db/seed";

  onMount(async () => {
    let isFirstVisit = localStorage.getItem("visited") !== "true";
    let dbIsEmpty = (await db.entries.count()) === 0;

    // populate db with test data
    if (isFirstVisit && dbIsEmpty) {
      await seed();
      localStorage.setItem("visited", "true");
    }

    // set initial page to dashboard only if data exists
    if (dbIsEmpty) {
      currentPage.set({
        name: "About",
        component: About,
        props: {},
      });
    } else {
      currentPage.set({
        name: "Dashboard",
        component: Dashboard,
        props: {},
      });
    }
  });

  const pages = [
    {
      name: "Settings",
      component: Settings,
      props: {},
    },
    {
      name: "View Entries",
      component: ViewEntries,
      props: {},
    },
    {
      name: "Dashboard",
      component: Dashboard,
      props: {},
    },
    {
      name: "New Entry",
      component: NewEntry,
      props: {},
    },
  ];
</script>

<Tailwind />
<ServiceWorkerIntegration />

<div class="flex md:flex-row-reverse flex-wrap">
  <!--Main Content-->
  <div class="w-full md:w-4/5 bg-gray-100">
    <div class="container bg-gray-100 pt-16 px-6 pb-16 h-full">
      <svelte:component this={$currentPage.component} {...$currentPage.props} />
    </div>
  </div>

  <!--Sidebar-->
  <div
    class="w-full md:w-1/5 bg-teal-900 md:bg-teal-900 px-2 text-center fixed bottom-0 md:pt-8 md:top-0 md:left-0 h-16 md:h-screen md:border-r-4 md:border-gray-600"
  >
    <div class="md:relative mx-auto lg:float-right lg:px-6">
      <ul class="list-reset flex flex-row md:flex-col text-center md:text-left">
        {#each pages as page}
          {#if page.name === $currentPage.name}
            <li class="mr-3 flex-1 justify-self-center">
              <button
                class="w-full block py-1 md:py-3 pl-1 align-middle text-white no-underline hover:text-white border-b-2 border-teal-600 focus:outline-none"
              >
                <span
                  class="pb-1 md:pb-0 text-xs md:text-base text-white md:font-bold block md:inline-block"
                >
                  {page.name}
                </span>
              </button>
            </li>
          {:else}
            <li class="mr-3 flex-1">
              <button
                on:click={() => currentPage.set(page)}
                class="w-full block py-1 md:py-3 pl-1 align-middle text-white-800 no-underline hover:text-teal-500 border-b-2 border-gray-800 md:border-gray-900 hover:border-teal-500 focus:outline-none"
              >
                <span
                  class="pb-1 md:pb-0 text-xs md:text-base text-white md:text-white-400 block md:inline-block"
                >
                  {page.name}
                </span>
              </button>
            </li>
          {/if}
        {/each}
      </ul>
    </div>
  </div>
</div>

<style>
  :root {
    --svelte-rgb: 255, 62, 0;
  }
</style>
