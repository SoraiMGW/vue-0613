<script setup>
import axios from "axios";
import { ref } from "vue";

// ページタイトルと投稿状態
const title = ref("Firebaseの利用3(書き込み2：Create)");
const resText = ref("未完了");

// 投稿データのリアクティブなオブジェクト
const post = ref({
fields: {
name: { stringValue: "" }, // 名前
message: { stringValue: "" }, // メッセージ
},
});

// Firestore にデータを投稿する関数
const postData = async () => {
const project1 = "vue-test1-ac637"; // Firebase プロジェクトID
const collection1 = "sample1"; // Firebase のコレクション名
const url = `https://firestore.googleapis.com/v1/projects/${project1}/databases/(default)/documents/${collection1}`;
try {
const result = await axios.post(url, post.value);
console.log(result.data); // 結果をコンソールに出力
resText.value = "投稿完了！"; // 投稿の状態を更新
} catch (error) {
console.error("投稿エラー:", error);
resText.value = "投稿失敗";
}
};
</script>

<template>
    <div>
    <!-- ページのタイトルと投稿の状態を表示 -->
        <h1>{{ title }}</h1>
        <p>{{ resText }}</p>
    <!-- フォーム：名前とメッセージの入力欄 -->
        <p>名前：<input type="text" v-model="post.fields.name.stringValue" /></p>
        <p>メッセージ：<input type="text" v-model="post.fields.message.stringValue" /></p>
    <!-- 投稿ボタン -->
        <input type="button" value="投稿！" @click="postData" />
    </div>
</template>