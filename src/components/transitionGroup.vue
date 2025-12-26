<template>
  <transition-group name="fade" appear="true">
    <slot></slot>
  </transition-group>
  
</template>
<style scoped>
.fade-enter-active, .fade-leave-active {
opacity : 0;
}
.fade-enter-from{
  opacity: 0;
  transform: translateX(10px);
}

.fade-leave-to {
    opacity: 0;
    transform: translateX(-10px);
}
</style>