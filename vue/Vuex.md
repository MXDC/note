---
categories:
  - vue
---
#### Vuex

```javascript
import Vue from "vue";
import Vuex from "vuex";

Vue.use(Vuex);

export default new Vuex.Store({
  state: {},
   mutations: {
    listMemos(state, total) {
      state.memos.total = total;
      console.log(total);
      
    }
  },
  actions: {},
  modules: {}
});
```

`mutations`用于修改并跟踪数据的变化，通过`store.commit('mutations',params)`调用