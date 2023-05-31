<script setup>
    import { ref } from "vue";
    const width = 512;
    let height = 0;

    const canvas = ref();
    const player = ref();
    let stream;

    function playBtn_Util() {
        try {
            stopBtn_Util();
        }
        catch (err) {}
        
        canvas.value.style.display = "none";
        player.value.style.display = "inline";

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

        height = player.value.height * width / player.value.width;
        if (isNaN(height)) {
            height = width;
            console.log("Height is NaN.")
        }

        // player.value.setAttribute("width", width);
        // player.value.setAttribute("height", height);
        // canvas.value.setAttribute("width", width);
        // canvas.value.setAttribute("height", height);
    }

    function stopBtn_Util() {
        stream.getTracks().forEach(track => track.stop());
    }

    function captureBtn_Util() {
        if (width && height) {
            canvas.value.style.display = "inline";
            player.value.style.display = "none";

            canvas.value.width = width;
            canvas.value.height = height;

            const context = canvas.value.getContext("2d");
            context.drawImage(player.value, 0, 0, width, height);

            const data = canvas.value.toDataURL("image/jpeg");
            
            const img = new Image();
            img.src = data;

            stopBtn_Util();
        } else {
            clearphoto();
        }
    }

    function clearphoto() {
        const context = canvas.value.getContext("2d");
        context.fillStyle = "#AAA";
        context.fillRect(0, 0, canvas.value.width, canvas.value.height);

        const data = canvas.value.toDataURL("image/png");
        // photo.setAttribute("src", data);
    }

</script>

<template>
    <div class="fixed z-20 flex justify-center w-full">
        <div class="bg-gray-800 drop-shadow-md rounded-lg w-9/12 md:w-6/12 lg:w-5/12">
            <button @click=playBtn_Util>Play stream</button>
            <button @click=stopBtn_Util>Stop stream</button>
            <button @click=captureBtn_Util>Capture</button>
            <div class="flex justify-center">
                <canvas id="canvas" ref="canvas" class="w-10/12 aspect-square" style="display: none;"></canvas>
                <video id="player" ref="player" class="w-10/12 aspect-square"></video> 
            </div>
        </div>
   </div> 
</template>