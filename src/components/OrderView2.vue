<template>
    <div class="flex items-center justify-center min-h-screen p-4">
        <el-card class="w-[548px]">
            <el-steps :active="activeStep" direction="vertical" process-status="process" finish-status="success">
                <!-- 步骤一 -->
                <el-step ref="step1">
                    <template #title>
                        <div class="flex pb-4 items-center gap-3 cursor-pointer" @click="toggleStep('step1')">
                            <span class="text-base text-[--text-default-2]">订单已创建</span>
                            <el-icon>
                                <ArrowUp v-if="expandedStep === 'step1'" />
                                <ArrowDown v-else />
                            </el-icon>
                        </div>
                    </template>
                    <template #description>
                        <div v-show="expandedStep === 'step1'" class="text-xs mb-6 leading-5 text-[--text-default-1]">
                            订单创建的详细信息可以放在这里。当内容增加时，步骤条会自动适应高度。
                        </div>
                    </template>
                </el-step>

                <!-- 步骤二 -->
                <el-step ref="step2">
                    <template #title>
                        <div class="flex pb-4 items-center justify-between cursor-pointer" @click="toggleStep('step2')">
                            <div class="flex items-center gap-3">
                                <span class="text-base text-[--text-default-2]">通过</span>
                                <div class="w-1 h-3 rounded-sm bg-[--bg-green-2]"></div>
                                <span class="-ml-1 text-base font-semibold text-[--text-default-2]">微信支付</span>
                                <el-icon>
                                    <Switch />
                                </el-icon>
                            </div>
                            <div class="flex items-center gap-1">
                                <span @click.stop class="text-sm leading-[22px] text-[--text-default-2]">
                                    支付提示
                                </span>
                                <el-icon>
                                    <ArrowUp v-if="expandedStep === 'step2'" />
                                    <ArrowDown v-else />
                                </el-icon>
                            </div>
                        </div>
                    </template>
                    <template #description>
                        <div v-show="expandedStep === 'step2'" class="mt-2 mb-6 p-4 border border-[--border-2] rounded">
                            <div class="flex flex-col gap-4">
                                <div class="flex items-center justify-between">
                                    <span class="text-sm leading-[22px] text-[--text-default-1]">You Pay</span>
                                    <div class="flex items-center gap-1">
                                        <span class="text-base font-semibold text-[--text-default-rase]">￥ </span>
                                        <span class="text-base font-semibold text-[--text-default-rase]">
                                            111.00</span>
                                        <span>icon</span>
                                    </div>
                                </div>
                                <div class="flex items-center justify-between">
                                    <span class="text-sm leading-[22px] text-[--text-default-1]">Name</span>
                                    <div class="flex items-center">
                                        <span class="text-sm leading-[22px] text-[--text-default-2]">
                                            卢泽兰（付款请勿备注）</span>
                                        <span>icon</span>
                                    </div>
                                </div>
                                <div class="flex items-center justify-between">
                                    <span class="text-sm leading-[22px] text-[--text-default-1]">WeChat
                                        Account</span>
                                    <div class="flex items-center">
                                        <span class="text-sm leading-[22px] text-[--text-default-2]">
                                            yixin52222101</span>
                                        <span>icon</span>
                                    </div>
                                </div>
                                <div class="flex items-center justify-between">
                                    <span class="text-sm leading-[22px] text-[--text-default-1]">Receive QR
                                        code</span>
                                    <el-popover trigger="hover" placement="bottom" :show-arrow=false
                                        :popper-style="{ padding: '12px 16px', border: 'none' }">
                                        <template #reference> <span class="cursor-pointer">icon</span></template>
                                        <div class="flex flex-col">
                                            <div class="flex flex-col items-center">
                                                <img src="https://api.qrserver.com/v1/create-qr-code/?size=120x120&data=yixin52222101"
                                                    alt="QR Code" class="w-30 h-30" />
                                                <el-button size="small" :icon="Download" class="mt-2">保存</el-button>
                                            </div>

                                        </div>
                                    </el-popover>
                                </div>
                            </div>
                        </div>
                    </template>
                </el-step>
                <!-- 步骤三 -->
                <el-step ref="step3">
                    <template #title>
                        <div class="flex items-center gap-3 cursor-pointer" @click="toggleStep('step3')">
                            <span class="text-base text-[--text-default-2]">Notify Seller</span>
                            <el-icon>
                                <ArrowUp v-if="expandedStep === 'step3'" />
                                <ArrowDown v-else />
                            </el-icon>
                        </div>
                    </template>
                    <template #description>
                        <div class="flex flex-col gap-4">
                            <div class="text-xs leading-5 text-[--text-default-1]">
                                After payment, click the button below so the seller can release the crypto.
                            </div>
                            <div class="flex items-center gap-2">
                                <el-button class="cursor-pointer text-[--text-default-2]">Transferred, Notify
                                    Seller</el-button>
                                <el-button text color="var(--text-default-primary)"
                                    class="cursor-pointer text-[--text-default-primary]">Cancel Order</el-button>
                            </div>
                        </div>
                    </template>
                </el-step>
            </el-steps>
        </el-card>
    </div>
</template>

<script setup>
import { ref, watch, onMounted, nextTick, onUnmounted } from 'vue'
import {
    Switch,
    Download,
    ArrowUp,
    ArrowDown
} from '@element-plus/icons-vue';

const activeStep = ref(2);
const expandedStep = ref('step2');
const step1 = ref(null);
const step2 = ref(null);
const initialStepHeight = 50;

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

<style lang="scss" scoped>
.el-step__main {
    padding-left: 12px;
    flex-grow: 1;
}


.el-step__head {
    transition: height 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.el-step.is-success .el-step__line {
    background-color: var(--text-default-primary);
}

:deep(.el-step__description) {
    padding-right: 0;
}

:deep(.el-popper) {
    min-width: 0;
}

:deep(.el-step__icon) {
    border: none;
    background-color: var(--origin-60);
}
</style>