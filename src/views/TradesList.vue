<template>
  <div>
    <!-- <TradeList
      class="open-trades"
      :trades="openTrades"
      title="当前委托"
      :active-trades="true"
      empty-text="空"
    /> -->
    <CustomTradeList
      v-if="!history && !botStore.activeBot.detailTradeId"
      :trades="botStore.activeBot.openTrades"
      title="当前委托"
      :active-trades="true"
      :stake-currency-decimals="botStore.activeBot.stakeCurrencyDecimals"
      empty-text="空"
    />
    <CustomTradeList
      v-if="history && !botStore.activeBot.detailTradeId"
      :trades="botStore.activeBot.closedTrades"
      title="Trade history"
      :stake-currency-decimals="botStore.activeBot.stakeCurrencyDecimals"
      empty-text="空"
    />
    <div
      v-if="botStore.activeBot.detailTradeId && botStore.activeBot.tradeDetail"
      class="d-flex flex-column"
    >
      <b-button
        size="sm"
        class="align-self-start mt-1 ms-1"
        @click="botStore.activeBot.setDetailTrade(null)"
        ><i-mdi-arrow-left /> Back</b-button
      >
      <TradeDetail
        :trade="botStore.activeBot.tradeDetail"
        :stake-currency="botStore.activeBot.stakeCurrency"
      />
    </div>
  </div>
</template>

<script setup lang="ts">
import CustomTradeList from '@/components/ftbot/CustomTradeList.vue';
import TradeDetail from '@/components/ftbot/TradeDetail.vue';
import { useBotStore } from '@/stores/ftbotwrapper';

defineProps({
  history: { default: false, type: Boolean },
});
const botStore = useBotStore();
</script>

<style scoped></style>
