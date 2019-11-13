---
title: 百度编辑器使用
date: 2019-11-13 14:34:14
tags:
---
# 在项目中使用百度编辑器的方法

<!--more-->
### 1.先在 npm install 引入相关百度编辑器的包
```
npm install vue-ueditor-wrap -S
```
### 2.在vue相关目录下引入百度编辑器资源的包，放到static静态目录下面(我是使用的vue2.0cli)

1.具体参考百度编辑器官方文档 [百度编辑器](http://ueditor.baidu.com/website/)

### 3.下面是我自己在vue项目中封装的一个组件
html部分
```
<template>
    <div class="ueditorCom">
        <vue-ueditor-wrap ref="ueditor" v-model="quillContent" @beforeInit="setJustify"  :destroy="false" :config="config" @ready="ready"></vue-ueditor-wrap>
    </div>
</template>
```

js部分
```$xslt
<script>
	// 1、引入VueUeditorWrap组件
	import VueUeditorWrap from 'vue-ueditor-wrap' // ES6 Module
	export default {
		name: 'ueditorCom',
		// 2、注册组件
		components: {
			VueUeditorWrap
		},
		props: ['quillContent'],
		data () {
			return {
				// 3、v-model绑定数据
				msg: '',
				// 4、根据项目需求自行配置,具体配置参见ueditor.config.js源码或 http://fex.baidu.com/ueditor/#start-start
				config: {
					// 编辑器不自动被内容撑高
					autoHeightEnabled: false,
					// 初始容器高度
					initialFrameHeight: 500,
					// 初始容器宽度
					initialFrameWidth: 863,
					// 上传文件接口（这是我项目自己配置的项目地址）
					serverUrl: process.env.UEAPI,
					allowDivTransToP: false
					// UEditor 资源文件的存放路径，如果你使用的是 vue-cli 生成的项目，通常不需要设置该选项，vue-ueditor-wrap 会自动处理常见的情况，如果需要特殊配置，参考下方的常见问题2
					// UEDITOR_HOME_URL: '/static/UEditor/'
					// 配合最新编译的资源文件，你可以实现添加自定义Request Headers,详情https://github.com/HaoChuan9421/ueditor/commits/dev-1.4.3.3
					// headers: {
					//   access_token: '37e7c9e3fda54cca94b8c88a4b5ddbf3'
					// }
				}
			}
		},
		watch: {
			'quillContent': function (val) {
				// console.log('111',val)
				this.$emit('changeQuill', this.quillContent)
			}
		},
        created(){
        },
		mounted () {
			// this.editor = window.UE.getEditor('editor', this.config) // 初始化UE
			// this.editor.addListener('ready', function () {})
		},
		methods: {
			// 5、 你可以在ready方法中拿到editorInstance实例,所有API和官方的实例是一样了。http://fex.baidu.com/ueditor/#api-common
			ready (editorInstance) {
				console.log(`实例${editorInstance.key}已经初始化:`, editorInstance)
			},
			//设置两端缩进
			setJustify (editorId) {
				let objItems = [{id: 0, name: '默认'}, {id: 1, name: '左右间距'}, {id: 2, name: '左间距'}, {id: 3, name: '右间距'}]
				window.UE.registerUI('justifyjustifycenter', function (editor, uiName) {
					console.log(editor,'编辑器示例');
					editor.registerCommand(uiName, {
						execCommand: function (cmdName, item) {
							if (item.value === 1){
								this.execCommand('paragraph', 'div', {style: 'padding-left: ' + 20  + 'px; padding-right: ' + 20 + 'px'})
                            } else if(item.value === 2){
								this.execCommand('paragraph', 'div', {style: 'padding-left: ' + 20  + 'px; padding-right: ' + 0 + 'px'})
                            } else if(item.value === 3){
								this.execCommand('paragraph', 'div', {style: 'padding-right: ' + 20  + 'px; padding-left: ' + 20 + 'px'})
                            } else {
								this.execCommand('paragraph', 'div', {style: 'padding-right: ' + 0  + 'px; padding-left: ' + 0 + 'px'})
                            }
							return true
						}
					})
					let items = []
					for (var i = 0; i < objItems.length; i++) {
						var item = objItems[i]
						items.push({
							label: item['name'],
							value: item['id']
						})
					}
					let combox = new window.UE.ui.Combox({
						editor: editor,
						items: items,
						onselect: function (t, index) {
							editor.execCommand(uiName, this.items[index])
						},
						title: '间距',
						initValue: '间距'
					})
					return combox
				}, [42])  // [42]控制下拉框在工具栏的位置
				// 设置字间距
				let objItems2 = [{id: 0, name: '默认'}, {id: 1, name: '1px'}, {id: 2, name: '2px'}, {id: 3, name: '3px'}]
				window.UE.registerUI('replacekey', function (editor, uiName) {
					editor.registerCommand(uiName, {
						execCommand: function (cmdName, item) {
							this.execCommand('paragraph', 'p', {style: 'letter-spacing:' + item.value + 'px'})
							return true
						}
					})
					var items = []
					for (var i = 0; i < objItems2.length; i++) {
						var item = objItems2[i]
						items.push({
							label: item['name'],
							value: item['id']
						})
					}
					var combox = new window.UE.ui.Combox({
						editor: editor,
						items: items,
						onselect: function (t, index) {
							editor.execCommand(uiName, this.items[index])
						},
						title: '字间距',
						initValue: '字间距'
					})
					return combox
				}, [33]) // [33]控制下拉框在工具栏的位置
			},
			// 6. 查看绑定的数据
			showData () {
				alert(this.msg)
				console.log(this.msg)
			},
		}
	}
</script>
```

注意前端这样 引入就可以了 后台需要根据语言的不同配置具体请到官方文挡查看
