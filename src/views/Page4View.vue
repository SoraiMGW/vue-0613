<script setup>
import axios from 'axios'
import { ref, onMounted } from 'vue'

// const title = ref('Firebaseの利用5(Update & Read)')
const db = ref([])

// Firestore 設定
const projectId = 'vue-test1-15df7'
const collectionId = 'sample1'
const baseUrl = `https://firestore.googleapis.com/v1/projects/${projectId}/databases/(default)/documents/${collectionId}`

// 一覧取得
const getData = async () => {
  try {
    const res = await axios.get(baseUrl)
    db.value = (res.data.documents || []).map(doc => ({
      name: doc.name,
      fields: doc.fields,
      // name/message → title/description/imageUrl に変更 ← ここが変更ポイント
      editTitle: doc.fields.title?.stringValue || '',
      editDescription: doc.fields.description?.stringValue || '',
      editImageUrl: doc.fields.imageUrl?.stringValue || ''
    }))
  } catch (err) {
    console.error('読み込みエラー:', err)
  }
}

// 更新処理
const updateDoc = async (doc) => {
  const url = `https://firestore.googleapis.com/v1/${doc.name}` +
              `?updateMask.fieldPaths=title&updateMask.fieldPaths=description&updateMask.fieldPaths=imageUrl` // ← 更新対象を変更

  const body = {
    fields: {
      // 更新するフィールドを title, description, imageUrl に変更 ← ここが変更ポイント
      title:       { stringValue: doc.editTitle },
      description: { stringValue: doc.editDescription },
      imageUrl:    { stringValue: doc.editImageUrl }
    }
  }

  try {
    await axios.patch(url, body)
    await getData()
  } catch (err) {
    console.error('更新エラー:', err)
  }
}

onMounted(getData)
</script>

<template>
  <div>
    <h1>{{ title }}</h1>

    <ul v-if="db.length">
      <li v-for="doc in db" :key="doc.name" style="margin-bottom:1.5rem;">

        <!-- 現在の値を表示 -->
        <div>
          <strong>Current:</strong>
          <p><strong>タイトル:</strong> {{ doc.fields.title?.stringValue || 'なし' }}</p> <!-- ← ここが変更ポイント -->
          <p><strong>説明:</strong> {{ doc.fields.description?.stringValue || 'なし' }}</p> <!-- ← ここが変更ポイント -->
          <img
            v-if="doc.fields.imageUrl?.stringValue"
            :src="doc.fields.imageUrl.stringValue"
            alt="Image"
            style="max-width: 150px;"
          /> <!-- ← ここが変更ポイント -->
        </div>

        <!-- 編集フォーム -->
        <div style="margin-top: 0.5rem;">
          <input v-model="doc.editTitle" placeholder="新しいタイトル" /> <!-- ← ここが変更ポイント -->
          <input v-model="doc.editDescription" placeholder="新しい説明" /> <!-- ← ここが変更ポイント -->
          <input v-model="doc.editImageUrl" placeholder="新しい画像URL" /> <!-- ← ここが変更ポイント -->
        </div>

        <!-- 更新ボタン -->
        <button @click="updateDoc(doc)" style="margin-top: 0.5rem;">更新</button>
      </li>
    </ul>

    <p v-else>ドキュメントがありません。</p>
  </div>
</template>
