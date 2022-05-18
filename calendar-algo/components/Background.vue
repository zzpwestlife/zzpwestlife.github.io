<template>
    <div class="w-60 relative mt-6">
        <div class="relative w-40 h-40">
            <img v-motion :initial="{ x: 800, y: -100, scale: 1.5, rotate: -50 }" alt="square"
                class="absolute top-0 left-0 right-0 bottom-0" src="https://sli.dev/logo-square.png" />
            <img v-motion :initial="{ y: 500, x: -100, scale: 2 }" alt="circle"
                class="absolute top-0 left-0 right-0 bottom-0" src="https://sli.dev/logo-circle.png" />
            <img v-motion :initial="{ x: 600, y: 400, scale: 2, rotate: 100 }" alt="triangle"
                class="absolute top-0 left-0 right-0 bottom-0" src="https://sli.dev/logo-triangle.png" />
        </div>
    </div>
</template>
