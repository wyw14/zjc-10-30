<template>
  <div class="card">
    <div class="recycle-header">
      <h3>🗑️ 回收站</h3>
      <span class="recycle-count">共 {{ items.length }} 条记录</span>
    </div>

    <div v-if="loading" class="empty-note">加载中...</div>

    <div v-else-if="items.length === 0" class="empty-note">
      回收站是空的
    </div>

    <div v-else class="recycle-list">
      <div v-for="item in items" :key="item.date" class="recycle-item">
        <div class="recycle-item-header">
          <span class="recycle-date">📅 原日期：{{ formatFullDate(item.originalDate) }}</span>
          <span class="recycle-deleted">⏰ 删除时间：{{ formatDateTime(item.deletedAt) }}</span>
        </div>
        <p class="recycle-question">❓ {{ item.question }}</p>
        <div v-if="item.answered && item.answer" class="recycle-answer">
          <p>{{ item.answer }}</p>
        </div>
        <div v-else class="recycle-no-answer">（无回答内容）</div>
        <div class="recycle-actions">
          <button class="restore-btn" @click="handleRestore(item)">
            ↩️ 恢复
          </button>
          <button class="danger-btn" @click="handlePermanentDelete(item)">
            🗑️ 彻底删除
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const props = defineProps({
  items: Array,
  loading: Boolean
})

const emit = defineEmits(['restore', 'permanent-delete', 'refresh'])

function formatFullDate(dateStr) {
  if (!dateStr) return ''
  const d = new Date(dateStr)
  const weekdays = ['周日', '周一', '周二', '周三', '周四', '周五', '周六']
  return `${d.getFullYear()}年${d.getMonth() + 1}月${d.getDate()}日 ${weekdays[d.getDay()]}`
}

function formatDateTime(isoStr) {
  if (!isoStr) return ''
  const d = new Date(isoStr)
  const pad = (n) => String(n).padStart(2, '0')
  return `${d.getFullYear()}-${pad(d.getMonth() + 1)}-${pad(d.getDate())} ${pad(d.getHours())}:${pad(d.getMinutes())}`
}

function handleRestore(item) {
  if (confirm(`确定要恢复 ${item.originalDate} 的回答吗？`)) {
    emit('restore', item.originalDate)
  }
}

function handlePermanentDelete(item) {
  if (confirm(`确定要彻底删除 ${item.originalDate} 的回答吗？此操作不可恢复！`)) {
    emit('permanent-delete', item.originalDate)
  }
}
</script>
