<!--
 * @Description: quill编辑器测试代码
 * @Author: HuXiaoDai
 * @Date: 2019-05-26 14:45:32
 * @LastEditTime: 2019-05-26 15:45:32
 -->
<template>
  <div class="test-wrap">
    <!-- 富文本编辑器 -->
    <quill-editor class="quill-editor"
      ref="myQuillEditor"
      :content="content"
      :options="editorOption"
      @change="onEditorChange($event)"
      @keydown.native="keydown($event, 5)"
      @focus="onEditorFocus($event)"
      @compositionstart.native="compositionstart($event)"
      @compositionend.native="compositionend($event, 5)"/>
    <!-- 预览 -->
    <h4>这里是内容预览</h4>
    <div class="ql-editor quill-editor-text" v-html="content"></div>
  </div>
</template>

<script>
// require styles
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'

import { quillEditor, Quill } from 'vue-quill-editor'

// 注册自定义的字体类型
const Font = Quill.import('formats/font')
const fonts = ['SimSun', 'SimHei', 'Microsoft-YaHei', 'KaiTi', 'FangSong', 'Arial']
Font.whitelist = fonts
Quill.register(Font, true)

// 注册自定义的字体大小
const Size = Quill.import('attributors/style/size')
const sizes = ['10px', '12px', '14px', '16px', '18px', '20px', '24px', '30px']
Size.whitelist = sizes
Quill.register(Size, true)

export default {
  components: {
    quillEditor
  },
  data () {
    return {
      content: '', // 富文本编辑器内容
      editorOption: { // 富文本编辑器配置
        placeholder: '请输入文本',
        modules: {
          toolbar: [
            [{ 'size': sizes }], // 字体大小
            [{ 'font': fonts }], // 字体类型
            ['bold', 'italic', 'underline', 'strike'], // 加粗，斜体，下划线，删除线
            ['blockquote'], // 引用
            [{ 'script': 'sub' }, { 'script': 'super' }], // 上下标
            [{ 'color': [] }, { 'background': [] }], // 文字颜色 背景颜色
            [{ 'align': [] }], // 对齐方式
            ['clean'] // 清除字体样式
          ]
        }
      }
    }
  },
  computed: {
    editor () {
      return this.$refs['myQuillEditor'].quill
    }
  },
  methods: {
    /**
     * [onEditorChange 监听富文本内容变化]
     * @param  {String} html [html格式的内容]
     * @param  {String} text [纯文本格式的内容]
     * @param  {Object} quill [quill对象]
     */
    onEditorChange ({html, text, quill}) {
      if (!this.isInputChines) {
        this.focusDelta = this.editor.getContents()
      }
      this.$emit('update:content', html)
    },
    /**
     * [onEditorFocus 富文本编辑框focus]
     * @param  {Object} quill [quill对象]
     */
    onEditorFocus (quill) {
      this.focusDelta = this.editor.getContents()
    },
    /**
     * [compositionstart 监听是否为中文输入]
     * @param {Object} event evt对象
     */
    compositionstart (event) {
      this.isInputChines = true
    },
    /**
     * [compositionend 监听中文输入结束]
     * @param {Object} event evt对象
     * @param {Number} decimalNum 最大输入数
     */
    compositionend (event, decimalNum) {
      this.isInputChines = false
      let delta = this.editor.getContents()
      let length = 0
      delta.ops.forEach(item => {
        if (item.insert) {
          length = length + item.insert.length
        }
      })
      length = length - 1 // 默认有个空格长度是1所以需要减1
      if (length > decimalNum) {
        this.editor.setContents(this.focusDelta)
      } else {
        this.focusDelta = delta
      }
    },
    /**
     * [keydown 非中文下 处理最大可输入范围]
     * @param {Object} event evt对象
     * @param {Number} decimalNum 最大输入数
     */
    keydown (event, decimalNum) {
      if (!this.isInputChines) {
        let length = this.editor.getLength()
        if (length > decimalNum && event.key !== 'Backspace') {
          event.preventDefault()
        }
      }
    }
  }
}
</script>

