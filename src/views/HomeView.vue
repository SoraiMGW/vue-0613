<script setup>
import axios from "axios";
import { ref, onMounted } from "vue";

const title = ref("FBI指名手配犯リスト");
const fbiList = ref([]);
const page = ref(1);

// データ取得
const getFBIList = async () => {
  try {
    const response = await axios.get("https://api.fbi.gov/wanted/v1/list", {
      params: {
        page: page.value
      }
    });
    fbiList.value = response.data.items || [];
  } catch (err) {
    console.error("FBI API取得エラー:", err);
  }
};

onMounted(getFBIList);
</script>

<template>
  <div>
    <h1>{{ title }}</h1>
    <ul v-if="fbiList.length">
      <li v-for="person in fbiList" :key="person.uid">
        <p><strong>{{ person.title }}</strong></p>
        <img :src="person.images[0]?.original" alt="No image" width="150" />
        <p>{{ person.description }}</p>
      </li>
    </ul>
    <button @click="page--; getFBIList()" :disabled="page === 1">前へ</button>
    <button @click="page++; getFBIList()">次へ</button>
  </div>
</template>
