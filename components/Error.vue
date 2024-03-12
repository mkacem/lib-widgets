<template>
  <div class="error" v-if="$config.public.DEBUG">
    <h2 v-if="template.appErrorMsg">
      {{ $t('SOMETHING_WRONG') }}  {{ template.appErrorMsg }}
    </h2>
    <h2 v-else>{{ $t('WIDGET_NOT_FOUND') }}</h2>
    
    <pre>{{ data }}</pre>
  </div>
</template>
<style scoped>
.error {
  color: red;
}

</style>
<script setup>
  defineProps({
    template: {
      type: Object,
      required: false
    },
    data: {
      type: Object,
      required: false
    },
    loadingData: {
      type: Boolean,
      required: false
    }
  })
</script>
