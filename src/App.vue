<template>
  <div class="flex flex-col gap-2 rounded-2xl border border-[--border-2] overflow-hidden">
    <div class="relative flex items-center bg-[--fill-1] rounded-t-2xl h-[68px]">
      <div class="z-10 flex-1 text-center px-[108px] py-4 text-[--text-2] text-2xl font-semibold cursor-pointer"
        v-for="item in tabs" :key="item" @click="changeTab(item)" :class="{ 'active-tab': activeTab === item }">
        {{ item }}
      </div>
      <div class="tab-indicator-container" :class="activeTab.toLowerCase()">
        <div class="g-inner" :class="activeTab.toLowerCase()"></div>
      </div>
    </div>
    <div class="p-6">
      <div v-if="activeTab === 'Buy'" class="flex flex-col gap-40">
        <div class="flex flex-col gap-4">
          <div
            class="flex flex-col items-start gap-2 p-4 border border-[--border-2] rounded transition-colors duration-200"
            :class="{ 'focused-border': isInputFocused }">
            <span class="text-sm leading-[22px] text-[--text-1]">You pay</span>
            <el-input size="large" v-model="payTotal" type="number" placeholder="10-100000"
              @focus="isInputFocused = true" @blur="handlePayBlur">
              <template #suffix>
                <div class="flex items-center gap-2">
                  <span>icon</span>
                  <span class="text-sm leading-[22px] font-semibold text-[--text-1]">CNY</span>
                  <span class="">icon</span>
                </div>
              </template>
            </el-input>
          </div>
          <div
            class="flex flex-col items-start gap-2 p-4 border border-[--border-2] rounded transition-colors duration-200"
            :class="{ 'focused-border': isInputFocused2 }">
            <span class="text-sm leading-[22px] text-[--text-1]">You Receive</span>
            <el-input size="large" v-model="receiveTotal" type="number" placeholder="10-100000"
              @focus="isInputFocused2 = true" @blur="handleReceiveBlur">
              <template #suffix>
                <div class="flex items-center gap-2">
                  <span>icon</span>
                  <span class="text-sm leading-[22px] font-semibold text-[--text-1]">BTC</span>
                  <span class="">icon</span>
                </div>
              </template>
            </el-input>
          </div>
        </div>
        <div class="flex flex-col gap-3">
          <div class="flex items-center gap-1">
            <span class="text-sm leading-[22px] text-[--text-5]">Estimated price</span>
            <span class="text-sm">icon</span>
            <span class="text-sm leading-[22px] text-[--text-1]">1 BTC ≈ 751,849.31 CNY</span>
          </div>
          <div
            class="flex items-center justify-center h-[42px] bg-[--green-1] text-xl font-semibold rounded-sm text-[--white-1] cursor-pointer hover:bg-[--green-1-60]">
            Select Payment
            Method</div>
        </div>
      </div>
      <div v-else>it's waitting you coding</div>
    </div>

  </div>
</template>
<script setup>
import { ref, computed } from 'vue'
const tabs = ref(['Buy', 'Sell'])
const activeTab = ref('Buy')
const payTotal = ref('')
const isInputFocused = ref(false)
const isInputFocused2 = ref(false)
const PARITIES = ref(7.1); // 汇率
const MIN_AMOUNT = 10;
const MAX_AMOUNT = 100000;

const receiveTotal = computed({
  get() {
    const payValue = parseFloat(payTotal.value);
    if (isNaN(payValue)) {
      return '';
    }
    // 为加密货币保留更多小数位
    const result = payValue / PARITIES.value;
    return isNaN(result) ? '' : result.toFixed(8);
  },
  set(newReceiveValue) {
    const receiveValue = parseFloat(newReceiveValue);
    if (isNaN(receiveValue)) {
      payTotal.value = '';
      return;
    }
    // 假设法币为两位小数
    const result = receiveValue * PARITIES.value;
    payTotal.value = isNaN(result) ? '' : result.toFixed(2);
  }
});
const changeTab = (current) => {
  activeTab.value = current
}
function handlePayBlur() {
  isInputFocused.value = false;
  const payValue = parseFloat(payTotal.value);
  if (isNaN(payValue)) return;

  if (payValue < MIN_AMOUNT) {
    payTotal.value = MIN_AMOUNT.toFixed(2);
  } else if (payValue > MAX_AMOUNT) {
    payTotal.value = MAX_AMOUNT.toFixed(2);
  }
}

function handleReceiveBlur() {
  isInputFocused2.value = false;
  const receiveValue = parseFloat(receiveTotal.value);
  if (isNaN(receiveValue)) return;

  const correspondingPay = receiveValue * PARITIES.value;

  if (correspondingPay < MIN_AMOUNT) {
    // 如果反算出的pay值小于最小限制，则将receive的值修正为对应最小pay的值
    receiveTotal.value = (MIN_AMOUNT / PARITIES.value).toFixed(8);
  } else if (correspondingPay > MAX_AMOUNT) {
    // 如果反算出的pay值大于最大限制，则将receive的值修正为对应最大pay的值
    receiveTotal.value = (MAX_AMOUNT / PARITIES.value).toFixed(8);
  }
}
</script>
<style lang="scss" scoped>
.active-tab {
  color: var(--text-1);
}

.focused-border {
  border-color: green;
}

.tab-indicator-container {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 50%;
  height: 100%;
  transition: left 0.3s ease-in-out;
  display: flex;
  justify-content: center;
  align-items: flex-end;
  pointer-events: none;

  &.sell {
    left: 50%;
  }
}

.g-inner {
  position: relative;
  width: 100%;
  height: 100%;
  background: var(--white-1);
  border-radius: 0 20px 0 0;
  transform: perspective(40px) scaleX(1.2) scaleY(1.4) rotateX(12deg) translate(-12px, 0);
  transform-origin: 50% 100%;

  &::before {
    content: "";
    position: absolute;
    right: -10px;
    bottom: -.5px;
    width: 10px;
    height: 10px;
    background: inherit;
    mask: radial-gradient(circle at 100% 0, transparent, transparent 9.5px, #000 10px, #000);
  }
}

.g-inner.sell {
  border-radius: 20px 0 0 0;
  transform: perspective(40px) scaleX(1.2) scaleY(1.4) rotateX(12deg) translate(12px, 0);

  &::before {
    right: auto;
    left: -10px;
    mask: radial-gradient(circle at 0% 0, transparent, transparent 9.5px, #000 10px, #000);
  }
}

:deep(.el-input) {
  font-size: 20px;
}

:deep(.el-input__wrapper) {
  padding: 1px 0;
  box-shadow: none;
}

:deep(.el-input__inner) {
  caret-color: var(--text-1);
  color: var(--text-1);
}

:deep(.el-input__inner::placeholder) {
  font-size: 20px;
  color: var(--text-4);
  font-weight: 600;
  line-height: 28px;
}

/* 隐藏数字输入框的步进按钮 */
:deep(.el-input__inner[type=number]) {
  -moz-appearance: textfield;
}

:deep(.el-input__inner::-webkit-outer-spin-button),
:deep(.el-input__inner::-webkit-inner-spin-button) {
  -webkit-appearance: none;
  margin: 0;
}
</style>