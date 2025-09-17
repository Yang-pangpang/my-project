<template>
    <div class="flex items-center justify-center min-h-screen p-4">
        <el-card class="w-full max-w-2xl shadow-lg">
            <!-- 卡片头部 -->
            <div class="p-6">
                <div class="flex justify-between items-center mb-2">
                    <h1 class="text-2xl font-bold text-gray-800">
                        在 <span class="text-orange-500">{{ timer }}</span> 内向卖家付款
                    </h1>
                </div>
                <div class="text-gray-500 text-sm flex items-center">
                    <span>订单号 22768918089439809536</span>
                    <el-icon class="ml-2 cursor-pointer">
                        <DocumentCopy />
                    </el-icon>
                </div>
            </div>

            <!-- 步骤条和折叠面板结合 -->
            <div class="p-6 pt-0">
                <el-steps :active="activeStep" direction="vertical" process-status="process" finish-status="success">
                    <!-- 步骤一 -->
                    <el-step ref="step1">
                        <template #title>
                            <div class="step-header" @click="toggleStep('step1')">
                                <span class="text-base font-medium">订单已创建</span>
                                <el-icon>
                                    <ArrowUp v-if="expandedStep === 'step1'" />
                                    <ArrowDown v-else />
                                </el-icon>
                            </div>
                        </template>
                        <template #description>
                            <div v-show="expandedStep === 'step1'" class="step-content pt-2 pb-4 pl-3">
                                订单创建的详细信息可以放在这里。当内容增加时，步骤条会自动适应高度。
                            </div>
                        </template>
                    </el-step>

                    <!-- 步骤二 -->
                    <el-step ref="step2">
                        <template #title>
                            <div class="step-header" @click="toggleStep('step2')">
                                <div class="flex items-center text-base font-medium">
                                    <span>通过</span>
                                    <img src="https://placehold.co/20x20/28C445/FFFFFF?text=W"
                                        class="w-5 h-5 mx-1.5 rounded-sm" />
                                    <span>微信支付</span>
                                    <el-icon class="ml-2">
                                        <Switch />
                                    </el-icon>
                                </div>
                                <div class="flex items-center">
                                    <a href=" " @click.stop
                                        class="text-sm text-gray-400 hover:text-blue-500 flex items-center pr-4">
                                        支付提示 <el-icon class="ml-1">
                                            <InfoFilled />
                                        </el-icon>
                                    </a>
                                    <el-icon>
                                        <ArrowUp v-if="expandedStep === 'step2'" />
                                        <ArrowDown v-else />
                                    </el-icon>
                                </div>
                            </div>
                        </template>
                        <template #description>
                            <div v-show="expandedStep === 'step2'" class="step-content pl-3">
                                <!-- 支付详情 -->
                                <div class="bg-gray-50 p-4 rounded-lg mt-2">
                                    <div class="space-y-4 text-sm">
                                        <div class="flex justify-between items-center">
                                            <span class="text-gray-500">您支付</span>
                                            <span class="text-green-600 font-bold text-lg flex items-center">
                                                ¥ 111.00 <el-icon class="ml-2 cursor-pointer">
                                                    <DocumentCopy />
                                                </el-icon>
                                            </span>
                                        </div>
                                        <div class="flex justify-between items-center">
                                            <span class="text-gray-500">姓名</span>
                                            <span class="text-gray-800 flex items-center">
                                                卢泽兰 (付款请勿备注) <el-icon class="ml-2 cursor-pointer">
                                                    <InfoFilled />
                                                </el-icon>
                                            </span>
                                        </div>
                                        <div class="flex justify-between items-center">
                                            <span class="text-gray-500">微信账号</span>
                                            <span class="text-gray-800 flex items-center">
                                                yixin52222101 <el-icon class="ml-2 cursor-pointer">
                                                    <DocumentCopy />
                                                </el-icon>
                                            </span>
                                        </div>
                                        <div class="flex justify-between items-start">
                                            <span class="text-gray-500">收款二维码</span>
                                            <div class="flex flex-col items-center">
                                                <img src="https://api.qrserver.com/v1/create-qr-code/?size=120x120&data=yixin52222101"
                                                    alt="QR Code" class="w-32 h-32 border rounded-md p-1" />
                                                <el-button size="small" :icon="Download" class="mt-2">保存</el-button>
                                            </div>
                                        </div>
                                    </div>
                                </div>

                                <!-- 通知卖家 -->
                                <div class="mt-4 p-4 bg-yellow-50 border border-yellow-200 rounded-lg">
                                    <h3 class="font-bold text-gray-800 flex items-center">
                                        <div
                                            class="w-6 h-6 bg-orange-500 text-white rounded-full flex items-center justify-center mr-2 text-sm">
                                            2</div>
                                        通知卖家
                                    </h3>
                                    <p class="text-gray-600 text-sm mt-1 ml-8">付款后，请点击下面的按钮，以便卖家释放加密货币。</p>
                                </div>

                                <!-- 操作按钮 -->
                                <div class="my-6 flex items-center space-x-4 ml-8">
                                    <el-button type="primary" color="#F59E0B">我已付款，通知卖家</el-button>
                                    <el-button link type="primary">取消订单</el-button>
                                </div>
                            </div>
                        </template>
                    </el-step>

                    <!-- 步骤三 -->
                    <el-step title="等待卖家放行">
                    </el-step>
                </el-steps>
            </div>
        </el-card>
    </div>
