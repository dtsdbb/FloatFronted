<script setup lang="ts">
import { ref, watchEffect } from "vue";
import { optionHandle } from "./center.map";
import BorderBox13 from "@/components/datav/border-box-13";

import type { MapdataType } from "./center.map";

const option = ref({});
const centerMapRef = ref<any>(null);
const points = ref<MapdataType[]>([
  { name: "湖南理工", value: [113.12931, 29.37197, 100] },
  { name: "中南大学", value: [112.930591, 28.171214, 200] },
]);

withDefaults(
  defineProps<{
    // 结束数值
    title: number | string;
  }>(),
  {
    title: "地图",
  }
);

watchEffect(() => {
  option.value = optionHandle(points.value);
});

const focusPoint = (lng: number, lat: number, zoom = 13) => {
  const chart = centerMapRef.value?.getEchartsInstance?.();
  const amapComponent = chart?.getModel?.().getComponent?.("amap");
  const amap = amapComponent?.getAMap?.();
  if (amap) {
    amap.setZoomAndCenter(zoom, [lng, lat], false, 2000);
    return;
  }
  const current = option.value as any;
  option.value = {
    ...current,
    amap: {
      ...(current?.amap || {}),
      center: [lng, lat],
      zoom,
    },
  };
};

const mapClick = (params: any) => {
  if (params?.seriesType !== "effectScatter") return;
  const value = params?.data?.value;
  if (!Array.isArray(value) || value.length < 2) return;
  const [lng, lat] = value;
  focusPoint(Number(lng), Number(lat), 13);
};
</script>

<template>
  <div class="centermap">
    <div class="maptitle">
      <div class="zuo"></div>
      <span class="titletext">{{ title }}</span>
      <div class="you"></div>
    </div>
    <div class="mapwrap">
      <BorderBox13>
        <v-chart
          class="chart"
          :option="option"
          ref="centerMapRef"
          @click="mapClick"
        />
      </BorderBox13>
    </div>
  </div>
</template>

<style scoped lang="scss">
.centermap {
  margin-bottom: 30px;

  .maptitle {
    height: 60px;
    display: flex;
    justify-content: center;
    padding-top: 10px;
    box-sizing: border-box;

    .titletext {
      font-size: 28px;
      font-weight: 900;
      letter-spacing: 6px;
      background: linear-gradient(92deg, #0072ff 0%, #00eaff 48.8525390625%, #01aaff 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      margin: 0 10px;
    }

    .zuo,
    .you {
      background-size: 100% 100%;
      width: 29px;
      height: 20px;
      margin-top: 8px;
    }

    .zuo {
      background: url("@/assets/img/xiezuo.png") no-repeat;
    }

    .you {
      background: url("@/assets/img/xieyou.png") no-repeat;
    }
  }

  .mapwrap {
    height: 580px;
    width: 100%;
    // padding: 0 0 10px 0;
    box-sizing: border-box;
    position: relative;

    .chart {
      height: 100%;
      width: 100%;
    }

    .quanguo {
      position: absolute;
      right: 20px;
      top: -46px;
      width: 80px;
      height: 28px;
      border: 1px solid #00eded;
      border-radius: 10px;
      color: #00f7f6;
      text-align: center;
      line-height: 26px;
      letter-spacing: 6px;
      cursor: pointer;
      box-shadow: 0 2px 4px rgba(0, 237, 237, 0.5), 0 0 6px rgba(0, 237, 237, 0.4);
      z-index: 10;
    }
  }
}
</style>
