<template>
  <div class="text-ellipsis">
    <div 
      ref="contentEl" 
      class="text-ellipsis-content" 
      :style="{ '-webkit-line-clamp': isExpand ? 'unset' : rows }">
      {{content}}
    </div>
    <span v-if="isEll" class="text-ellipsis-action" @click="onActionClick">{{actionText}}</span>
  </div>
</template>

<script setup lang="ts">
import { defineProps, ref, onBeforeMount, watch, computed, nextTick } from 'vue'

const props = defineProps({
  // 文本内容
  content: {
    type: String,
    default: '',
    required: true
  },
  // 省略行数
  rows: {
    type: Number,
    default: 5
  },
  // 展开文案
  expandText: {
    type: String,
    default: '展开'
  },
  // 收起文案
  collapseText: {
    type: String,
    default: '收起'
  }
})

const isEll = ref(false) // 是否省略
const isExpand = ref(false) // 是否展开
const contentEl = ref<null | HTMLElement>(null) // 容器dom

const actionText = computed(() => {
  return isExpand.value ? props.collapseText : props.expandText
})

// 计算内容省略
async function calcEll () {
  await nextTick()
  if (!contentEl.value) return
  const { offsetHeight, scrollHeight } = contentEl.value
  isEll.value = scrollHeight > offsetHeight
}

// 展开/收起点击
function onActionClick () {
  isExpand.value = !isExpand.value
}

onBeforeMount(() => {
  calcEll()
})

watch(() => [
  props.content, 
  props.rows
], calcEll)
</script>

<style lang='less' scoped>
.text-ellipsis {
  white-space: pre-wrap;
}
.text-ellipsis-content {
  display: -webkit-box; 
  -webkit-box-orient: vertical; 
  word-break: break-all; 
  overflow: hidden;
}
.text-ellipsis-action {
  color: #409eff;
  cursor: pointer;
  &.is-block {
    display: block;
  }
  &:hover {
    opacity: .85;
  }
}
</style>