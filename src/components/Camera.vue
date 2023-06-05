<script setup>
    import { ref, watch, nextTick } from "vue";
    const height = 512;
    const width = 512;

    const compressed_height = 128;
    const compressed_width = 128;

    const canvas = ref();
    const player = ref();
    const is_taken = ref(false);
    const is_modal_opened = ref(false);
    let stream;

    let imageFile;
    
    defineExpose({ is_modal_opened });
    const emit = defineEmits( ["post-created"] );

    // ------- Camera -------

    watch(is_modal_opened, (newVal, oldVal) => {
        if (newVal === true) {
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
                    width: { exact: width },
                    height: { exact: height }
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
                // set resolution for download
                canvas.value.width = compressed_width;
                canvas.value.height = compressed_height;   

                const context = canvas.value.getContext("2d");
                context.drawImage(player_value, 0, 0, compressed_width, compressed_height);

                const imageData = canvas.value.toDataURL("image/png");
                imageFile = dataURLtoFile(imageData, "image.png");

                context.clearRect(0, 0, canvas.compressed_width, canvas.compressed_height);

                // set resolution for visualization
                canvas.value.width = width;
                canvas.value.height = height;   

                context.drawImage(player_value, 0, 0, width, height);

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

    // ------- POST Request -------

    function dataURLtoFile(dataURL, filename) {
        const arr = dataURL.split(",");
        const mime = arr[0].match(/:(.*?);/)[1];
        const bstr = atob(arr[1]);
        let n = bstr.length;
        const u8arr = new Uint8Array(n);
        while (n--) {
            u8arr[n] = bstr.charCodeAt(n);
        }
        return new File([u8arr], filename, { type: mime });
    }

    function postBtn_Util() {
        navigator.geolocation.getCurrentPosition(addItem);
        cancel_Util();
    }

    const addItem = async (position) => {
        const formData = new FormData();
        formData.append("owner", "Veeliw");
        formData.append("latitude", position.coords.latitude);
        formData.append("longitude", position.coords.longitude);
        formData.append("image", imageFile);

        const options = {
            method: "POST",
            body: formData
        };
        const response = await fetch(import.meta.env.VITE_BACKEND + "/posts/post", options)
            .catch((error) => console.error(error));
        emit("post-created");
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
                    <button v-if="is_taken" @click=postBtn_Util class="rounded-full bg-green-400 px-3 py-1 text-white ml-3 drop-shadow">Post</button>
                    <button v-else @click=captureBtn_Util class="rounded-full bg-sky-400 px-3 py-1 text-white drop-shadow">Capture</button>
                    <button @click=cancel_Util class="rounded-full bg-gray-400 px-3 py-1 text-white ml-3 drop-shadow">Cancel</button>
                </div>
            </div>
            
        </div>
   </div> 
</template>