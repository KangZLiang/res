<template>

  <el-popover
    placement="right"
    trigger="click"
  >
    <el-form
      ref="tabsStyleForm"
      :model="styleInfo"
      size="small"
      class="de-form-item"
    >
      <el-form-item
        label="头部字体颜色"
        prop="headFrontColor"
      >
        <div
          class="picker-color-div"
          @click="triggerTheme('headFontColor')"
        >
          <el-input
            v-model="styleInfo.headFontColor"
            readonly
            class="theme-input"
          >
            <el-color-picker
              ref="headFontColorPicker"
              slot="prefix"
              v-model="styleInfo.headFontColor"
              class="theme-picker"
              @change="styleChange"
            />
          </el-input>
        </div>
      </el-form-item>
      <el-form-item
        label="头部字体选中颜色"
        prop="headFontActiveColor"
      >
        <div
          class="picker-color-div"
          @click="triggerTheme('headFontActiveColor')"
        >
          <el-input
            v-model="styleInfo.headFontActiveColor"
            readonly
            class="theme-input"
          >
            <el-color-picker
              ref="headFontActiveColorPicker"
              slot="prefix"
              v-model="styleInfo.headFontActiveColor"
              class="theme-picker"
              @change="styleChange"
            />
          </el-input>
        </div>
      </el-form-item>
      <el-form-item
        label="头部边框颜色"
        prop="headBorderColor"
      >
        <div
          class="picker-color-div"
          @click="triggerTheme('headBorderColor')"
        >
          <el-input
            v-model="styleInfo.headBorderColor"
            readonly
            class="theme-input"
          >
            <el-color-picker
              ref="headBorderColorPicker"
              slot="prefix"
              v-model="styleInfo.headBorderColor"
              class="theme-picker"
              @change="styleChange"
            />
          </el-input>
        </div>
      </el-form-item>
      <el-form-item
        label="头部边框选中颜色"
        prop="headBorderActiveColor"
      >
        <div
          class="picker-color-div"
          @click="triggerTheme('headBorderActiveColor')"
        >
          <el-input
            v-model="styleInfo.headBorderActiveColor"
            readonly
            class="theme-input"
          >
            <el-color-picker
              ref="headBorderActiveColorPicker"
              slot="prefix"
              v-model="styleInfo.headBorderActiveColor"
              class="theme-picker"
              @change="styleChange"
            />
          </el-input>
        </div>
      </el-form-item>
      <el-form-item :label="$t('detabs.head_position')">
        <el-radio-group
          v-model="styleInfo.headPosition"
          size="mini"
        >
          <el-radio-button label="left">{{ $t('chart.text_pos_left') }}</el-radio-button>
          <el-radio-button label="center">{{ $t('chart.text_pos_center') }}</el-radio-button>
          <el-radio-button label="right">{{ $t('chart.text_pos_right') }}</el-radio-button>
        </el-radio-group>
      </el-form-item>
      <el-form-item :label="$t('panel.carousel')">
        <el-row>
          <el-col :span="6">
            <el-checkbox
              v-model="styleInfo.carouselEnable"
              size="mini"
              @change="styleChange"
            >{{ $t('commons.enable') }}
            </el-checkbox>
          </el-col>
          <el-col
            :span="8"
            style="text-align: right;padding-right: 10px"
          >
            {{ $t('panel.switch_time') }}
          </el-col>
          <el-col :span="10">
            <el-input
              v-model="styleInfo.switchTime"
              :disabled="!styleInfo.carouselEnable"
              type="number"
              size="mini"
              :min="2"
              :max="3600"
              class="hide-icon-number number-padding"
              @change="switchTimeChange"
            >
              <template slot="append">S</template>
            </el-input>
          </el-col>

        </el-row>
      </el-form-item>
      <el-from-item :label="$t('panel.switch_chart')">
        <el-row>
          <el-col
            :span="8"
          >
            {{ $t('panel.switch_chart') }}
          </el-col>
          <el-col :span="16">
            <ul>
              <li v-for="(item,index) in mainCanvasComponentData" :key="index" @click="ChartClick(item)" :class="{'border': name == item.title}">
                {{item.title}}
              </li>
            </ul>
          </el-col>
        </el-row>
      </el-from-item>
    </el-form>
    <i
      slot="reference"
      class="iconfont icon-tabs"
    />
  </el-popover>

</template>

<script>
import { mapState } from 'vuex'
import { getNowCanvasComponentData } from '@/components/canvas/utils/utils'
import bus from '@/utils/bus'

export default {
  name: 'TabStyle',
  props: {
    styleInfo: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      element: [],
      name: ''
    }
  },
  computed: {

    ...mapState([
      'componentData',
      'curComponent'
    ]),
    mainCanvasComponentData() {
      const arr = getNowCanvasComponentData('canvas-main')
      let res = []
      arr.some(item => {
        if (item.label === '切换') {
          res = item.options.tabList
          this.name = item.options.tabList[0].title
        }
      })
      return res
    }
  },
  methods: {
    ChartClick(tab) {
      this.name = tab.title
      bus.$emit('chart-click', tab)
    },
    triggerTheme(key) {
      const pickKey = key + 'Picker'
      const current = this.$refs[pickKey]
      current && (current.showPicker = true)
    },
    switchTimeChange() {
      if (!this.styleInfo.switchTime || this.styleInfo.switchTime < 2) {
        this.styleInfo.switchTime = 2
      } else if (this.styleInfo.switchTime && this.styleInfo.switchTime > 3600) {
        this.styleInfo.switchTime = 3600
      }
      this.styleChange()
    },
    styleChange() {
      this.$store.commit('canvasChange')
    }
  }

}
</script>

<style lang="scss" scoped>
::v-deep.number-padding {
  .el-input__inner {
    padding-right: 0;
  }
  .el-input-group__append {
    padding: 0 10px;
  }
}
.border {
  border: 1px solid #ccc;
}
</style>
