<script setup>
import axios from "axios"; // axiosのインポート
import { ref, onMounted } from "vue"; // ref と onMounted を使用

// ページタイトルと投稿状態のリアクティブなデータ
const title = ref("Firebaseの利用2(書き込み1：Create)");
const resText = ref("未完了");

// データを書き込む関数
const postData = async () => {
const project1 = "vue-test1-15df7"; // FirebaseプロジェクトID
const collection1 = "sample1"; // Firebaseのコレクション名
const url = `https://firestore.googleapis.com/v1/projects/${project1}/databases/(default)/documents/${collection1}`;

try {
const result = await axios.post(url, {
fields: {
name: { stringValue: "test-name" },
message: { stringValue: "test-message" },
},
});
console.log(result.data); // コンソールにレスポンスデータを出力
resText.value = "投稿完了！"; // 状態を更新
} catch (error) {
console.error("投稿エラー:", error);
resText.value = "投稿失敗";
}
};

// コンポーネントがマウントされた時にデータを書き込む
onMounted(postData);
</script>

<template>
    <div>
    <!-- ページのタイトルと投稿の状態を表示 -->
        <h1>{{ title }}</h1>
        <p>{{ resText }}</p>
    </div>
</template>