<template>
  <div class="slidev-layout lblue-fact">
    <slot></slot>
  </div>
</template>

<style scoped lang="postcss">
.slidev-layout.lblue-fact {
  @apply text-white bg-#00ADD8 font-size-10 h-full text-center grid place-content-center select-none;
}
</style>