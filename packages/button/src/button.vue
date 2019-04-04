<template>
  <button
    class="el-button"
    @click="handleClick"
    :disabled="buttonDisabled || localLoading"
    :autofocus="autofocus"
    :type="nativeType"
    :class="[
      type ? 'el-button--' + type : '',
      buttonSize ? 'el-button--' + buttonSize : '',
      {
        'is-disabled': buttonDisabled,
        'is-loading': localLoading,
        'is-plain': plain,
        'is-round': round,
        'is-circle': circle
      }
    ]"
  >
    <i class="el-icon-loading" v-if="localLoading"></i>
    <i :class="icon" v-if="icon && !localLoading"></i>
    <span v-if="$slots.default">
      <slot></slot>
    </span>
  </button>
</template>
<script>
export default {
  name: 'ElButton',

  inject: {
    elForm: {
      default: ''
    },
    elFormItem: {
      default: ''
    }
  },

  data() {
    return {
      localLoading: this.loading || false
    };
  },

  props: {
    type: {
      type: String,
      default: 'default'
    },
    size: String,
    icon: {
      type: String,
      default: ''
    },
    nativeType: {
      type: String,
      default: 'button'
    },
    loading: Boolean,
    disabled: Boolean,
    plain: Boolean,
    autofocus: Boolean,
    round: Boolean,
    circle: Boolean,
    // 增强版的click事件，支持自动设置loading状态
    enhanceClick: Function
  },

  watch: {
    loading: function(newValue) {
      this.localLoading = newValue;
    }
  },

  computed: {
    _elFormItemSize() {
      return (this.elFormItem || {}).elFormItemSize;
    },
    buttonSize() {
      return this.size || this._elFormItemSize || (this.$ELEMENT || {}).size;
    },
    buttonDisabled() {
      return this.disabled || (this.elForm || {}).disabled;
    }
  },

  methods: {
    isPromise(object) {
      return typeof object === 'object' && typeof object.then === 'function';
    },
    updateLoading(value) {
      this.localLoading = value;
      this.$emit('update:visible', value);
    },
    handleClick(evt) {
      if (this.enhanceClick) {
        const promise = this.enhanceClick(evt);
        if (this.isPromise(promise)) {
          this.updateLoading(true);
          promise
            .then(() => {
              this.updateLoading(false);
            })
            .catch(() => {
              this.updateLoading(false);
            });
        }
      } else {
        this.$emit('click', evt);
      }
    }
  }
};
</script>
