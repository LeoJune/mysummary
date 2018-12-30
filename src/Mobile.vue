<template>
  <div id="app">
    <StyleEditor ref="styleEditor" :code="currentStyle"></StyleEditor>
    <ResumeEditor ref="resumeEditor" :markdown="currentMarkdown" :enableHtml="enableHtml"></ResumeEditor>
  </div>
</template>

<script>
  import StyleEditor from './components/StyleEditor'
  import ResumeEditor from './components/ResumeEditor'
  import './assets/reset.css'

  export default {
    name: 'app',
    components: {
      StyleEditor,
      ResumeEditor
    },
    data() {
      return {
        interval: 40,
        currentStyle: '',
        enableHtml: false,
        fullStyle: [
          `/*
大家好，我是技术部 前端 程卓
* 今天是报告年终总结的日子
* 设计不会设计，视频也不会视频
* 只能靠写写代码来完成这次总结了
* 说做就做，我们就来一起写一份总结
* 顺便也会展示一些我们前端在日常工作
* 中的是什么样的
*/

/*就像平时h5中的那样，首先先给要动元素都加上动的属性， */
* {
  transition: all .5s;
}
/* 白色背景太单调了，对眼睛也不好，来换个护眼的颜色 */
html {
  background: rgb(63, 82, 99);
  color: rgb(222,222,222);
}
/* 文字离边框太近了，我们来离远一点 */
.styleEditor {
  padding: .5em;
  border: 1px solid;
  overflow: auto;
  width: 90vw;
  margin: 2.5vh 5vw;
  height: 90vh;
}
/* 太高了 */
.styleEditor {
  height: 45vh;
}
/* 文字颜色都一样可不利于阅读，区分一下 */
.token.selector{ color: rgb(133,153,0); }
.token.property{ color: rgb(187,137,0); }
.token.punctuation{ color: yellow; }
.token.function{ color: rgb(42,161,152); }

/* 再来加一点 3D 效果 */
html{
  perspective: 1000px;
}
.styleEditor {
  background-color: rgb(0, 0, 0);
  border: 1px solid rgb(204, 204, 204);
  box-shadow: rgba(0, 0, 0, 0.3) -4px 4px 2px 0px;
  -webkit-transform: rotateY(10deg) translateZ(-100px) ;
          transform: rotateY(10deg) translateZ(-100px) ;
}
/* 代码部分的样式就准备完毕，不能忘了今天的主体 
 * 接下来我们来准备一个写总结的区域 
 */
.summaryEditor{
  position: fixed;
  top: 48%; left: 0;
  padding: .5em;  margin: 2.5vh;
  width: 95vw; height: 45vh;
  border: 1px solid;
  background: white; color: #222;
  overflow: auto;
  -webkit-transform: rotateY(-10deg) translateZ(-100px) ;
          transform: rotateY(-10deg) translateZ(-100px) ;
}

`,
          `
/* 让我们来开始今天的主题吧，写总结
 * 等等~ 
 * 在写样式之前，我们还是先把总结的样式写好
 * 要不然两个区域来回切换是在麻烦
 * ~~~~~~~~~~~~
 * 虽然真实的场景就是来两个区域来回切换，囧
 */
`
          ,
          `
/* 来给总结部分加样式 */
.summaryEditor{
  padding: 2em;
}
.summaryEditor h1{
  display: inline-block;
  border-bottom: 1px solid;
  margin: 1em 0 .5em;
}
.summaryEditor h2{
  display: inline-block;
  margin: 1em 0 .5em;
}
.summaryEditor ul,.summaryEditor ol{
  list-style: none;
}
.summaryEditor ol{
  background-color: rgb(217, 217, 217);
  border-radius: 6px;
}
.summaryEditor ul li,.summaryEditor ol li{
  margin-bottom: .5em;
}
.summaryEditor ul> li::before{
  content: '•';
  margin-right: .5em;
}
.summaryEditor ol {
  counter-reset: section;
}
.summaryEditor ol li::before {
  counter-increment: section;
  content: counters(section, ".") " ";
  margin-right: .5em;
}
.summaryEditor blockquote {
  margin: 1em;
  padding: .5em;
  background: #ddd;
}
/*
 * 好了，我们可以开始了
 * 这回是真的开始
 */
`],
        currentMarkdown: '',
        fullMarkdown: `# 1.2018年工作回顾
## 1. 2018年重点完成工作

 * 累计完成50个h5的制作，其中展示类10个，游戏类10个，视频类10个（视频类最让我头疼）
 * 下午茶系统（看到自己写的东西不管什么原因在被很多人使用，这对于技术人员来说是一件令人高兴的事情）
 * 诶，没什么写了，所以说用ppt的话我怕到不了3页 (⊙o⊙) …
 
## 2. 2018年 工作中存在的问题，经验总结

 * 有些h5在上线之后才发现bug，虽说要写没有bug的程序很难，但是只要在写的过程中逻辑在线，上线之前多测试，就能有效减少这类情况的出现
 * 有些合理需求由于自身技术原因，实现不了或实现的不够好，还是要保持学习的热情，提高自身姿势水平
 * 写h5过程中也会遇到莫名其妙的bug，还好部门同事都是见多识广，经验丰富的老手，也感谢在平时工作中他们对我的帮助

  # 2. 2019年展望

  2019年展望清单

1. 不在项目上线后才发现bug
2. 提高自身姿势水平
3. 成为解决方案的提供者，而不是问题的提供者


好了，以上就是我的2018年年终总结，希望在2019年总结时能在上面的清单上都打上勾。
感谢大家观看！

`
      }
    },
    created() {
      this.makeResume()
    },

    methods: {
      makeResume: async function () {
        await this.progressivelyShowStyle(0)
        await this.progressivelyShowStyle(1)
        await this.showHtml()
        await this.progressivelyShowStyle(2)
        await this.progressivelyShowResume()
      },
      showHtml: function () {
        return new Promise((resolve, reject) => {
          this.enableHtml = true
          this.$nextTick(() => {
            this.$refs.resumeEditor.goTop()
          })
          resolve()
        })
      },
      progressivelyShowStyle(n) {
        return new Promise((resolve, reject) => {
          let interval = this.interval
          let showStyle = (async function () {
            let style = this.fullStyle[n]
            if (!style) { return }
            // 计算前 n 个 style 的字符总数
            let length = this.fullStyle.filter((_, index) => index <= n).map((item) => item.length).reduce((p, c) => p + c, 0)
            let prefixLength = length - style.length
            if (this.currentStyle.length < length) {
              let l = this.currentStyle.length - prefixLength
              let char = style.substring(l, l + 1) || ' '
              this.currentStyle += char
              if (style.substring(l - 1, l) === '\n' && this.$refs.styleEditor) {
                this.$nextTick(() => {
                  this.$refs.styleEditor.goBottom()
                })
              }
              setTimeout(showStyle, interval)
            } else {
              resolve()
            }
          }).bind(this)
          showStyle()
        })
      },
      progressivelyShowResume() {
        return new Promise((resolve, reject) => {
          let length = this.fullMarkdown.length
          let interval = this.interval
          let showResume = () => {
            if (this.currentMarkdown.length < length) {
              this.currentMarkdown = this.fullMarkdown.substring(0, this.currentMarkdown.length + 1)
              let lastChar = this.currentMarkdown[this.currentMarkdown.length - 1]
              let prevChar = this.currentMarkdown[this.currentMarkdown.length - 2]
              if (prevChar === '\n' && this.$refs.resumeEditor) {
                this.$nextTick(() => this.$refs.resumeEditor.goBottom())
              }
              setTimeout(showResume, interval)
            } else {
              resolve()
            }
          }
          showResume()
        })
      }
    }
  }

</script>

<style scoped>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    min-height: 100vh; position: relative;
  }

  html {
    min-height: 100vh;
  }
  *{
    box-sizing: border-box;
  }

</style>
