<template>
  <div class="ctn">
    <el-row>
      <!-- 左 -->
      <el-col :span="8">
        <el-form label-width="100px">

          <!-- 拖拽容器 -->
          <draggable :clone="cloneData" :list="form_list" :options="optionsLeft">

            <!-- 过度容器 -->
            <!-- <transition-group class="form-list-group" type="transition" :name="'flip-list'" tag="div"> -->

              <!-- 被拖拽内容 -->
              <div class="form-left" v-for="(v,k) in form_list" :key="k" style="padding: 15px;">
                <renders :ele='v.ele' :obj='v.obj'></renders>
              </div>
            <!-- </transition-group> -->
          </draggable>
        </el-form>
      </el-col>

      <!-- 中 -->
      <el-col :span="10">
        <el-form label-width="100px" :rules="rules">
          <el-alert title="未绑定数据字典控件无效" type="warning" :closable='false' style="margin-bottom: 15px;"></el-alert>
          <el-divider>将组件拖拽至下述↓方框内</el-divider>
          <div class="border">
            <h4 style="padding: 0 15px;">{{formInfo.formName}}</h4>
            <el-divider></el-divider>

            <draggable :list="form_Right" :options="optionsRight">
              <!-- 过度容器 -->
              <!-- <transition-group class="form-list-group" type="transition" :name="'flip-list'" tag="div"> -->
                <!-- 被拖入内容 -->
                <div class="form-right" v-for="(v,k) in form_Right" :key="k" style="padding: 15px;"><!--  @click="setting(v)" -->
                  <div class="icon">
                    <i class="el-icon-setting" @click="setting(v)"></i>
                    <i class="el-icon-close" @click="close(v)"></i>
                  </div>
                  <renders :ele='v.ele' :obj='v.obj'></renders>
                </div>
              <!-- </transition-group> -->
            </draggable>
          </div>

        </el-form>

        <el-divider>保存上述↑内容</el-divider>

        <div class="btn">
          <el-button-group>
            <el-button type="primary" @click="reset">重 置</el-button>
            <el-button type="primary" @click="submit">保 存</el-button>
          </el-button-group>
        </div>
      </el-col>

      <!-- 右 -->
      <el-col :span="6">
        <!-- 表单信息 -->
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <span>表单信息</span>
          </div>

          <div>
            <el-form ref="formInfo" :model="formInfo" label-width="80px" :rules="rules">
              <el-form-item label="表单名称">
                <el-input v-model="formInfo.formName" clearable></el-input>
              </el-form-item>

              <el-form-item label="表单标识">
                <el-input v-model="formInfo.Identification" clearable></el-input>
              </el-form-item>

              <el-form-item label="表单描述">
                <el-input v-model="formInfo.formDescribe" clearable type="textarea"></el-input>
              </el-form-item>
            </el-form>
          </div>
        </el-card>

        <!-- 控件信息 -->
        <!-- <el-card class="box-card" style="margin-top: 25px;">
          <div slot="header" class="clearfix">
            <span>控件信息</span>
          </div>

          <div>
            <el-form ref="controlInfo" :model="controlInfo" label-width="70px">
              <el-form-item label="控件名称">
                <el-input v-model="controlInfo.controlName" clearable></el-input>
              </el-form-item>

              <el-form-item label="是否必填">
                <el-radio-group v-model="controlInfo.mustFill">
                  <el-radio :label="1">是</el-radio>
                  <el-radio :label="0">否</el-radio>
                </el-radio-group>
              </el-form-item>

              <el-form-item label="配置选项">
              </el-form-item>
              <div v-for="(v, k) in controlInfo.option" :key="k">
                <el-input :v-model="v.value" clearable></el-input>
              </div>

            </el-form>
          </div>
        </el-card> -->
      </el-col>
    </el-row>

    <!-- 属性配置模态框 -->
    <el-dialog title="配置文本域属性" :visible.sync="setDialogVisible" width="650px">
      <el-form ref="controlInfo" :model="controlInfo" label-width="100px">
        <el-form-item label="控件名称">
          <el-input v-model="controlInfo.controlName" clearable></el-input>
        </el-form-item>

        <el-form-item label="是否必填" v-if="settingForm.ele !== 'el-button'">
          <el-radio-group v-model="controlInfo.mustFill">
            <el-radio :label="1">是</el-radio>
            <el-radio :label="0">否</el-radio>
          </el-radio-group>
        </el-form-item>

        <el-form-item label="关联字典" v-if="settingForm.ele === 'el-radio' || settingForm.ele === 'el-checkbox' || settingForm.ele === 'el-select'"><!-- 关联字典(单选 多选 下拉选) -->
          <el-select v-model="controlInfo.dictionaries" clearable>
            <el-option :label="'性别'" :value="1"></el-option>
            <el-option :label="'单位'" :value="3"></el-option>
            <el-option :label="'部门'" :value="2"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="占位符内容" v-if="settingForm.ele === 'el-input' || settingForm.ele === 'textarea' || settingForm.ele === 'el-select'"><!-- 输入框 文本框 下拉选 -->
          <el-input v-model="controlInfo.placeholder" clearable></el-input>
        </el-form-item>

        <el-form-item label="最大输出长度" v-if="settingForm.ele === 'el-input' || settingForm.ele === 'textarea'"><!-- 输入框 文本框 -->
          <el-input-number v-model="controlInfo.maxlength"></el-input-number>
        </el-form-item>

        <el-form-item label="最小值" v-if="settingForm.ele === 'el-input-number'"><!-- 计数器 -->
          <el-input-number v-model="controlInfo.min"></el-input-number>
        </el-form-item>

        <el-form-item label="最大值" v-if="settingForm.ele === 'el-input-number'"><!-- 计数器 -->
          <el-input-number v-model="controlInfo.max"></el-input-number>
        </el-form-item>
      </el-form>

      <span slot="footer" class="dialog-footer">
        <el-button @click="setDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="set">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import draggable from 'vuedraggable'
