<template>
    <div class="header">
        <NuxtLink to="/">
            <img src="\back.png" alt="" />
        </NuxtLink>
    </div>
    <div class="main">
        <form class="container">
            <div>
                <input
                    v-model="data.name"
                    type="text"
                    id="name"
                    name="name"
                    placeholder="Name"
                    required
                    maxlength="20"
                />
            </div>
            <div>
                <textarea
                    v-model="data.description"
                    type="text"
                    id="description"
                    placeholder="description "
                    name="description"
                >
                </textarea>
            </div>
            <div class="time">
                <h2 style="color: white; margin: 0; font-size: 20px">
                    {{ data.day }}
                </h2>
                <select v-model="data.day" @change="dateSet" id="select">
                    <option v-for="(item, index) in list1">
                        {{ item }}
                    </option>
                </select>
            </div>
            <div>
                <NuxtLink to="/">
                    <button type="submit" @click="send()" disabled>Add</button>
                </NuxtLink>
            </div>
        </form>
    </div>
</template>

<script setup>
useHead({
    title: "Add Training",
});

const now = new Date();
const dayInWeek = now.getDay();

//sets date in data to selected day
function dateSet(e) {
    e.target.blur();
    const index = e.target.selectedIndex;
    const futureDate = new Date(now.getTime() + index * 86400000);
    data.date =
        futureDate.getDate() +
        "/" +
        (futureDate.getMonth() + 1) +
        "/" +
        futureDate.getFullYear();
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

const data = reactive({
    name: "",
    description: "",
    day: "Day",
    date: "",
    done: "undone",
});

let dataList = JSON.parse(localStorage.getItem("data"));

// push data to local storage
function send() {
    dataList.push(data);
    dataList.sort((a, b) => {
        const partsA = a.date.split("/");
        const partsB = b.date.split("/");
        const dateA = new Date(partsA[2], partsA[1] - 1, partsA[0]);
        const dateB = new Date(partsB[2], partsB[1] - 1, partsB[0]);
        return dateA - dateB;
    });
    localStorage.setItem("data", JSON.stringify(dataList));
}

onMounted(() => {
    //set list to the correct order
    list1.value = list
        .slice(dayInWeek - 1)
        .concat(list.slice(0, dayInWeek - 1));
});

watch(data, () => {
    //check if all fields are filled
    if (data.name && data.description && data.day !== "Day") {
        document.querySelector("button").disabled = false;
    }
});
</script>

<style scoped>
.main {
    display: flex;
    flex-direction: column;
    align-items: center;
}
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
.container {
    width: calc(100% - 48px);
    display: grid;
    grid-template-columns: 1fr 1fr;
    grid-template-rows: repeat(3, auto);
    grid-template-areas: "name name" "description description" "time button";
    padding: 24px;
    gap: 20px;
    max-width: 600px;
}
.container div {
    background-color: var(--grey);
    padding: 20px;
    border-radius: 12px;
}
.container div:nth-child(1) {
    grid-area: name;
}
.container div:nth-child(2) {
    grid-area: description;
}
input,
textarea {
    background-color: transparent;
    border: none;
    color: white;
    font-weight: 400;
    font-size: 24px;
    font-family: "Roboto", sans-serif;
}

textarea {
    width: 100%;
    resize: vertical;
    min-height: 128px;
}

input::placeholder,
textarea::placeholder {
    color: white;
}
input:focus,
textarea:focus {
    outline: none;
}
.container div:nth-child(4) {
    grid-area: button;
}
.time {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 5px;
    grid-area: time;
}
.time select {
    background-color: var(--grey);
    border: none;
    color: white;
    font-weight: 300;
    font-size: 20px;
    border-radius: 12px;
    text-align: center;
    width: 18px;
}
button {
    color: white;
    background-color: var(--grey);
    border: none;
    font-size: 20px;
    font-weight: 500;
    font-family: "Inter", sans-serif;
    width: 100%;
}
button:hover {
    cursor: pointer;
}
@media screen and (min-width: 750px) {
    .header {
        height: 90px;
    }
}
</style>
