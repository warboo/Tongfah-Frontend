<script setup>
    import { ref, watch, nextTick } from "vue";
    const height = 512;
    const width = 512;

    const canvas = ref();
    const player = ref();
    const is_taken = ref(false);
    const is_modal_opened = ref(false);
    let stream;
    
    defineExpose({ is_modal_opened });

    watch(is_modal_opened, (newVal, oldVal) => {
        if (newVal === true) {
            console.log("Running watcher...")
            playBtn_Util();
        }
    });

    function playBtn_Util() {
        try {
            stopBtn_Util();
        }
        catch (err) {}
        
        is_taken.value = false;

        navigator.mediaDevices
            .getUserMedia({
                video: {
                    facingMode: "environment",
                    width: { exact: 512 },
                    height: { exact: 512 }
                },
                audio: false
            })
            .then(mediaStream => {
                player.value.srcObject = mediaStream;
                player.value.play();
                stream = mediaStream;
            })
            .catch(err => {
                console.error(`An error occurred: ${err}`);
            });
    }

    function stopBtn_Util() {
        stream.getTracks().forEach(track => track.stop());
    }

    function captureBtn_Util() {
        if (width && height) {

            const player_value = player.value;
            is_taken.value = true;

            nextTick(() => {
                canvas.value.width = width;
                canvas.value.height = height;   

                const context = canvas.value.getContext("2d");
                context.drawImage(player_value, 0, 0, width, height);

                const data = canvas.value.toDataURL("image/jpeg");
                
                const img = new Image();
                img.src = data;

                stopBtn_Util();
            }); 
        } else {
            clearphoto();
        }
    }

    function clearphoto() {
        const context = canvas.value.getContext("2d");
        context.fillStyle = "#AAA";
        context.fillRect(0, 0, canvas.value.width, canvas.value.height);
    }

    function cancel_Util() {
        is_modal_opened.value = false;
        stopBtn_Util();
    }

</script>

<template>
    <div v-if="is_modal_opened" class="fixed z-20 flex justify-center w-full">
        <div class="bg-gray-800 drop-shadow-md py-5 rounded-lg w-9/12 md:w-6/12 lg:w-5/12 xl:w-4/12">
            <div class="flex justify-center">
                <canvas v-if="is_taken" id="canvas" ref="canvas" class="w-10/12 aspect-square"></canvas>
                <video v-else id="player" ref="player" class="w-10/12 aspect-square"></video> 
            </div>
            <div class="flex justify-center pt-5">
                <div class="flex justify-end w-10/12">
                    <button v-if="is_taken" @click=playBtn_Util class="rounded-full bg-sky-400 px-3 py-1 text-white drop-shadow">Retake</button>
                    <button v-if="is_taken" class="rounded-full bg-green-400 px-3 py-1 text-white ml-3 drop-shadow">Post</button>
                    <button v-else @click=captureBtn_Util class="rounded-full bg-sky-400 px-3 py-1 text-white drop-shadow">Capture</button>
                    <button @click=cancel_Util class="rounded-full bg-gray-400 px-3 py-1 text-white ml-3 drop-shadow">Cancel</button>
                </div>
            </div>
            
        </div>
   </div> 
</template>