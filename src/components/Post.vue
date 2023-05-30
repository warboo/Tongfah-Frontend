<script setup>
defineProps({
  owner: { type: String, required: true },
  time: { type: String, required: true },
  date: { type: String, required: true },
  location: { type: String, required: true },
  condition: { type: String, required: true },
  temp: { type: String, required: true  },
  wind: { type: String, required: true  },
  precip: { type: String, required: true  },
  visibility: { type: String, required: true  },
  uv: { type: String, required: true },
  pm25: { type: String, required: true },
  img: { type: String, required: true }
})
</script>

<template>
    <div class="flex justify-center">
        <div class="bg-white p-4 m-2 rounded-[12px] drop-shadow-md flex w-11/12 md:w-4/5 lg:w-4/6">
            <img :src="img" class="h-32 rounded-lg mr-5" />
            <div>
                <div class="text-sm">
                    <h2 class="font-bold text-xl">{{ location }}</h2>
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
            <div class="text-sm text-gray-400 text-right flex-auto">
                <p>{{ time }}</p>
                <p>{{ date }}</p>
            </div>
        </div>
    </div>
</template>