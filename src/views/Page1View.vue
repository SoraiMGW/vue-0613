<script setup>
import axios from 'axios'
import { ref, onMounted } from 'vue'

const title = ref('お気に入り登録（Create）')
const fbiList = ref([])
const projectId = 'vue-test1-15df7' 
const collectionId = 'sample1'  

// FBI指名手配犯リスト取得
const getFBIList = async () => {
  try {
    const res = await axios.get('https://api.fbi.gov/wanted/v1/list')
    fbiList.value = res.data.items || []
  } catch (e) {
    console.error('FBI取得エラー', e)
  }
}

// Firestoreにお気に入り登録
const addToFavorites = async (person) => {
  const url = `https://firestore.googleapis.com/v1/projects/${projectId}/databases/(default)/documents/${collectionId}`
  try {
    await axios.post(url, {
      fields: {
        uid: { stringValue: person.uid },
        title: { stringValue: person.title },
        description: { stringValue: person.description || '' },
        imageUrl: { stringValue: person.images?.[0]?.original || '' },
      }
    })
    // alert('お気に入りに追加しました！')
  } catch (err) {
    console.error('追加エラー', err)
    alert('追加に失敗しました')
  }
}

onMounted(getFBIList)
</script>

<template>
  <div>
    <h1>{{ title }}</h1>
    <ul v-if="fbiList.length">
      <li v-for="person in fbiList" :key="person.uid" style="margin-bottom:1rem;">
        <p><strong>{{ person.title }}</strong></p>
        <img :src="person.images?.[0]?.original" alt="No image" width="150" />
        <p>{{ person.description }}</p>
        <button @click="addToFavorites(person)">お気に入りに追加</button>
      </li>
    </ul>
  </div>
</template>
