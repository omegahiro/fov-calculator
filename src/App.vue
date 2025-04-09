<script setup>
import { ref, computed } from 'vue'

// 入力用のref変数（双方向バインディング用）
const u = ref('')
const v = ref('')
const horizontalFovDeg = ref('69.0')  // 初期値：69度
const verticalFovDeg = ref('51.0')    // 初期値：51度

// ラジアンへの変換関数
function toRadians(degrees) {
  return degrees * Math.PI / 180
}

// 距離計算用の computed プロパティ
const results = computed(() => {
  // 入力値を数値に変換
  const uVal = parseFloat(u.value)
  const vVal = parseFloat(v.value)
  const hFov = parseFloat(horizontalFovDeg.value)
  const vFov = parseFloat(verticalFovDeg.value)

  // 入力のバリデーション
  if (
    isNaN(uVal) || isNaN(vVal) || isNaN(hFov) || isNaN(vFov) ||
    uVal <= 0 || vVal <= 0 || hFov <= 0 || vFov <= 0 ||
    hFov >= 180 || vFov >= 180
  ) {
    return null
  }

  // FoVをラジアンに変換
  const horizontalFovRad = toRadians(hFov)
  const verticalFovRad = toRadians(vFov)

  // 各方向の距離を計算
  const horizontalDistance = (uVal / 2) / Math.tan(horizontalFovRad / 2)
  const verticalDistance = (vVal / 2) / Math.tan(verticalFovRad / 2)

  // 必要距離（両方をカバーするための最大値）
  const requiredDistance = Math.max(horizontalDistance, verticalDistance)

  return {
    horizontalDistance,
    verticalDistance,
    requiredDistance
  }
})
</script>

<template>
  <div style="max-width: 650px; margin: auto; padding: 2rem; font-family: sans-serif;">
    <h1>必要距離計算アプリ</h1>

    <p>カメラ視野角（FoV）と撮影範囲から、カメラからの最小距離を計算します。</p>

    <div style="margin-top: 1rem;">
      <label>
        📐 水平方向のFoV [度]:
        <input type="number" v-model="horizontalFovDeg" min="1" max="179" step="0.1" />
      </label>
    </div>

    <div style="margin-top: 1rem;">
      <label>
        📐 垂直方向のFoV [度]:
        <input type="number" v-model="verticalFovDeg" min="1" max="179" step="0.1" />
      </label>
    </div>

    <div style="margin-top: 2rem;">
      <label>
        ↔️ 撮影範囲（水平 u）[m]:
        <input type="number" v-model="u" min="0" step="0.01" />
      </label>
    </div>

    <div style="margin-top: 1rem;">
      <label>
        ↕️ 撮影範囲（垂直 v）[m]:
        <input type="number" v-model="v" min="0" step="0.01" />
      </label>
    </div>

    <div v-if="results !== null" style="margin-top: 2rem;">
      <h2>✅ 計算結果</h2>
      <ul>
        <li>🔹 水平方向の必要距離: <strong>{{ results.horizontalDistance.toFixed(2) }} m</strong></li>
        <li>🔹 垂直方向の必要距離: <strong>{{ results.verticalDistance.toFixed(2) }} m</strong></li>
        <li>🔸 <strong>必要な距離（両方カバー）: {{ results.requiredDistance.toFixed(2) }} m</strong></li>
      </ul>
    </div>

    <div v-else style="margin-top: 2rem; color: gray;">
      有効な入力（u, v, FoV）をすべて数値で正しく入力してください。<br>
      ※ FoV は 180度未満である必要があります。
    </div>
  </div>
</template>
