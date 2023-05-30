<script setup>
import WebTitle from "./components/WebTitle.vue";
import UserIcon from "./components/UserIcon.vue";
import Post from "./components/Post.vue";
import PlusIcon from "./components/PlusIcon.vue";
import Posts from "./components/Posts.vue";
</script>

<template>
  <div id="app" class="text-gray-600 font-Rubik bg-fixed">
    <header class="fixed top-0 inset-x-0 z-10">
      <div class="flex justify-center bg-white pt-5 pb-3 back drop-shadow-md">
        <div class="flex items-center w-4/5">
          <WebTitle class="text-2xl flex-auto" />
          <UserIcon />
        </div>
      </div>
    </header>

    <main class="pt-20 pb-3 h-screen">
      <Posts />
    </main>
    <footer class="flex justify-center py-5 fixed bottom-0 inset-x-0">
      <div class="flex justify-end w-4/5">
        <PlusIcon />
      </div>
    </footer>
  </div>
</template>

<style scoped>
#app {
  background-image: url("./assets/paper.jpg");
  background-repeat: no-repeat;
  background-size: cover;
}
</style>
