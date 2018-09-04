<template>
  <div class="mint-search">
    <div class="mint-searchbar">
      <div class="mint-searchbar-inner">
        <i class="mintui mintui-search"></i>
          <!-- currentValue与接受的value值同步更新 -->
        <input class="mint-searchbar-core" ref="input" @click="visible = true" type="search" v-model="currentValue" :placeholder="placeholder">
      </div>
        <!-- 取消按钮,点击时清空文本并且visible置为false -->
      <a class="mint-searchbar-cancel" @click="visible = false, currentValue = ''" v-show="visible">{{cancelText}}</a>
    </div>
    <div class="mint-search-list" v-show="visible">
      <div class="mint-search-list-warp">
        <slot>
          <x-cell v-for="(item, index) in result" :key="index" :title="item"></x-cell>
        </slot>
      </div>
    </div>
  </div>
</template>

<script>
import XCell from 'mint-ui/packages/cell/index.js';
if (process.env.NODE_ENV === 'component') {
  require('mint-ui/packages/cell/style.css');
}

/**
 *  这里需要重写一下Mini-ui的Search组件 原因是原有的子组件与父组件之间通过.sync来实现
 *  双向绑定的方法从Vue 2.3+ 开始已经修改成了语法糖，因此原有的通信方式已经无法使用
 *  这里已经在github 上提出了 详情可以查看 https://github.com/ElemeFE/mint-ui/issues/888
 */
export default {
  name: 'mt-search',
  data() {
    return {
      visible: false,
      currentValue: this.value
    };
  },

  components: { XCell },

  watch: {
    currentValue(val) {
      this.$emit('input', val);
    },
    value(val) {
      this.currentValue = val;
    },
    //  visible变量在输入框被点击时置为true
    //  监听该变量,一旦发生改变,则与之绑定的父组件中的searchVisible也发生改变,来模拟子向父通信
    visible() {
      this.$emit('update:visible', this.visible);
    }
  },

  props: {
    // 通过v-model="searchValue" 接收
    value: String,
    autofocus: Boolean,
    show: Boolean,
    cancelText: {
      default: '取消'
    },
    placeholder: {
      default: '搜索'
    },
    result: Array
  },

  mounted() {
    this.autofocus && this.$refs.input.focus();
  }
};
</script>
