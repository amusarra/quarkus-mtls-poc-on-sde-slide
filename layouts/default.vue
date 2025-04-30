<template>
  <div class="slidev-layout default">
    <!-- Contenuto slide -->
    <div class="flex-1 px-10 py-8">
      <slot/>
    </div>

    <!-- Footer fisso -->
    <!-- Footer fisso in basso -->
    <div class="absolute bottom-0 w-full text-center text-[10px] text-gray-500 py-2">
      Autore: Antonio Musarra - (https://github.com/amusarra | https://www.linkedin.com/in/amusarra/) – CC BY-NC-SA 4.0
    </div>
  </div>
</template>