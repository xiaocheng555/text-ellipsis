<template>
  <div class="text-ellipsis" ref="boxEl">
    {{textVisible}}<span class="text-ellipsis-suffix" v-if="isEll" ref="actionEl">
      {{!isExpand ? dot : ''}}<span class="text-ellipsis-action" @click="onActionClick">
        <slot v-bind="{isExpand}">
          {{actionText}}
        </slot>
      </span>
    </span>
  </div>
</template>

<script setup lang="ts">
import { defineProps, ref, onBeforeMount, watch, computed, nextTick } from 'vue'

const props = defineProps({
  content: {
    type: String,
    default: ''
  },
  rows: {
    type: Number,
    default: 5
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
const boxEl = ref<null | HTMLElement>(null) // 容器dom
const actionEl = ref<null | HTMLElement>(null) // 容器dom

const actionText = computed(() => {
  return isExpand.value ? props.collapseText : props.expandText
})

const textVisible = computed(() => {
  return isExpand.value ? props.content : text.value
})

function toNum (val: any) : number {
  if (!val) return 0
  
  return parseFloat(val)
}

function toPx (val?: Number | String) {
  if (typeof val === 'number') return val + 'px'
  if (typeof val === 'string') {
    if (/^\d+$/.test(val)) return val + 'px'
    return val
  }
  return ''
}

// 计算显示的内容
async function calcContent () {
  // 用新的div模拟文本内的容器环境
  function cloneBox () {
    if (!boxEl.value) return
    
    const originStyle = window.getComputedStyle(boxEl.value)
    const div = document.createElement('div')
    const styleNames: string[] = Array.prototype.slice.apply(originStyle)
    styleNames.forEach(name => {
      div.style.setProperty(name, originStyle.getPropertyValue(name))
    })
    
    div.style.position = 'fixed'
    div.style.zIndex = '-9999'
    div.style.top = '-9999px'
    div.style.height = 'auto'
    div.style.minHeight = 'auto'
    div.style.maxHeight = 'auto'
    
    div.textContent = props.content
    document.body.appendChild(div)
    return div
  }
  
  // 计算省略的文本内容
  function calcEllText (div: HTMLElement, maxHeight: number) {
    if (!actionEl.value) return ''
    
    // 省略符号和展开文字的容器
    const oEl = document.createElement('span')
    const oWitdth = actionEl.value.offsetWidth
    oEl.style.width = toPx(oWitdth)
    oEl.style.display = 'inline-block'
    oEl.style.verticalAlign = 'top'
    oEl.style.height = div.style.lineHeight
    
    // 二分法计算省略时的文本
    let l = 0
    let r = props.content.length
    let res = -1
    while (l <= r) {
      const mid = Math.floor((l + r) / 2)
      div.textContent = props.content.slice(0, mid)
      div.appendChild(oEl)
      // debugger
      if (div.offsetHeight <= maxHeight) {
        // 未溢出
        l = mid + 1
        res = mid // 记录满足条件的值
      } else { 
        // 溢出
        r = mid - 1
      }
    }
    
    return props.content.slice(0, res)
  }
  
  await nextTick()
  const div = cloneBox()
  if (!div) return 
  
  const maxHeight = (props.rows + 1 / 2) * toNum(div.style.lineHeight)
  if (maxHeight < div.offsetHeight) {
    isEll.value = true
    await nextTick()
    text.value = calcEllText(div, maxHeight)
  } else {
    isEll.value = false
  }
  document.body.removeChild(div)
}

// 展开/收起点击
function onActionClick () {
  isExpand.value = !isExpand.value
}

onBeforeMount(() => {
  calcContent()
})

watch(() => props.content, calcContent)
</script>

<style lang='less' scoped>
.text-ellipsis {
  line-height: 1.5;
}
.text-ellipsis-suffix {
  white-space: nowrap;
}
.text-ellipsis-action {
  white-space: nowrap;
  color: #409eff;
  cursor: pointer;
}
</style>