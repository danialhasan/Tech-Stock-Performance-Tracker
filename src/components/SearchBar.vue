<template>
  <div class="w-full h-20 p-3 bg-gray-300">
    <!-- <div
      class="flex flex-row justify-start items-center bg-white w-full h-full rounded-lg"
    >
      <div class="mx-2 h-8 w-8">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          className="h-6 w-6"
          fill="none"
          viewBox="0 0 24 24"
          stroke="rgba(156, 163, 175)"
        >
          <path
            strokeLinecap="round"
            strokeLinejoin="round"
            strokeWidth="{2}"
            d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
          />
        </svg>
      </div>
      <h3 class="text-gray-500 text-2xl leading-tight">Search All Stocks...</h3>
    </div> -->
    <form class="w-full h-full">
      <div class="relative flex items-center w-full h-full">
        <div class="absolute mx-2 h-8 w-8">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            className="h-6 w-6"
            fill="none"
            viewBox="0 0 24 24"
            stroke="rgba(156, 163, 175)"
          >
            <path
              strokeLinecap="round"
              strokeLinejoin="round"
              strokeWidth="{2}"
              d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
            />
          </svg>
        </div>
        <input
          type="text"
          name="search"
          id=""
          class="pl-12 rounded-xl border-none w-full h-full text-xl"
        />
      </div>
    </form>
  </div>
</template>

<script>
export default {
  name: "SearchBar",
  data() {
    return {};
  },
};
</script>