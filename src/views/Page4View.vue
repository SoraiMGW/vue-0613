<script setup>
import axios from 'axios'
import { ref, onMounted } from 'vue'

const title = ref('Firebaseの利用5(Update & Read)')
const db = ref([])

// Firestore 設定
const projectId    = 'vue-test1-15df7'
const collectionId = 'sample1'
const baseUrl      = `https://firestore.googleapis.com/v1/projects/${projectId}/databases/(default)/documents/${collectionId}`

// 一覧取得
const getData = async () => {
  try {
    const res = await axios.get(baseUrl)
    db.value = (res.data.documents || []).map(doc => ({
      name: doc.name,                                // フルパス
      fields: doc.fields,                            // 元データ
      editName: doc.fields.name.stringValue,         // 編集用初期値
      editMessage: doc.fields.message?.stringValue || ''
    }))
  } catch (err) {
    console.error('読み込みエラー:', err)
  }
}

// 更新処理
const updateDoc = async (doc) => {
  const url = `https://firestore.googleapis.com/v1/${doc.name}` +
              `?updateMask.fieldPaths=name&updateMask.fieldPaths=message`

  const body = {
    fields: {
      name:    { stringValue: doc.editName },
      message: { stringValue: doc.editMessage }
    }
  }

  try {
    await axios.patch(url, body)
    await getData()
  } catch (err) {
    console.error('更新エラー:', err)
  }
}

// マウント時に取得
onMounted(getData)
</script>

<template>
  <div>
    <h1>{{ title }}</h1>

    <ul v-if="db.length">
      <li v-for="doc in db" :key="doc.name" style="margin-bottom:1rem;">

        <!-- 現在の値 -->
        <div>
          <strong>Current:</strong>
          {{ doc.fields.name.stringValue }}
          <span v-if="doc.fields.message">／ {{ doc.fields.message.stringValue }}</span>
        </div>

        <!-- 編集フォーム -->
        <div style="margin:0.5rem 0;">
          <input v-model="doc.editName" placeholder="New name" />
          <input v-model="doc.editMessage" placeholder="New message" />
        </div>

        <!-- 更新ボタン -->
        <button @click="updateDoc(doc)">更新</button>
      </li>
    </ul>

    <p v-else>ドキュメントがありません。</p>
  </div>
</template>