<template>
  <el-tooltip
    ref="tooltipRef"
    v-bind="$attrs"
    :trigger="trigger"
    :placement="placement"
    :disabled="disabled"
    :visible="visible"
    :transition="transition"
    :popper-options="popperOptions"
    :tabindex="tabindex"
    :append-to-body="appendToBody"
    :content="content"
    :offset="offset"
    :show-after="showAfter"
    :hide-after="hideAfter"
    :auto-close="autoClose"
    :show-arrow="showArrow"
    :aria-label="title"
    :effect="effect"
    :enterable="enterable"
    :popper-class="kls"
    :popper-style="style"
    :teleported="compatTeleported"
    :persistent="persistent"
    @show="afterEnter"
    @hide="afterLeave"
  >
    <template v-if="$slots.reference">
      <slot name="reference" />
    </template>

    <template #content>
      <div v-if="title" :class="ns.e('title')" role="title">
        {{ title }}
      </div>
      <slot>
        {{ content }}
      </slot>
    </template>
  </el-tooltip>
</template>
<script lang="ts">
import { defineComponent, computed, ref, unref } from 'vue'
import ElTooltip from '@element-plus/components/tooltip'
import { useDeprecateAppendToBody } from '@element-plus/components/popper'
import { isString } from '@element-plus/utils'
import { useNamespace } from '@element-plus/hooks'
import { usePopoverProps } from './popover'

import type { StyleValue } from 'vue'

const emits = ['update:visible', 'after-enter', 'after-leave']

const COMPONENT_NAME = 'ElPopover'

export default defineComponent({
  name: COMPONENT_NAME,
  components: {
    ElTooltip,
  },
  props: usePopoverProps,
  emits,
  setup(props, { emit }) {
    const ns = useNamespace('popover')
    const tooltipRef = ref<InstanceType<typeof ElTooltip> | null>(null)
    const popperRef = computed(() => {
      return unref(tooltipRef)?.popperRef
    })
    const width = computed(() => {
      if (isString(props.width)) {
        return props.width as string
      }
      return `${props.width}px`
    })

    const style = computed(() => {
      return [
        {
          width: width.value,
        },
        props.popperStyle,
      ] as StyleValue
    })

    const kls = computed(() => {
      return [ns.b(), props.popperClass, { [ns.m('plain')]: !!props.content }]
    })

    const { compatTeleported } = useDeprecateAppendToBody(
      COMPONENT_NAME,
      'appendToBody'
    )

    const hide = () => {
      tooltipRef.value?.hide()
    }

    const afterEnter = () => {
      emit('after-enter')
    }

    const afterLeave = () => {
      emit('after-leave')
    }

    return {
      compatTeleported,
      ns,
      kls,
      style,
      tooltipRef,
      popperRef,
      hide,
      afterEnter,
      afterLeave,
    }
  },
})
</script>
