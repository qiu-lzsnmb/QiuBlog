<template>
  <div class="layout-header">
    <div class="menuLeft">
      <n-icon size="27" @click="() => $emit('collapsed')" style="">
        <MenuSharp />
      </n-icon>
      <n-icon size="25" @click="shareShow = true">
        <ShareSocialOutline />
      </n-icon>
    </div>
    <div class="menuRight">
      <n-switch v-model:value="darkMode" :rail-style="railStyle">
        <template #checked>δΈη­</template>
        <template #unchecked>δΈη­</template>
        <template #icon>{{ darkMode ? "π" : "π" }}</template>
      </n-switch>
      <n-icon size="25" @click="locale = !locale"><Language /></n-icon>
      <n-icon size="25"><ColorPalette /></n-icon>
    </div>
    <n-modal v-model:show="shareShow">
      <vue-qrcode :value="url" :options="{ width: 300 }"></vue-qrcode>
    </n-modal>
  </div>
</template>

<script setup>
import { ref } from "vue";
import {
  ColorPalette,
  Language,
  ShareSocialOutline,
  MenuSharp,
} from "@vicons/ionicons5";
import { useDesignSettingStore } from "@/store/modules/designSetting.js";

const shareShow = ref(false);
const designStore = useDesignSettingStore();

const url = window.location.href;
//ζι»ζ¨‘εΌ
const darkMode = computed({
  get: () => designStore.getDarkTheme,
  set: (val) => designStore.setDarkTheme(val),
});
//ε½ιεθ―­θ¨
const locale = computed({
  get: () => designStore.getLocale,
  set: (val) => designStore.setLocale(val),
});
defineProps(["collapsed"]);

const railStyle = ({ focused, checked }) => {
  const style = {};
  if (checked) {
    style.background = "#d03050";
    if (focused) {
      style.boxShadow = "0 0 0 2px #d0305040";
    }
  } else {
    style.background = "#2080f0";
    if (focused) {
      style.boxShadow = "0 0 0 2px #2080f040";
    }
  }
  return style;
};
</script>

<style lang="scss" scoped>
.layout-header {
  height: 7vh;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0;
  transition: all 0.2s ease-in-out;
  width: 100%;
  z-index: 500;
}

.menuLeft {
  display: flex;
  align-items: center;
  padding-left: 15px;
}

.menuRight {
  display: flex;
  align-items: center;
  padding-right: 25px;
}

.n-icon {
  cursor: pointer;
}
</style>
