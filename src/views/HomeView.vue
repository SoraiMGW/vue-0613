<script setup>
import axios from "axios";
import { ref, onMounted } from "vue";

// リアクティブなデータ
const title = ref("Firebaseの利用1(読み込み：Read)");
const db = ref([]);

// Firestore からデータを取得する関数
const getData = async () => {
const project1 = "vue-test1-15df7"; // プロジェクトID
const collection1 = "sample1"; // コレクション名
const url =`https://firestore.googleapis.com/v1/projects/${project1}/databases/(default)/documents/${collection1}`;

try {
const result = await axios.get(url);
console.log(result.data);
db.value = result.data.documents; // 取得したデータを db に格納
} catch (error) {
console.error("データ取得エラー:", error);
}
};

// コンポーネントがマウントされたらデータを取得
onMounted(getData);
</script>

<template>
  <div>
    <h1>{{ title }}</h1>
    <ul>
      <li v-for="dbItem in db" :key="dbItem.name">
      {{ dbItem.fields.name.stringValue }}
      </li>
    </ul>
  </div>
</template>