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
const diaries = ref([]) // ← 追加：日記の一覧を格納ko

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
  max-width: 320px;
  margin: 30px auto;
  padding: 16px;
  background-color: #dcdcdc; /* 灰色ベース */
  border: 4px solid #2a2a2a;
  box-shadow: 6px 6px 0 #000;
  font-family: monospace;
  color: #111;
  font-size: 14px;
  line-height: 1.6;
  text-align: left;
  image-rendering: pixelated;
}

h2 {
  font-size: 16px;
  color: #b00000;
  margin-bottom: 12px;
  border-bottom: 2px solid #b00000;
  padding-bottom: 4px;
  letter-spacing: 1px;
}

input,
textarea {
  width: 100%;
  margin-bottom: 12px;
  padding: 8px;
  background: #fff;
  color: #000;
  font-family: monospace;
  font-size: 14px;
  border: 2px solid #333;
  box-shadow: inset 2px 2px 0 #999;
  outline: none;
  resize: none;
}

textarea {
  height: 100px;
}

button {
  display: inline-block;
  width: 100%;
  padding: 10px 0;
  background-color: #b00000;
  color: #fff;
  font-family: monospace;
  font-size: 14px;
  border: 3px solid #4d0000;
  box-shadow: 3px 3px 0 #000;
  cursor: pointer;
  text-transform: uppercase;
  transition: transform 0.1s ease-in-out;
}

button:active {
  transform: translate(2px, 2px);
  box-shadow: 1px 1px 0 #000;
}

hr {
  border: none;
  border-top: 2px dashed #444;
  margin: 24px 0;
}

ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.diary-item {
  margin-bottom: 20px;
  padding: 10px;
  background: #fff;
  border: 2px solid #2a2a2a;
  box-shadow: 3px 3px 0 #000;
  font-family: monospace;
}

.diary-item h3 {
  font-size: 14px;
  margin-bottom: 6px;
  color: #b00000;
}

.diary-item p {
  font-size: 13px;
  color: #000;
}

.diary-item small {
  display: block;
  text-align: right;
  font-size: 11px;
  color: #666;
  margin-top: 6px;
  font-style: italic;
}

</style>
