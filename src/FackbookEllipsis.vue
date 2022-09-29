<template>
  <div class="text-ellipsis">
    {{textVisible}}
    <span class="text-ellipsis-dots" v-if="isEll && !isExpand">{{dot}}</span><span v-if="isEll" class="text-ellipsis-action" @click="onActionClick">
      <slot v-bind="{isExpand}">
        {{actionText}}
      </slot>
    </span>
  </div>
</template>

<script setup lang="ts">
import { defineProps, ref, onBeforeMount, watch, computed } from 'vue'

const props = defineProps({
  content: {
    type: String,
    default: ''
  },
  newline: {
    type: Number,
    default: 5
  },
  maxLen: {
    type: Number,
    default: 400
  },
  expandText: {
    type: String,
    default: '展开'
  },
  collapseText: {
    type: String,
    default: '收起'
  },
  dot: {
    type: String,
    default: '...'
  }
})

const text = ref('') // 显示的文本内容
const isEll = ref(false) // 是否省略
const isExpand = ref(false) // 是否展开

const actionText = computed(() => {
  return isExpand.value ? props.collapseText : props.expandText
})

const textVisible = computed(() => {
  return isExpand.value ? props.content : text.value
})

// 计算显示的内容
function calcContent () {
  const { content, newline, maxLen } = props
  const rowTexts = content.split(/\n/) // 每行的内容
  const curRows = rowTexts.length
  
  let curText = content
  isEll.value = false
  
  // 超出最大行数
  if (curRows > newline) {
    isEll.value = true
    curText = rowTexts.slice(0, newline).join('\n')
  }
  
  // 超出最大字数
  if (curText.length > maxLen) {
    isEll.value = true
    curText = curText.slice(0, maxLen)
  }
  text.value = curText
}

// 展开/收起点击
function onActionClick () {
  isExpand.value = !isExpand.value
}

onBeforeMount(() => {
  calcContent()
})

watch(() => [props.content, props.maxLen, props.newline], calcContent)
</script>

<style lang='less' scoped>
.text-ellipsis {
  white-space: pre-wrap;
}
.text-ellipsis-action {
  color: #409eff;
  cursor: pointer;
}
</style>