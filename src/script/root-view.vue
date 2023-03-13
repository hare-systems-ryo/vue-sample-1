<script setup lang="ts">
import dayjs from 'dayjs';
import { reactive, computed, ComputedRef, ref, Ref } from 'vue';
const TimeStamp = () => {
  return dayjs().format('YYYY-MM-DD HH:mm:ss.SSS');
};

interface State {
  listA: {
    [key: string]: ListRowA;
  };
  listB: {
    [key: string]: ListRowB;
  };
}

interface ListRowA {
  data: { a: number; b: number };
  sum: ComputedRef<number> | Ref<number>;
}
interface ListRowB {
  data: { a: number; b: number };
}

const state = reactive<State>({
  listA: {},
  listB: {},
});

const InitDataA = (): ListRowA => {
  return {
    data: { a: 0, b: 0 },
    sum: ref(0),
  };
};

const InitDataB = (): ListRowB => {
  return {
    data: { a: 0, b: 0 },
  };
};

const tsListA = computed(() => {
  return Object.keys(state.listA);
});

const tsListB = computed(() => {
  return Object.keys(state.listB);
});

const sumListB = computed(() => (ts: string, row: ListRowB) => {
  console.log('listB', ts, 'computed', row);
  return row.data.a + row.data.b;
});
const sumList = computed(() => {
  console.log('sumList');
  return Object.keys(state.listB)
    .map((key) => {
      return {
        key: key,
        value: state.listB[key].data.a + state.listB[key].data.b,
      };
    })
    .reduce((ret, row) => {
      ret[row.key] = row.value;
      return ret;
    }, {} as { [key: string]: number });
});

const sumListBFunc = (ts: string, a: number, b: number) => {
  console.log('listB', ts, 'computed', a, b);
  return a + b;
};

const appendListA = () => {
  const ts = TimeStamp();
  const row = InitDataA();
  state.listA[ts] = row;
  state.listA[ts].sum = computed(() => {
    console.log('listA', ts, 'computed', state.listA[ts].data);
    return state.listA[ts].data.a + state.listA[ts].data.b;
  });
};

const appendListB = () => {
  const ts = TimeStamp();
  const row = InitDataB();
  state.listB[ts] = row;
};

appendListA();
appendListB();
setTimeout(() => {
  appendListA();
  appendListB();
}, 10);

class Hoge {
  public state = reactive({ step: 0 });
  public step() {
    this.state.step += 1;
  }
}
const hoge = new Hoge();
</script>
<template>
  <div class="container-fluid">
    <div class="" @click="hoge.step()">hoge{{ hoge.state.step }}</div>
    <div class="card mt-3">
      <div class="card-header text-white bg-primary">
        Vue reactiveの中にcomputed
      </div>
      <div class="card-body">
        <button type="button" class="btn btn-primary mb-2" @click="appendListA">
          AppendListA
        </button>
        <div class="item"></div>
        <template v-for="(ts, index) in tsListA" :key="index">
          <div class="card mb-2">
            <div class="card-header">{{ ts }}</div>
            <div class="card-body">
              <div class="row">
                <div class="col-6">
                  <label class="form-label">
                    a :{{ state.listA[ts].data.a }}
                  </label>
                  <input
                    type="range"
                    class="form-range"
                    min="0"
                    max="100"
                    v-model.number="state.listA[ts].data.a"
                  />
                </div>
                <div class="col-6">
                  <label class="form-label">
                    b :{{ state.listA[ts].data.b }}
                  </label>
                  <input
                    type="range"
                    class="form-range"
                    min="0"
                    max="100"
                    v-model.number="state.listA[ts].data.b"
                  />
                </div>
                <div class="">
                  <div>data: {{ state.listA[ts].data }}</div>
                  <div>sum: {{ state.listA[ts].sum }}</div>
                </div>
              </div>
            </div>
          </div>
        </template>
      </div>
    </div>
    <div class="card mt-3">
      <div class="card-header text-white bg-primary">Vue 引数付きcomputed</div>
      <div class="card-body">
        <button type="button" class="btn btn-primary mb-2" @click="appendListB">
          AppendListB
        </button>
        <div class="item"></div>
        <template v-for="(ts, index) in tsListB" :key="index">
          <div class="card mb-2">
            <div class="card-header">{{ ts }}</div>
            <div class="card-body">
              <div class="row">
                <div class="col-6">
                  <label class="form-label">
                    a :{{ state.listB[ts].data.a }}
                  </label>
                  <input
                    type="range"
                    class="form-range"
                    min="0"
                    max="100"
                    v-model.number="state.listB[ts].data.a"
                  />
                </div>
                <div class="col-6">
                  <label class="form-label">
                    b :{{ state.listB[ts].data.b }}
                  </label>
                  <input
                    type="range"
                    class="form-range"
                    min="0"
                    max="100"
                    v-model.number="state.listB[ts].data.b"
                  />
                </div>
                <div class="">
                  <div>data: {{ state.listB[ts].data }}</div>
                  <div>
                    sum:
                    {{ sumListB(ts, state.listB[ts]) }}
                  </div>
                </div>
              </div>
            </div>
          </div>
        </template>
      </div>
      {{ sumList }}
    </div>
  </div>
  <teleport to="#teleport"></teleport>
</template>

<style lang="scss" scoped>
.item:not(:last-child) {
  margin-top: 20px;
}

.card,
.card-header,
.card-body {
  border-color: rgb(24, 7, 112);
}
</style>
