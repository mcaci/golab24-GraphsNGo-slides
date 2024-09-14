<template>
  <div class="slidev-layout lblue-end">
    <slot>END</slot>
  </div>
</template>

<style scoped lang="postcss">
.slidev-layout.lblue-end {
  @apply text-black text-opacity-85 text-xl tracking-widest bg-#00ADD8 h-full text-center grid place-content-center select-none font-size-4;
}
</style>