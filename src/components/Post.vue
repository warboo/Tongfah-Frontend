<script setup>
    defineProps({
        id: { type: String, required: true },
        owner: { type: String, required: true },
        time: { type: String, required: true },
        date: { type: String, required: true },
        location: { type: String, required: true },
        location_url: { type: String, require: true },
        condition: { type: String, required: true },
        temp: { type: String, required: true  },
        wind: { type: String, required: true  },
        precip: { type: String, required: true  },
        visibility: { type: String, required: true  },
        uv: { type: String, required: true },
        pm25: { type: String, required: true },
        img: { type: String, required: true }
    });

    const emit = defineEmits(["post-deleted"]);

    function deletePost(id) {
        emit("post-deleted", id);
    }
</script>

<template>
    <div class="flex justify-center">
        <div class="bg-white p-4 m-2 rounded-[12px] drop-shadow-md flex w-11/12 md:w-4/5 lg:w-4/6">
            <img :src="img" class="h-32 rounded-lg mr-5" />
            <div>
                <div class="text-sm">
                    <a :href="location_url" class="flex">
                        <img src="../assets/google-maps.png" class="mr-2 h-5 w-5" />
                        <h2 class="font-bold text-xl">{{ location }}</h2>
                    </a>
                    <h3 class="font-bold bg-gray-300 rounded-lg px-2 py-0.5 my-1 w-fit">{{ condition }}</h3>
                    <table>
                        <tr>
                            <td class="pr-20 sm:pr-10">Temp: {{ temp }}</td>
                            <td>Visibility: {{ visibility }} km</td>
                        </tr>
                        <tr>
                            <td class="pr-20 sm:pr-10">Wind: {{ wind }} km/h</td>
                            <td>UV: {{ uv }}</td>
                        </tr>
                        <tr>
                            <td class="pr-20 sm:pr-10">Precipitation: {{ precip }}%</td>
                            <td>PM2.5: {{ pm25 }}</td>
                        </tr>
                    </table>
                </div>
            </div>
            <div class="flex flex-col justify-between items-end text-sm text-gray-400 text-right flex-auto">
                <div>
                    <p>{{ time }}</p>
                    <p>{{ date }}</p>
                </div>
                <img src="../assets/bin.png" class="h-5 w-5 cursor-pointer" @click="deletePost(id)" />
            </div>
        </div>
    </div>
</template>