<template>
    <div class="wrapper" v-for="(item, index) in dataList">
        <h2>
            {{ item.name }}
        </h2>
        <p v-for="line in item.description.split('\n')">
            {{ line }}
        </p>
        <div class="check" @click="check(dataList[index])">
            <img src="\check.png" alt="" />
        </div>
        <h3>
            {{ item.day }}
            {{ item.date.split("/")[0] + "." + item.date.split("/")[1] + "." }}
        </h3>
    </div>
    <div class="content">
        <div class="stats-and-finished">
            <h2>Done this week: {{ count }}</h2>
        </div>
        <NuxtLink to="/older-trainings" class="stats-and-finished">
            <h2>Finished trainings</h2>
        </NuxtLink>
    </div>
</template>

<script setup>
let count = ref(0);
const now = new Date();

function setDate() {
    let startOfWeek = new Date();
    startOfWeek.setDate(new Date().getDate() - new Date().getDay() + 1);
    startOfWeek.setHours(0, 0, 0, 0);
    localStorage.setItem(
        "thisWeek",
        JSON.stringify({ count: 0, date: startOfWeek })
    );
}

function check(item) {
    // Find the corresponding item in the data array
    const dataIndex = data.value.findIndex((i) => i === item);
    // If the item was found in the data array
    if (dataIndex !== -1) {
        data.value[dataIndex].done = "done";
        localStorage.setItem("data", JSON.stringify(data.value));
    }
    let thisWeek = JSON.parse(localStorage.getItem("thisWeek"));
    thisWeek.count++;
    count.value = thisWeek.count;
    localStorage.setItem("thisWeek", JSON.stringify(thisWeek));
}

const data = ref([]);

//list that is shown
const dataList = computed(() => {
    return data.value.filter((item) => item.done === "undone");
});

onMounted(() => {
    if (!localStorage.getItem("data")) {
        localStorage.setItem("data", JSON.stringify([]));
    }
    data.value = JSON.parse(localStorage.getItem("data"));

    //checks if the date is in the past
    for (let index = 0; index < data.value.length; index++) {
        const parts = data.value[index].date.split("/");
        if (
            new Date(parts[2], parts[1] - 1, parts[0]).getTime() <
            new Date(now.getFullYear(), now.getMonth(), now.getDate()).getTime()
        ) {
            data.value[index].done = "old";
            localStorage.setItem("data", JSON.stringify(data.value));
        }
    }

    if (!localStorage.getItem("thisWeek")) {
        setDate();
    } else {
        const thisWeek = JSON.parse(localStorage.getItem("thisWeek"));
        if (
            new Date(thisWeek.date).getTime() <
            new Date().getTime() - 7 * 86400000
        ) {
            setDate();
        } else {
            count.value = thisWeek.count;
        }
    }
});
</script>

<style scoped>
.content {
    display: flex;
    flex-direction: column;
    gap: 18px;
}
.stats-and-finished {
    height: 60px;
    background-color: var(--grey);
    border-radius: 12px;
    display: flex;
    justify-content: center;
    align-items: center;
    text-decoration: none;
}
.stats-and-finished h2 {
    color: white;
    font-size: 18px;
}
.wrapper {
    background-color: var(--grey);
    padding: 12px;
    color: white;
    border-radius: 12px;
    position: relative;
}
h2 {
    margin: 0;
}
h3 {
    margin: 0;
    position: absolute;
    top: 12px;
    right: 12px;
}
.check:hover {
    cursor: pointer;
}
.check {
    position: absolute;
    right: 12px;
    bottom: 12px;
    width: 24px;
    aspect-ratio: 1;
}
img {
    width: 100%;
}
@media screen and (min-width: 750px) {
    .wrapper {
        padding: 20px;
    }
    .check {
        width: 32px;
    }
    .stats-and-finished h2 {
        font-size: 20px;
    }
}
</style>