<style>
.test-wrap {
  margin: 0 auto;
  width: 1200px;
}
.ql-bubble .ql-picker.ql-size {
  width: 85px;
}
/* 自定义字体大小 */
.ql-toolbar .ql-picker.ql-size .ql-picker-label[data-value="10px"]::before,
.ql-toolbar .ql-picker.ql-size .ql-picker-item[data-value="10px"]::before {
  content: '10px';
  font-size: 14px;
}
.ql-toolbar .ql-picker.ql-size .ql-picker-label[data-value="11px"]::before,
.ql-toolbar .ql-picker.ql-size .ql-picker-item[data-value="11px"]::before {
  content: '11px';
  font-size: 14px;
}
.ql-toolbar .ql-picker.ql-size .ql-picker-label[data-value="12px"]::before,
.ql-toolbar .ql-picker.ql-size .ql-picker-item[data-value="12px"]::before {
  content: '12px';
  font-size: 14px;
}
.ql-toolbar .ql-picker.ql-size .ql-picker-label[data-value="14px"]::before,
.ql-toolbar .ql-picker.ql-size .ql-picker-item[data-value="14px"]::before {
  content: '14px';
  font-size: 14px;
}
.ql-toolbar .ql-picker.ql-size .ql-picker-label[data-value="16px"]::before,
.ql-toolbar .ql-picker.ql-size .ql-picker-item[data-value="16px"]::before {
  content: '16px';
  font-size: 14px;
}
.ql-toolbar .ql-picker.ql-size .ql-picker-label[data-value="18px"]::before,
.ql-toolbar .ql-picker.ql-size .ql-picker-item[data-value="18px"]::before {
  content: '18px';
  font-size: 14px;
}
.ql-toolbar .ql-picker.ql-size .ql-picker-label[data-value="20px"]::before,
.ql-toolbar .ql-picker.ql-size .ql-picker-item[data-value="20px"]::before {
  content: '20px';
  font-size: 14px;
}
.ql-toolbar .ql-picker.ql-size .ql-picker-label[data-value="24px"]::before,
.ql-toolbar .ql-picker.ql-size .ql-picker-item[data-value="24px"]::before {
  content: '24px';
  font-size: 14px;
}
.ql-toolbar .ql-picker.ql-size .ql-picker-label[data-value="30px"]::before,
.ql-toolbar .ql-picker.ql-size .ql-picker-item[data-value="30px"]::before {
  content: '30px';
  font-size: 14px;
}

/* 自定义字体类型 */
.ql-font-SimSun {
  font-family: 宋体, SimSun, STSong!important;
}
.ql-font-SimHei {
  font-family: 黑体, SimHei, STHeiti!important;
}
.ql-font-Microsoft-YaHei {
  font-family: 微软雅黑, "Microsoft Yahei"!important;
}
.ql-font-KaiTi {
  font-family: 楷体, 楷体_GB2312, KaiTi, STKaiti!important;
}
.ql-font-FangSong {
  font-family: 仿宋, FangSong, STFangsong!important;
}
.ql-toolbar .ql-picker.ql-font .ql-picker-label[data-value=SimSun]::before,
.ql-toolbar .ql-picker.ql-font .ql-picker-item[data-value=SimSun]::before {
  content: "宋体";
  font-family: 宋体, SimSun, STSong;
}
.ql-toolbar .ql-picker.ql-font .ql-picker-label[data-value=SimHei]::before,
.ql-toolbar .ql-picker.ql-font .ql-picker-item[data-value=SimHei]::before {
  content: "黑体";
  font-family: 黑体, SimHei, STHeiti;
}
.ql-toolbar .ql-picker.ql-font .ql-picker-label[data-value=Microsoft-YaHei]::before,
.ql-toolbar .ql-picker.ql-font .ql-picker-item[data-value=Microsoft-YaHei]::before {
  content: "微软雅黑";
  font-family: 微软雅黑, "Microsoft Yahei";
}
.ql-toolbar .ql-picker.ql-font .ql-picker-label[data-value=KaiTi]::before,
.ql-toolbar .ql-picker.ql-font .ql-picker-item[data-value=KaiTi]::before {
  content: "楷体";
  font-family: 楷体, 楷体_GB2312, KaiTi, STKaiti;
}
.ql-toolbar .ql-picker.ql-font .ql-picker-label[data-value=FangSong]::before,
.ql-toolbar .ql-picker.ql-font .ql-picker-item[data-value=FangSong]::before {
  content: "仿宋";
  font-family: 仿宋, FangSong, STFangsong;
}
.ql-toolbar .ql-picker.ql-font .ql-picker-label[data-value=Arial]::before,
.ql-toolbar .ql-picker.ql-font .ql-picker-item[data-value=Arial]::before {
  content: "Arial";
  font-family: "Arial";
}

.ql-font-SimSun {
  font-family: "SimSun";
}
.ql-font-SimHei {
  font-family: "SimHei";
}
.ql-font-Microsoft-YaHei {
  font-family: "Microsoft YaHei";
}
.ql-font-KaiTi {
  font-family: "KaiTi";
}
.ql-font-FangSong {
  font-family: "FangSong";
}
.ql-font-Arial {
  font-family: "Arial";
}
</style>
