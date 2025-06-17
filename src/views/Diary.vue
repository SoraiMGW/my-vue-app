<template>
  <div class="form-container">
    <h2>日記投稿</h2>

    <!-- 投稿フォーム -->
    <input v-model="title" placeholder="タイトル" />
    <textarea v-model="content" placeholder="本文（今日の出来事など）"></textarea>
    <button @click="submit">送信</button>

    <hr />

    <!-- 日記一覧表示 -->
    <h2>日記一覧</h2>
    <ul>
      <li v-for="diary in diaries" :key="diary.id" class="diary-item">
        <h3>{{ diary.title }}</h3>
        <p>{{ diary.content }}</p>
        <small>{{ formatDate(diary.createdAt) }}</small>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const title = ref('')
const content = ref('')
const diaries = ref([]) // ← 追加：日記の一覧を格納

const submit = async () => {
  const payload = {
    title: title.value,
    content: content.value,
    createdAt: new Date().toISOString()
  }

  try {
    await axios.post('http://localhost:7277/api/diary', payload)
    alert('登録完了')
    title.value = ''
    content.value = ''
    await fetchDiaries() // ← 送信後に再取得
  } catch (e) {
    console.error('登録エラー', e)
    alert('登録に失敗しました')
  }
}

// ← 追加：日記をAPIから取得する処理
const fetchDiaries = async () => {
  try {
    const res = await axios.get('http://localhost:7277/api/diary')
    diaries.value = res.data
  } catch (e) {
    console.error('取得エラー', e)
    alert('日記の取得に失敗しました')
  }
}

// ← 追加：マウント時に取得
onMounted(() => {
  fetchDiaries()
})

// ← 追加：日付を見やすくする関数
const formatDate = (iso) => {
  const d = new Date(iso)
  return d.toLocaleString()
}
</script>

<style scoped>
.form-container {
  max-width: 600px;
  margin: 0 auto;
  padding: 16px;
  background-color: #f7f7f7;
  border-radius: 8px;
}
input,
textarea {
  width: 100%;
  margin-bottom: 12px;
  padding: 8px;
  font-size: 16px;
}
button {
  padding: 8px 16px;
  font-size: 16px;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 4px;
}
.diary-item {
  margin-bottom: 20px;
  padding: 10px;
  border-bottom: 1px solid #ccc;
}
</style>