</template>

<script setup>
import { ref, computed, watch, onMounted, nextTick, onUnmounted } from 'vue'
import {
    DocumentCopy,
    Switch,
    InfoFilled,
    Download,
    ArrowUp,
    ArrowDown
} from '@element-plus/icons-vue';

const activeStep = ref(1);
const expandedStep = ref('step1');
const step1 = ref(null);
const step2 = ref(null);
const initialStepHeight = 50;

const totalSeconds = ref(14 * 60 + 23);
const timer = computed(() => {
    const minutes = Math.floor(totalSeconds.value / 60);
    const seconds = totalSeconds.value % 60;
    return `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
});

const toggleStep = (stepName) => {
    if (expandedStep.value === stepName) {
        expandedStep.value = null;
    } else {
        expandedStep.value = stepName;
    }
};

const adjustLineHeights = () => {
    nextTick(() => {
        const steps = [{
            component: step1.value,
            name: 'step1'
        }, {
            component: step2.value,
            name: 'step2'
        }];

        steps.forEach(({
            component,
            name
        }) => {
            if (!component || !component.$el) return;

            const headEl = component.$el.querySelector('.el-step__head');
            const mainEl = component.$el.querySelector('.el-step__main');

            if (headEl && mainEl) {
                if (expandedStep.value === name) {
                    headEl.style.height = `${mainEl.offsetHeight}px`;
                } else {
                    headEl.style.height = `${initialStepHeight}px`;
                }
            }
        });
    });
};

watch(expandedStep, adjustLineHeights);

onMounted(() => {
    let interval = setInterval(() => {
        if (totalSeconds.value > 0) totalSeconds.value--;
    }, 1000);

    setTimeout(adjustLineHeights, 0);

    onUnmounted(() => clearInterval(interval));
});
</script>

<style scoped>
/* 自定义样式以更好地匹配图片效果 */
body {
    background-color: #f0f2f5;
    font-family: 'Inter', 'Helvetica Neue', Helvetica, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', Arial, sans-serif;
}

.el-card {
    border: none;
    border-radius: 12px;
}

/* 步骤条样式微调 */
.el-step.is-vertical {
    display: flex;
    align-items: flex-start;
}

.el-step__main {
    padding-left: 10px;
    flex-grow: 1;
}

.el-step__head {
    /* 高度由JS动态控制 */
    transition: height 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    /* 添加高度变化过渡动画 */
}

/* 当步骤完成时，线条也显示为成功色 */
.el-step.is-success .el-step__line {
    background-color: var(--el-color-success);
}

/* 移除默认的 title 和 description 之间的间距 */
.el-step__description {
    padding-top: 0;
}

.el-step__title {
    padding-bottom: 0;
    line-height: inherit;
}

/* 自定义折叠头部 */
.step-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
    cursor: pointer;
    padding: 10px 0;
    font-size: 16px;
    font-weight: 500;
}

.step-content {
    overflow: hidden;
    /* 配合动画效果 */
}
</style>