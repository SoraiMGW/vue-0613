<!-- src/views/Page3View.vue -->
<script setup>
import axios from 'axios'
import { ref, onMounted } from 'vue'

const title = ref('Firebaseの利用4(削除：Delete)')
const db = ref([])

// Firestore のプロジェクト／コレクション設定
const projectId    = 'vue-test1-15df7'
const collectionId = 'sample1'
const baseUrl      = `https://firestore.googleapis.com/v1/projects/${projectId}/databases/(default)/documents/${collectionId}`

// データ取得（一覧表示用）
const getData = async () => {
  try {
    const res = await axios.get(baseUrl)
    // res.data.documents: 各ドキュメントのフルパスやフィールドを含む
    db.value = res.data.documents || []
  } catch (err) {
    console.error('データ取得エラー:', err)
  }
}

// ドキュメント削除
const deleteDoc = async (fullName) => {
  // fullName 例: "projects/…/documents/sample1/ABC123"
  const url = `https://firestore.googleapis.com/v1/${fullName}`

  try {
    await axios.delete(url)
    // 削除後、一覧をリロード
    await getData()
  } catch (err) {
    console.error('削除エラー:', err)
  }
}

// マウント時に一覧取得
onMounted(getData)
</script>

<template>
  <div>
    <h1>{{ title }}</h1>
    <ul v-if="db.length">
      <li v-for="doc in db" :key="doc.name">
        <!-- 表示したいフィールド -->
        {{ doc.fields.name.stringValue }}
        <!-- 削除ボタン -->
        <button @click="deleteDoc(doc.name)" style="margin-left:8px">
          削除
        </button>
      </li>
    </ul>
    <p v-else>ドキュメントがありません。</p>
  </div>
</template>