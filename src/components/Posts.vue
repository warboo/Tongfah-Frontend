<script setup>
  import { ref, onMounted } from "vue";
  import Post from "./Post.vue";
  import Loader from "./Loader.vue";
  import { Buffer } from "buffer";
  window.Buffer = window.Buffer || Buffer;

  const posts = ref([]);
  const loading = ref(false);

  onMounted(async () => {
    await getData();
  });

  function image_Util(image) {
    const buffer = image.data;
    const base64 = Buffer.from(buffer).toString("base64");
    return "data: image/png;base64," + base64;
  }

  function time_Util(date) {
    date = new Date(date).toString();
    return date.slice(16, 24);
  }

  function date_Util(date) {
    date = new Date(date).toString();
    return date.slice(0, 3) + "," + date.slice(3, 15);
  }

  function UV_Util(uv) {
    if (uv <= 2) return uv + " (Low)";
    else if (uv <= 5) return uv + " (Moderate)";
    else if (uv <= 7) return uv + " (High)";
    else if (uv <= 10) return uv + " (Very High)";
    else return uv + " (Extreme)";
  }

  function PM25_Util(pm25, pm25_index) {
    pm25 = Math.round(pm25);
    if (pm25_index === 1) return pm25 + " (Good)";
    else if (pm25_index === 2) return pm25 + " (Moderate)";
    else if (pm25_index === 3) return pm25 + " (Unhealthy for sensitive group)";
    else if (pm25_index === 4) return pm25 + " (Unhealthy)";
    else if (pm25_index === 5) return pm25 + " (Very Unhealthy)";
    else if (pm25_index === 6) return pm25 + " (Hazardous)";
  }

  async function getData() {
    loading.value = true;
    const res = await fetch(import.meta.env.VITE_BACKEND + "/posts");
    const finalRes = await res.json();
    posts.value = finalRes;
    loading.value = false;
  }

  async function deletePost(id) {
    loading.value = true;
    const options = {
        method: "DELETE",
    };
    const response = await fetch(import.meta.env.VITE_BACKEND + "/posts/delete/" + id, options)
      .then(() => {
        posts.value = posts.value.filter(post => post._id !== id);
      })
      .catch((error) => console.error(error));
    loading.value = false;
  }

</script>

<template>
    <Loader v-if="loading" />
    <Post v-else v-for="post in posts" @post-deleted="deletePost"
        :key=post._id
        :id=post._id
        :owner=post.owner 
        :time=time_Util(post.date)
        :date=date_Util(post.date)
        :location=post.location
        :condition=post.condition
        :temp=post.temp.toString()
        :wind=post.wind.toString()
        :precip=post.precip.toString()
        :visibility=post.visibility.toString()
        :uv=UV_Util(post.uv)
        :pm25="PM25_Util(post.pm25, post.pm25_index)"
        :img=image_Util(post.image)
      />
</template>