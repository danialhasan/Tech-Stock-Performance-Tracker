<template>
  <div
    id="container"
    class="fixed flex items-center justify-center bottom-0 w-full px-4 h-28"
  >
    <div
      class="flex flex-row justify-center items-center w-full bg-gray-800 h-20 rounded-lg"
    >
      <div
        class="relative flex flex-col justify-center items-center w-20 h-20 mx-6"
      >
        <div
          class="absolute top-0 w-full h-1.5 rounded-full bg-indigo-500"
        ></div>

        <div>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="h-10 w-10"
            fill="none"
            viewBox="0 0 24 24"
            stroke="rgb(255, 255, 255)"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M16 8v8m-4-5v5m-4-2v2m-2 4h12a2 2 0 002-2V6a2 2 0 00-2-2H6a2 2 0 00-2 2v12a2 2 0 002 2z"
            />
          </svg>
        </div>
        <div><p class="font-regular text-md text-white">Graphs</p></div>
      </div>

      <div
        class="relative flex flex-col justify-center items-center w-20 h-20 mx-6"
      >
        <div class="hidden absolute top-0 w-full h-1 bg-indigo-500"></div>
        <div>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="h-10 w-10"
            fill="none"
            viewBox="0 0 24 24"
            stroke="rgb(255, 255, 255)"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M4 6h16M4 10h16M4 14h16M4 18h16"
            />
          </svg>
        </div>
        <div><p class="font-regular text-md text-white">Tables</p></div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "Navbar",
  data() {
    return {};
  },
};
</script>