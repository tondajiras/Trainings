<template>
    <div class="header">
        <NuxtLink to="/">
            <img src="\back.png" alt="" />
        </NuxtLink>
    </div>
    <div class="container">
        <div class="wrapper" v-for="item in dataList">
            <h2>
                {{ item.name }}
            </h2>
            <p v-for="line in item.description.split('\n')">
                {{ line }}
            </p>
            <div class="restore" v-if="item.done === 'old'">
                <select id="select" @change="setday" value="">
                    <option v-for="(item, index) in list1">
                        {{ item }}
                    </option>
                </select>
                <button type="submit" disabled @click="restore(item)">
                    <img src="\restore.png" alt="" />
                </button>
            </div>
            <h3>
                {{ item.day }}
                {{
                    item.date.split("/")[0] +
                    "." +
                    item.date.split("/")[1] +
                    "."
                }}
            </h3>
        </div>
    </div>
</template>

<script setup>
var day = ref(0);

function setday(e) {
    e.target.blur();
    day.value = e.target.selectedIndex;
    document.querySelector("button").disabled = false;
}

const list = [
    "Monday",
    "Tuesday",
    "Wednesday",
    "Thursday",
    "Friday",
    "Saturday",
    "Sunday",
];
var list1 = ref([]);
const now = new Date();
const dayInWeek = now.getDay();

onMounted(() => {
    //set list to the correct order
    list1.value = list
        .slice(dayInWeek - 1)
        .concat(list.slice(0, dayInWeek - 1));
});

//moves the training to main page
function restore(item) {
    // Find the corresponding item in the data array
    const dataIndex = data.value.findIndex((i) => i === item);
    // If the item was found in the data array
    console.log(day.value);

    if (dataIndex !== -1) {
        const futureDate = new Date(now.getTime() + day.value * 86400000);
        data.value[dataIndex].date =
            futureDate.getDate() +
            "/" +
            (futureDate.getMonth() + 1) +
            "/" +
            futureDate.getFullYear();
        data.value[dataIndex].done = "undone";
        data.value[dataIndex].day = list1.value[day.value];
        //order the data from the oldest to the newest
        data.value.sort((a, b) => {
            const partsA = a.date.split("/");
            const partsB = b.date.split("/");
            const dateA = new Date(partsA[2], partsA[1] - 1, partsA[0]);
            const dateB = new Date(partsB[2], partsB[1] - 1, partsB[0]);
            return dateA - dateB;
        });
        localStorage.setItem("data", JSON.stringify(data.value));
    }
}

const data = ref([]);

onMounted(() => {
    data.value = JSON.parse(localStorage.getItem("data"));
    console.log(data.value);
});

const dataList = computed(() => {
    const ordredData = data.value.filter(
        (item) => item.done === "done" || item.done === "old"
    );
    //order the data that are shown from the newest to the oldest
    ordredData.sort((a, b) => {
        const partsA = a.date.split("/");
        const partsB = b.date.split("/");
        const dateA = new Date(partsA[2], partsA[1] - 1, partsA[0]);
        const dateB = new Date(partsB[2], partsB[1] - 1, partsB[0]);
        return dateB - dateA;
    });
    return ordredData;
});

onUnmounted(() => {
    localStorage.setItem("data", JSON.stringify(data.value));
});
</script>

<style scoped>
.header {
    background-color: var(--light-black);
    height: 65px;
    display: flex;
    align-items: center;
}
.header img {
    width: 28px;
    height: 28px;
    margin: 0px 24px;
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
.container {
    display: grid;
    grid-template-columns: 1fr;
    gap: 20px;
    padding: 24px;
}
.restore {
    position: absolute;
    right: 12px;
    bottom: 12px;
    display: flex;
}
.restore select {
    width: 18px;
    background-color: var(--grey);
    color: white;
    font-weight: 300;
    font-size: 20px;
    border-radius: 12px;
    text-align: center;
}
.restore button {
    background-color: transparent;
    border: none;
    padding: 0;
    position: relative;
}
.restore img {
    width: 24px;
    aspect-ratio: 1;
}
.restore button:hover {
    cursor: pointer;
}
@media screen and (min-width: 750px) {
    .header {
        height: 90px;
    }
    .wrapper {
        padding: 20px;
    }
    .container {
        grid-template-columns: 1fr 1fr;
    }
    .restore img {
        width: 32px;
    }
}
</style>
