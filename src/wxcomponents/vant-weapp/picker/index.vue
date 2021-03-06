<template>
<uni-shadow-root class="vant-weapp-picker-index"><view class="van-picker custom-class">
  <view v-if="showToolbar" class="van-picker__toolbar van-hairline--top-bottom toolbar-class">
    <view class="van-picker__cancel" data-type="cancel" @click="emit">
      {{ cancelButtonText || '取消' }}
    </view>
    <view v-if="title" class="van-picker__title van-ellipsis">{{ title }}</view>
    <view class="van-picker__confirm" data-type="confirm" @click="emit">
      {{ confirmButtonText || '确认' }}
    </view>
  </view>
  <view v-if="loading" class="van-picker__loading">
    <loading color="#1989fa"></loading>
  </view>
  <view class="van-picker__columns" :style="'height: '+(itemHeight * visibleItemCount)+'px'" @touchmove.stop.prevent="noop">
    <picker-column v-for="(item,index) in (isSimple(columns) ? [columns] : columns)" :key="item.index" class="van-picker__column" :data-index="index" custom-class="column-class" :value-key="valueKey" :initial-options="isSimple(columns) ? item : item.values" :default-index="item.defaultIndex" :item-height="itemHeight" :visible-item-count="visibleItemCount" active-class="active-class" @change="onChange"></picker-column>
    <view class="van-picker__frame van-hairline--top-bottom" :style="'height: '+(itemHeight)+'px'"></view>
  </view>
</view></uni-shadow-root>
</template>
<wxs module="isSimple" src="./index-isSimple.wxs"></wxs>
<script>
import PickerColumn from '../picker-column/index.vue'
import Loading from '../loading/index.vue'
global['__wxVueOptions'] = {components:{'picker-column': PickerColumn,'loading': Loading}}

global['__wxRoute'] = 'vant-weapp/picker/index'
import { VantComponent } from '../common/component';

function isSimple(columns) {
  return columns.length && !columns[0].values;
}

VantComponent({
  classes: ['active-class', 'toolbar-class', 'column-class'],
  props: {
    title: String,
    loading: Boolean,
    showToolbar: Boolean,
    confirmButtonText: String,
    cancelButtonText: String,
    visibleItemCount: {
      type: Number,
      value: 5
    },
    valueKey: {
      type: String,
      value: 'text'
    },
    itemHeight: {
      type: Number,
      value: 44
    },
    columns: {
      type: Array,
      value: [],
      observer: function observer(columns) {
        if (columns === void 0) {
          columns = [];
        }

        this.simple = isSimple(columns);
        var children = this.children = this.selectAllComponents('.van-picker__column');

        if (Array.isArray(children) && children.length) {
          this.setColumns();
        }
      }
    }
  },
  beforeCreate: function beforeCreate() {
    this.children = [];
  },
  methods: {
    noop: function noop() {},
    setColumns: function setColumns() {
      var _this = this;

      var data = this.data;
      var columns = this.simple ? [{
        values: data.columns
      }] : data.columns;
      columns.forEach(function (columns, index) {
        _this.setColumnValues(index, columns.values);
      });
    },
    emit: function emit(event) {
      var type = event.currentTarget.dataset.type;

      if (this.simple) {
        this.$emit(type, {
          value: this.getColumnValue(0),
          index: this.getColumnIndex(0)
        });
      } else {
        this.$emit(type, {
          value: this.getValues(),
          index: this.getIndexes()
        });
      }
    },
    onChange: function onChange(event) {
      if (this.simple) {
        this.$emit('change', {
          picker: this,
          value: this.getColumnValue(0),
          index: this.getColumnIndex(0)
        });
      } else {
        this.$emit('change', {
          picker: this,
          value: this.getValues(),
          index: event.currentTarget.dataset.index
        });
      }
    },
    // get column instance by index
    getColumn: function getColumn(index) {
      return this.children[index];
    },
    // get column value by index
    getColumnValue: function getColumnValue(index) {
      var column = this.getColumn(index);
      return column && column.getValue();
    },
    // set column value by index
    setColumnValue: function setColumnValue(index, value) {
      var column = this.getColumn(index);
      column && column.setValue(value);
    },
    // get column option index by column index
    getColumnIndex: function getColumnIndex(columnIndex) {
      return (this.getColumn(columnIndex) || {}).data.currentIndex;
    },
    // set column option index by column index
    setColumnIndex: function setColumnIndex(columnIndex, optionIndex) {
      var column = this.getColumn(columnIndex);
      column && column.setIndex(optionIndex);
    },
    // get options of column by index
    getColumnValues: function getColumnValues(index) {
      return (this.children[index] || {}).data.options;
    },
    // set options of column by index
    setColumnValues: function setColumnValues(index, options, needReset) {
      if (needReset === void 0) {
        needReset = true;
      }

      var column = this.children[index];

      if (column && JSON.stringify(column.data.options) !== JSON.stringify(options)) {
        column.set({
          options: options
        }, function () {
          if (needReset) {
            column.setIndex(0);
          }
        });
      }
    },
    // get values of all columns
    getValues: function getValues() {
      return this.children.map(function (child) {
        return child.getValue();
      });
    },
    // set values of all columns
    setValues: function setValues(values) {
      var _this2 = this;

      values.forEach(function (value, index) {
        _this2.setColumnValue(index, value);
      });
    },
    // get indexes of all columns
    getIndexes: function getIndexes() {
      return this.children.map(function (child) {
        return child.data.currentIndex;
      });
    },
    // set indexes of all columns
    setIndexes: function setIndexes(indexes) {
      var _this3 = this;

      indexes.forEach(function (optionIndex, columnIndex) {
        _this3.setColumnIndex(columnIndex, optionIndex);
      });
    }
  }
});
export default global['__wxComponents']['vant-weapp/picker/index']
</script>
<style platform="mp-weixin">
@import '../common/index.css';.van-picker{position:relative;overflow:hidden;-webkit-text-size-adjust:100%;background-color:#fff;-webkit-user-select:none;user-select:none}.van-picker__toolbar{display:-webkit-flex;display:flex;height:44px;line-height:44px;-webkit-justify-content:space-between;justify-content:space-between}.van-picker__cancel,.van-picker__confirm{padding:0 15px;font-size:14px;color:#1989fa}.van-picker__cancel:active,.van-picker__confirm:active{background-color:#e8e8e8}.van-picker__title{max-width:50%;font-size:16px;font-weight:500;text-align:center}.van-picker__columns{position:relative;display:-webkit-flex;display:flex}.van-picker__column{-webkit-flex:1 1;flex:1 1;width:0}.van-picker__loading{position:absolute;top:0;right:0;bottom:0;left:0;z-index:4;display:-webkit-flex;display:flex;background-color:rgba(255,255,255,.9);-webkit-align-items:center;align-items:center;-webkit-justify-content:center;justify-content:center}.van-picker__frame,.van-picker__loading .van-loading{position:absolute;top:50%;left:0;z-index:1;width:100%;pointer-events:none;-webkit-transform:translateY(-50%);transform:translateY(-50%)}
</style>