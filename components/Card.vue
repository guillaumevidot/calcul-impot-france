<template>
  <div class="bg-white shadow-lg rounded-xl overflow-hidden p-2 mb-2 mt-4">
    <!-- Header Slot -->
    <header v-if="$slots.header" class="px-6 py-4 border-b border-gray-200">
      <slot name="header"></slot>
    </header>

    <!-- Main Content Slot -->
    <div class="px-6 py-3">
      <slot></slot>
    </div>
  </div>
</template>