import renders from './renders/renders'
export default {
  components: {
    draggable,
    renders
  },
  data () {
    return {
      form_list: [ // 左侧被拖拽数据
        {
          ele: 'el-input',
          obj: {
            label: '控件名称',
            prop: {
              mustFill: false
            },
            placeholder: '请输入',
            clearable: true,
            maxlength: 20
          }
        },
        {
          ele: 'el-select',
          obj: {
            label: '控件名称',
            prop: {
              mustFill: false
            },
            placeholder: '请选择',
            clearable: true
          }
        },
        {
          ele: 'el-radio',
          obj: {
            label: '控件名称',
            prop: {
              mustFill: false
            }
          }
        },
        {
          ele: 'el-checkbox',
          obj: {
            label: '控件名称',
            prop: {
              mustFill: false
            }
          }
        },
        {
          ele: 'el-input-number',
          obj: {
            label: '控件名称',
            prop: {
              mustFill: false
            },
            min: 1,
            max: 1000
          }
        },
        {
          ele: 'textarea',
          obj: {
            label: '控件名称',
            prop: {
              mustFill: false
            },
            placeholder: '请输入',
            clearable: true,
            maxlength: 500
          }
        },
        {
          ele: 'el-button',
          obj: {
            label: '控件名称',
            prop: {
              mustFill: false
            }
          }
        }
      ],
      optionsLeft: { // 左侧拖拽属性配置
        animation: 0,
        ghostClass: 'ghost',
        // 分组
        group: {
          name: 'shared',
          pull: 'clone',
          revertClone: false
        },
        // 禁止拖动排序
        sort: false
      },
      form_Right: [], // 右侧被拖入数据
      optionsRight: { // 右侧拖拽属性配置
        animation: 0,
        ghostClass: 'ghost',
        group: {
          // 只允许放置shared的控件,禁止pull
          put: ['shared']
        }
      },
      rules: { // 表单检测规则
        mustFill: [
          { required: true, message: '此为必填项', trigger: 'change' }
        ]
      },

      index: 0, // 用于自增统计排序
      setDialogVisible: false,

      // 右侧信息
      formInfo: { // 表单信息
        formName: '表单名称',
        Identification: '', // 标识
        formDescribe: '' // 描述
      },

      indexActivate: -1, // 激活的下标
      controlInfo: { // 控件信息
        controlName: '控件名称', // 表单名称
        mustFill: 1, // 是否必填
        placeholder: '请输入', // 占位符文字

        dictionaries: '', // 关联字典(单选 多选 下拉选)
        maxlength: 20, // 最大输出长度(输入框 文本框)
        min: 1, // 最小值(计数器)
        max: 1000 // 最大值(计数器)
      },
      settingForm: {} // 设置
    }
  },
  methods: {
    // https://github.com/SortableJS/Vue.Draggable#clone
    // 克隆,深拷贝对象
    cloneData (original) {
      // 添加一个modal标题
      // original.obj.modalTitle = original.obj.label || ''
      // 深拷贝对象，防止默认空对象被更改
      let obj = JSON.parse(JSON.stringify(original))
      obj.index = this.index++
      return obj
    },

    // 设置当前表单
    setting (v) {
      this.setDialogVisible = true
      this.indexActivate = v.index
      this.settingForm = v
    },
    // 提交设置
    set () {
      for (let i of this.form_Right) {
        if (i.index === this.indexActivate) {
          i.obj.label = this.controlInfo.controlName // 控件名称

          if (i.ele !== 'el-button') {
            if (this.controlInfo.mustFill === 1) { // 是否必填
              i.obj.prop.mustFill = true
            } else if (this.controlInfo.mustFill === 0) {
              i.obj.prop.mustFill = false
            }
          }

          if (i.ele === 'el-input' || i.ele === 'textarea' || i.ele === 'el-select') { // 占位符
            i.obj.placeholder = this.controlInfo.placeholder
          }

          if (i.ele === 'el-radio' || i.ele === 'el-checkbox' || i.ele === 'el-select') { // 占位符文字
            i.obj.dictionaries = this.controlInfo.dictionaries
          }

          if (i.ele === 'el-input' || i.ele === 'textarea') { // 最大输出长度
            i.obj.maxlength = this.controlInfo.maxlength
          }

          if (i.ele === 'el-input-number') { // 最小值 最大值
            i.obj.min = this.controlInfo.min
            i.obj.max = this.controlInfo.max
          }

          break
        }
      }
      this.setDialogVisible = false
    },

    // 删除当前表单
    close (v) {
      let index2 = -1
      for (let i = 0; i < this.form_Right.length; i++) {
        if (this.form_Right[i].index === v.index) {
          index2 = i
          break
        }
      }
      this.form_Right.splice(index2, 1)
    },
    // 重置
    reset () {
      this.form_Right = []
    },
    // 提交
    submit () {
      if (this.formInfo.formName === '' || this.formInfo.Identification === '') {
        this.$message({message: '表单名称和表单标识不可为空', type: 'error'})
        return
      }
      if (this.form_Right.length === 0) {
        this.$message({message: '至少要有一个表单才可提交', type: 'error'})
        return
      }

      for (let i of this.form_Right) {
        if (i.ele === 'el-radio' || i.ele === 'el-checkbox' || i.ele === 'el-select') {
          if (!i.obj.dictionaries) {
            this.$message({message: '单选框、多选框和下拉选择器必须绑定字典才能生效', type: 'error'})
            return
          }
        }
      }

      let req = {
        data: this.form_Right,
        info: this.formInfo
      }

      console.log('提交', req)
      this.$message({message: '提交成功', type: 'success'})
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.el-col{
  padding: 40px;
  border: 1px solid #000;
}
.form-right{
  position: relative;
}
.form-right > .icon{
  display: none;
  padding: 5px;
  position: absolute;
  top: -15px;
  right: 0;
  font-size: 14px;
  cursor: pointer;
  z-index: 2;
}
.form-right:hover > .icon{
  display: block;
}

/* 方框 */
.border{
  border: 1px dashed rgba(102, 117, 255, 0.5);
  padding: 40px 0;
}
.border:hover{
  border: 1px dashed rgba(102, 117, 255, 1);
}

/* 底部按钮 */
.btn{
  text-align: center;
}

</style>
