<template>
  <component :is="name + '-icon'" />
</template>

<script>
import StarIcon from "@/components/icons/StarIcon.vue"
import FullPathIcon from "@/components/icons/FullPathIcon.vue"

export default {
  props: {
    name: {
      type: String,
      required: true,
    },
  },
  components: {
    StarIcon,
    FullPathIcon,
  }
}
</script>
