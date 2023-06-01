<script setup>
  import { ref } from "vue";
  import WebTitle from "./components/WebTitle.vue";
  import UserIcon from "./components/UserIcon.vue";
  import PlusIcon from "./components/PlusIcon.vue";
  import Posts from "./components/Posts.vue";
  import Camera from "./components/Camera.vue";

  const camera_modal = ref();
  const postsKey = ref(0);

  function openModal() {
    camera_modal.value.is_modal_opened = true;
  }

  function handlePostCreated() {
    postsKey.value++;
  }

</script>

<template>
  <div id="app" class="text-gray-600 font-Rubik bg-fixed">
    <div class="background-container"></div>
    <div class="content-container">
      <header class="fixed top-0 inset-x-0 z-10">
        <div class="flex justify-center bg-white pt-5 pb-3 back drop-shadow-md">
          <div class="flex items-center w-4/5">
            <WebTitle class="text-2xl flex-auto" />
            <UserIcon />
          </div>
        </div>
      </header>

      <main class="pt-20 pb-3 h-screen">
        <Camera @post-created="handlePostCreated" ref="camera_modal" />
        <Posts :key="postsKey" />
        <div class="h-12"></div>
      </main>
      <footer class="flex justify-center py-5 fixed bottom-0 inset-x-0">
        <div class="flex justify-end w-4/5">
          <PlusIcon @click="openModal" />
        </div>
      </footer>
    </div>
  </div>
</template>

<style scoped>
#app {
  /* Set the height and width to cover the viewport */
  height: 100vh;
  width: 100vw;
  overflow: auto; /* Enable scrolling within the app container */
}

.background-container {
  /* Set the fixed position and dimensions for the background container */
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1; /* Place the background container behind the app content */
  background-image: url("./assets/paper.jpg");
  background-repeat: no-repeat;
  background-size: cover;
}

.content-container {
  /* Set the relative position for the app content container */
  position: relative;
  z-index: 1; /* Place the app content above the background container */
}
</style>
