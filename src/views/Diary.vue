<template>
  <div class="form-container">
    <h2>日記投稿</h2> <!-- タイトルを「記事投稿」→「日記投稿」に変更 -->

    <!-- タイトル入力 -->
    <input v-model="title" placeholder="タイトル" />

    <!-- 日記本文入力 -->
    <textarea v-model="content" placeholder="本文（今日の出来事など）"></textarea>

    <!-- 送信ボタン -->
    <button @click="submit">送信</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

// タイトルと本文（content）をrefで定義
const title = ref('')
const content = ref('') // ← body を content に名称変更（より意味的に適切なため）

// 送信処理
const submit = async () => {
  // APIに送信するJSONデータ
  const payload = {
    title: title.value,
    content: content.value, // ← content に合わせてキーも修正
    createdAt: new Date().toISOString() // ← 投稿日時を追加（APIが受け付ければ）
  }

  try {
    // ポート番号は自分のASP.NET Coreアプリに合わせて調整
    await axios.post('https://localhost:7277/api/diary', payload) // ← URLはあなたのAPIに合わせて変更
    alert('登録完了')

    // 入力フォームをリセット
    title.value = ''
    content.value = ''
  } catch (e) {
    console.error('登録エラー', e)
    alert('登録に失敗しました')
  }
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
</style>
