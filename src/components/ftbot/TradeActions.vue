<template>
  <div class="d-flex flex-column">
    <b-button
      v-if="botApiVersion <= 1.1"
      class="btn-xs text-start"
      size="sm"
      title="Forceexit"
      @click="$emit('forceExit', trade)"
    >
      <i-mdi-close-box class="me-1" />强制卖出
    </b-button>
    <b-button
      v-if="botApiVersion > 1.1"
      class="btn-xs text-start"
      size="sm"
      title="Forceexit limit"
      @click="$emit('forceExit', trade, 'limit')"
    >
      <i-mdi-close-box class="me-1" />强制限价平仓
    </b-button>
    <b-button
      v-if="botApiVersion > 1.1"
      class="btn-xs text-start mt-1"
      size="sm"
      title="Forceexit market"
      @click="$emit('forceExit', trade, 'market')"
    >
      <i-mdi-close-box class="me-1" />强制市价平仓
    </b-button>
    <b-button
      v-if="botApiVersion > 2.16"
      class="btn-xs text-start mt-1"
      size="sm"
      title="Forceexit partial"
      @click="$emit('forceExitPartial', trade)"
    >
      <i-mdi-close-box-multiple class="me-1" />强制部分平仓
    </b-button>
    <b-button
      v-if="botApiVersion >= 2.24 && trade.open_order_id"
      class="btn-xs text-start mt-1"
      size="sm"
      title="Cancel open orders"
      @click="$emit('cancelOpenOrder', trade)"
    >
      <i-mdi-cancel class="me-1" />取消委托
    </b-button>
    <b-button
      v-if="botApiVersion >= 2.28"
      class="btn-xs text-start mt-1"
      size="sm"
      title="Reload"
      @click="$emit('reloadTrade', trade)"
    >
      <i-mdi-reload-alert class="me-1" />重载交易
    </b-button>
    <b-button
      class="btn-xs text-start mt-1"
      size="sm"
      title="Delete trade"
      @click="$emit('deleteTrade', trade)"
    >
      <i-mdi-delete class="me-1" />
      删除
    </b-button>
  </div>
</template>

<script setup lang="ts">
import { Trade } from '@/types';

defineProps({
  botApiVersion: {
    type: Number,
    default: 1.0,
  },
  trade: {
    type: Object as () => Trade,
    required: true,
  },
});
defineEmits(['forceExit', 'forceExitPartial', 'cancelOpenOrder', 'reloadTrade', 'deleteTrade']);
</script>

<style scoped lang="scss"></style>
