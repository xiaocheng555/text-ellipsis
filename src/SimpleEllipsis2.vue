<template>
  <div class="text-ellipsis" :class="[!isExpand && 'un-expand']">
    <div 
      ref="contentEl" 
      class="text-ellipsis-content" 
      :style="{ maxHeight: isExpand ? 'none' : `${maxHeight}px` }">
      <!-- 占位符 -->
      <span class="text-ellipsis-placeholder" :style="{height: placeholderHeight + 'px'}"></span>
      <!-- 内容+操作按钮,不留空格 -->
      {{isExpand ? content : '' }}<span v-if="isEll" ref="tailEl" class="text-ellipsis-tail">
        <span class="text-ellipsis-dot" v-if="!isExpand">{{dot}}</span><span v-if="!single" class="text-ellipsis-action" @click="onActionClick">{{actionText}}</span>
      </span>{{isExpand ? '' : content}}
    </div>
    <span v-if="single && isEll" class="text-ellipsis-action" @click="onActionClick">{{actionText}}</span>
  </div>
</template>

<script setup lang="ts">
import { defineProps, defineExpose, ref, onBeforeMount, watch, computed, nextTick } from 'vue'

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
  },
  // 省略点
  dot: {
    type: String,
    default: '...'
  },
  single: {
    type: Boolean,
    default: false
  }
})

const isEll = ref(false) // 是否省略
const isExpand = ref(false) // 是否展开
const contentEl = ref<null | HTMLElement>(null) // 容器dom
const tailEl = ref<null | HTMLElement>(null) // 操作按钮dom
const placeholderHeight = ref(0) // 占位符高度
const maxHeight = ref(0) // 最大高度

const actionText = computed(() => {
  return isExpand.value ? props.collapseText : props.expandText
})

function toNum (val: any) : number {
  if (!val) return 0
  
  return parseFloat(val)
}

let lazyToCalc = false // 延迟执行

// 计算内容省略
async function calcEll () {
  await nextTick()
  if (!contentEl.value) return
  // 计算最大高度
  const { lineHeight } = window.getComputedStyle(contentEl.value)
  if (Number.isNaN(lineHeight)) {
    console.warn(`text-ellipsis 组件不能设置line-height为${lineHeight}`)
  }
  maxHeight.value = toNum(lineHeight) * props.rows
  // 判断是否省略内容
  isEll.value = contentEl.value.scrollHeight > maxHeight.value
  // 计算占位符高度: 容器高度 - 操作按钮高度
  if (isEll.value) {
    // 延迟执行,解决内容已经展开时,触发计算,tailEl容器展开时的高度与收起时高度不一致,导致错位
    if (isExpand.value) { 
      lazyToCalc = true
      return
    }
    await nextTick()
    if (tailEl.value) {
      placeholderHeight.value = maxHeight.value - tailEl.value.offsetHeight
    }
  }
}

// 展开/收起点击
function onActionClick () {
  isExpand.value = !isExpand.value
  if (lazyToCalc) {
    lazyToCalc = false
    calcEll()
  }
}

onBeforeMount(() => {
  calcEll()
})

watch(() => [
  props.content, 
  props.rows
], calcEll)

defineExpose({
  update: calcEll
})
</script>

<style lang='less' scoped>
.text-ellipsis {  
  line-height: 1.5;
  &.un-expand {
    .text-ellipsis-placeholder {
      float: right;
    }
    .text-ellipsis-tail {
      float: right;
      clear: both;
    }
  }
  &.is-single {
    .text-ellipsis-action {
      display: block;
    }
  }
}
.text-ellipsis-content {
  overflow: hidden;
  text-overflow: ellipsis;
  word-wrap: break-word;
  word-break: break-word;
  text-justify: inter-character;
  text-align: justify;
  white-space: pre-line;
  // white-space: pre-wrap;
}
.text-ellipsis-action {
  color: #409eff;
  cursor: pointer;
  &:hover {
    opacity: .85;
  }
}
</style>