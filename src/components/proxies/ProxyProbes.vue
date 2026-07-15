<template>
  <div
    v-if="probes.length"
    class="flex w-full items-center gap-0.5"
    @mouseenter="handlerProbesTip"
  >
    <span
      v-for="(probe, index) in probes"
      :key="index"
      :class="
        twMerge('h-1 flex-1 rounded-full bg-current opacity-70', getColorForProbeClass(probe.class))
      "
    ></span>
  </div>
</template>

<script setup lang="ts">
import { getColorForProbeClass } from '@/helper'
import { useTooltip } from '@/helper/tooltip'
import { getExtendedProbesByName } from '@/assembly/proxies'
import { twMerge } from 'tailwind-merge'
import { computed } from 'vue'

const props = defineProps<{
  name: string
}>()

const { showTip } = useTooltip()
const probes = computed(() => getExtendedProbesByName(props.name))

const handlerProbesTip = (e: Event) => {
  if (!probes.value.length) return

  const list = document.createElement('div')

  list.classList.add('flex', 'flex-col', 'gap-1')
  for (const probe of probes.value) {
    const row = document.createElement('div')
    const target = document.createElement('div')
    const status = document.createElement('div')

    target.textContent = probe.target
    status.textContent = probe.class === 'ok' ? `${probe.delay}ms` : probe.class
    status.className = getColorForProbeClass(probe.class)

    row.classList.add('flex', 'items-center', 'justify-between', 'gap-3')
    row.append(target, status)
    list.append(row)
  }

  showTip(e, list, {
    delay: [500, 0],
    trigger: 'mouseenter',
    touch: false,
  })
}
</script>
