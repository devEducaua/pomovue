<script setup lang="ts">
    import { ref, computed } from "vue";

    const workTime = 25 * 60;
    const restTime = 5 * 60;

    const mode = ref("work");
    const timer = ref(workTime);
    const playingLabel = ref("start");

    const formatted = computed(() => format(timer.value));

    let isPlaying = false;

    const format = (time: number): string => {
        const m = Math.floor(time / 60);
        const s = time % 60;

        return `${m}:${s < 10 ? '0' : ''}${s}`;
    }

    const down = () => {
        timer.value--;
    }

    const toggle = () => {
        isPlaying = !isPlaying;
        playingLabel.value = isPlaying ? "pause" : "start";
    }

    const enableNotify = async () => {
        if (Notification.permission === "default")
            await Notification.requestPermission();
    }

    const sound = new Audio("/beep.mp3");

    const time = setInterval(async () => {
        if (isPlaying) 
            down();

        if (timer.value === 0) {
            if (mode.value == "work") {
                mode.value = "rest";
                timer.value = restTime;
            }

            else if (mode.value === "rest") {
                mode.value = "work";
                timer.value = workTime;
            }

            if (Notification.permission === "granted")
                new Notification("mode change", { body: `to ${mode.value}` });

            sound.play();
        }

    }, 1000);
</script>

<template>
    <main>
        <div class="box">
            <div class="info">
                <p id="mode">{{ mode }}</p>
                <button @click="enableNotify">notify</button>
            </div>
            <h1 class="time">{{ formatted }}</h1>
            <div class="buttons">
                <button @click="toggle">{{ playingLabel }}</button>
            </div>
        </div>
    </main>
</template>

<style>

.info {
    display: flex;
    justify-content: center;
    align-items: center;
    color: #777;
    font-size: 1.2rem;

    p {
        margin: 20px;
    }
    button {
        color: #777;
        font-family: serif;
        font-size: 1.2rem;
    }
}

.time {
    font-size: 8em;
    margin: 10px;
}

.box {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
}

button {
    background-color: transparent;
    color: #fff;
    font-size: 1.5rem;
    padding: 10px 30px;
    border: 0.1px solid #000;
    border-radius: 6px;

    &:hover {
        border: 0.1px solid #222;
    }

    &:active {
        background-color: #222;
    }
}

</style>

