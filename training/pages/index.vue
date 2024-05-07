<template>
    <title>Trainings</title>
    <div class="header">
        <h1>Trainings</h1>
        <NuxtLink to="/add">
            <div class="add-button">
                <img src="\add.png" alt="" />
            </div>
        </NuxtLink>
    </div>
    <div class="container">
        <trainings></trainings>
    </div>
</template>

<script setup></script>

<style scoped>
.header {
    background-color: var(--light-black);
    height: 65px;
    display: flex;
    align-items: center;
    justify-content: space-between;
}
.add-button {
    width: 28px;
    height: 28px;
    margin: 0px 24px;
}
.add-button img {
    width: 100%;
    height: 100%;
}
.header h1 {
    color: white;
    font-size: 25px;
    margin: 0px 24px;
    font-weight: 400;
    position: relative;
    top: 6px;
}
.container {
    display: grid;
    grid-template-columns: 1fr;
    gap: 20px;
    padding: 24px;
}

@media screen and (min-width: 750px) {
    .header {
        height: 90px;
    }
    .header h1 {
        font-size: 32px;
    }

    .container {
        grid-template-columns: 1fr 1fr;
    }
}
</style>
