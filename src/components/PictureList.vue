<script setup lang="ts">
import { withDefaults, toRefs, defineProps } from "vue";

interface Props {
  pictureList: any[];
}

const props = withDefaults(defineProps<Props>(), {
  pictureList: () => [],
});
</script>

<template>
  Pictures
  <a-list item-layout="horizontal" :data-source="props.pictureList">
    <template #renderItem="{ item }">
      <a-list-item>
        <a-list-item-meta :description="item.content">
          <template #title>
            <a>{{ item.title }}</a>
          </template>
          <template #avatar>
            <a-avatar class="im" :src="item.url" />
          </template>
        </a-list-item-meta>
      </a-list-item>
    </template>
  </a-list>
</template>

<style scoped>
.im {
  width: 100px;
  height: 100px;
}
</style>
