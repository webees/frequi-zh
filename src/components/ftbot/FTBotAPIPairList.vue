<template>
  <!-- TODO We could move the list into a component since we are reusing the same code for both lists. -->
  <div>
    <div>
      <h3>过滤方式</h3>

      <div v-if="botStore.activeBot.pairlistMethods.length" class="list">
        <b-list-group v-for="(method, key) in botStore.activeBot.pairlistMethods" :key="key">
          <b-list-group-item href="#" class="pair white">{{ method }}</b-list-group-item>
        </b-list-group>
      </div>
    </div>
    <!-- Show Whitelist -->
    <h3 :title="`${botStore.activeBot.whitelist.length} pairs`">白名单</h3>
    <div v-if="botStore.activeBot.whitelist.length" class="list">
      <b-list-group v-for="(pair, key) in botStore.activeBot.whitelist" :key="key">
        <b-list-group-item class="pair white">{{ pair }}</b-list-group-item>
      </b-list-group>
    </div>
    <p v-else>白名单不可用。请登录并确保服务器正在运行。</p>
    <hr />

    <!-- Blacklsit -->
    <div>
      <label
        class="me-auto h3"
        title="Blacklist - Select (followed by a click on '-') to remove pairs"
        >黑名单</label
      >
      <div class="float-end d-flex d-flex-columns pe-1">
        <b-button
          id="blacklist-add-btn"
          class="me-1"
          :class="botStore.activeBot.botApiVersion >= 1.12 ? 'col-6' : ''"
          size="sm"
          ><i-mdi-plus-box-outline />
        </b-button>
        <b-button
          v-if="botStore.activeBot.botApiVersion >= 1.12"
          size="sm"
          class="col-6"
          title="Select pairs to delete pairs from your blacklist."
          :disabled="blacklistSelect.length === 0"
          @click="deletePairs"
        >
          <i-mdi-delete />
        </b-button>
      </div>
      <b-popover
        title="添加黑名单"
        target="blacklist-add-btn"
        triggers="click"
        :show="blackListShow"
      >
        <form ref="form" @submit.prevent>
          <div>
            <b-form-group label-cols="3" label="交易对" label-for="pair-input">
              <b-form-input
                id="pair-input"
                v-model="newblacklistpair"
                required
                autofocus
              ></b-form-input>
            </b-form-group>
            <b-button
              id="blacklist-submit"
              class="float-end mb-2"
              size="sm"
              type="submit"
              @click="addBlacklistPair"
            >
              添加</b-button
            >
          </div>
        </form>
      </b-popover>
    </div>
    <div v-if="botStore.activeBot.blacklist.length" class="list">
      <b-list-group v-for="(pair, key) in botStore.activeBot.blacklist" :key="key">
        <b-list-group-item
          class="pair black"
          :active="blacklistSelect.indexOf(key) > -1"
          @click="blacklistSelectClick(key)"
          ><span class="check"><i-mdi-check-circle /></span>{{ pair }}</b-list-group-item
        >
      </b-list-group>
    </div>
    <p v-else>黑名单不可用。请登录并确保服务器正在运行。</p>
    <!-- Pagination -->
    <!-- TODO Add pagination support -->
  </div>
</template>

<script setup lang="ts">
import { useBotStore } from '@/stores/ftbotwrapper';
import { onMounted, ref } from 'vue';

const newblacklistpair = ref('');
const blackListShow = ref(false);
const blacklistSelect = ref<number[]>([]);
const botStore = useBotStore();

const initBlacklist = () => {
  if (botStore.activeBot.whitelist.length === 0) {
    botStore.activeBot.getWhitelist();
  }
  if (botStore.activeBot.blacklist.length === 0) {
    botStore.activeBot.getBlacklist();
  }
};

const addBlacklistPair = () => {
  if (newblacklistpair.value) {
    blackListShow.value = false;

    botStore.activeBot.addBlacklist({ blacklist: [newblacklistpair.value] });
    newblacklistpair.value = '';
  }
};

const blacklistSelectClick = (key) => {
  console.log(key);
  const index = blacklistSelect.value.indexOf(key);
  if (index > -1) {
    blacklistSelect.value.splice(index, 1);
  } else {
    blacklistSelect.value.push(key);
  }
};

const deletePairs = () => {
  if (blacklistSelect.value.length === 0) {
    console.log('nothing to delete');
    return;
  }
  // const pairlist = blacklistSelect.value;
  const pairlist = botStore.activeBot.blacklist.filter(
    (value, index) => blacklistSelect.value.indexOf(index) > -1,
  );
  console.log('Deleting pairs: ', pairlist);
  botStore.activeBot.deleteBlacklist(pairlist);
  blacklistSelect.value = [];
};
onMounted(() => {
  initBlacklist();
});
</script>

<style scoped lang="scss">
.check {
  // Hidden checkbox on blacklist selection
  // background: white;
  color: #41b883;
  opacity: 0;
  // border-radius: 50%;
  z-index: 5;
  width: 1.3em;
  height: 1.3em;
  top: -0.3em;
  left: -0.3em;
  position: absolute;
  transition: opacity 0.2s;
}

.list-group-item.active .check {
  opacity: 1;
}

.list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(110px, 1fr));
  grid-gap: 0.5rem;
  padding-bottom: 1rem;
}

.pair {
  border: 1px solid #ccc;
  background: #41b883;
  padding: 0.5rem;
  border-radius: 5px;
  text-align: center;
  position: relative;
  cursor: pointer;
}

.white {
  background: white;
  color: black;
}

.black {
  background: black;
  color: white;
}
</style>
