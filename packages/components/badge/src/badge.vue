<template>
  <div :class="ns.b()">
    <slot />
    <transition :name="`${ns.namespace.value}-zoom-in-center`">
      <sup
        v-show="!hidden && (content || isDot)"
        :class="[
          ns.e('content'),
          ns.em('content', type),
          ns.is('fixed', !!$slots.default),
          ns.is('dot', isDot),
          ns.is('process', processing),
          badgeClass,
        ]"
        :style="style"
        v-text="content"
      />
    </transition>
  </div>
</template>

<script lang="ts" setup>
import { computed } from 'vue'
import { useNamespace } from '@vuesax-alpha/hooks'
import { addUnit, isNumber } from '@vuesax-alpha/utils'
import { badgeProps } from './badge'
import type { StyleValue } from 'vue'

defineOptions({
  name: 'VsBadge',
})

const props = defineProps(badgeProps)

const ns = useNamespace('badge')

const hidden = computed(() => {
  return props.hidden || (props.value === 0 && !props.showZero)
})

const content = computed<string>(() => {
  if (props.isDot) return ''
  if (isNumber(props.value) && isNumber(props.max)) {
    if (props.max < props.value) {
      return `${props.max}+`
    }
    return `${props.value}`
  }
  return `${props.value}`
})
const style = computed<StyleValue>(() => {
  return [
    {
      backgroundColor: props.color,
      marginRight: addUnit(-(props.offset?.[0] ?? 0)),
      marginTop: addUnit(props.offset?.[1] ?? 0),
    },
    props.badgeStyle ?? {},
    { '--vs-badge-ripple-color': props.color || '' },
  ]
})

defineExpose({
  /** @description badge content */
  content,
})
</script>
