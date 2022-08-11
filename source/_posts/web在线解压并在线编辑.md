---
title: 'web在线解压并在线编辑'
date: 2022-08-01 14:49:25
tags: 
	- Vue
	- monaco-editor
---

### web代码编辑
效果如下：
![在线编辑](https://img-blog.csdnimg.cn/f7cb00c0913d44a7b887abd0f916ab0b.png)

#### 1、解压
在线解压需要用到 [jszip](https://stuk.github.io/jszip/documentation/api_jszip.html)，支持将`String / Array of bytes / ArrayBuffer / Uint8Array / Buffer / Blob / Promise`文件格式的压缩包解压。
由于接口返回的是文件流，所以使用`jszip-utils`包将文件流转为`ArrayBuffer`格式。
```shell
npm install jszip -S
npm install jszip-utils -S
```
```js
import JSZipUtils from "jszip-utils";
import JSZip from "jszip";

export default {
	data(){
		return {
			zip: null,
		}
	},
  	methods:{
		loadZip() {
	      JSZipUtils.getBinaryContent("压缩文件地址", function (err, data) {
			   if(err) {
			      throw err; // or handle the error
			   }
			   this.zip = new JSZip();
			   //解压
			   this.zip.loadAsync(data).then((res) => {		
	              console.log(res);
	            });
			});
	    },
	}
}
```
打印出来是这样的：

![jszip](https://img-blog.csdnimg.cn/fdac0d1ad5b943e2808036c6e50dcd02.png)
#### 2、生成目录
目录用的是`element-ui`的树形控件，加载方式懒加载，这样就不用递归生成所有目录。
```html
<el-tree
  ref="tree"
  lazy
  highlight-current
  :data="fileMenu"
  :props="defaultProps"
  :load="loadNode"
  @node-click="handleNodeClick"
>
  <!-- 文件夹图标 -->
  <span class="custom-tree-node" slot-scope="{ node, data }">
    <em v-if="!data.leaf" class="el-icon-folder-opened"></em>
    <span>{{ node.label }}</span>
  </span>
</el-tree>
```
```js
data(){
	return {
		fileMenu: [],
		defaultProps: {
	        children: "children",
	        label: "label",
	        isLeaf: "leaf",
	    },
	}
},
methods:{
	getMenuList(zip) {
		const menu = [];
		// 循环当前文件夹目录
		zip.forEach((relativePath, file) => {
		   const dir = relativePath.split("/");
		   // 文件
		   if (dir.length === 1) {
		     menu.push({
		       label: dir[0],
		       leaf: true,
		       path: file.name,
		     });
		   }
		   // 文件夹
		   if (dir.length === 2 && file.dir) {
		     menu.push({
		       label: dir[0],
		       leaf: false,
		       path: file.name,
		     });
		   }
	 });
	 return menu;
	},
	// 加载子目录
	loadNode(node, resolve) {
	  if (node.level === 0) return;
	  // 把点击的文件夹路径传入
	  const child = this.getMenuList(this.zip.folder(node.data.path));
	  resolve(child);
	},
}
```

#### 3、代码编辑
在线代码编辑需要用到 [monaco-editor](https://microsoft.github.io/monaco-editor/api/index.html)，建议下载0.30.0版本。如果需要代码高亮、快捷键、语法提示等功能可以安装`monaco-editor-webpack-plugin`插件帮忙导入。
```shell
npm install monaco-editor@0.30.0 -S
npm install monaco-editor-webpack-plugin@6.0.0 -S
```
```html
<template>
	<div id="monaco"></div>
</template>

<script>
import * as monaco from "monaco-editor";
export default {
	data(){
		return {
			monacoInstance: null,
		}
	},
	mounted(){
		this.monacoInstance = monaco.editor.create(
		  document.getElementById("monaco"),
		  {
		    value: `//请选择文件进行编辑`,
		    language: "js",
		  }
		);
		// 编辑监听
	    this.monacoInstance.onDidChangeModelContent(() => {
	      this.saveValue();
	    });
	},
	methods:{
		// 在树节点加上点击事件
		handleNodeClick(data) {
		  if (data.leaf) {
		  	this.currentPath = data.path;
		  	// 将点击的文件内容显示在编译框内
		     this.zip
		       .file(data.path)
		       .async("string")
		       .then((res) => {
		         this.monacoInstance.setValue(res);
		       });
		   }
		 },
		 // 保存编辑
		 saveValue() {
	      if (this.currentPath) {
	        const newValue = this.monacoInstance.getValue();
	        this.zip.file(this.currentPath, newValue);
	      }
	    },
	    // 打包
	    pack(){
			this.zip.generateAsync({type:"blob"})
				.then(function (content) {
				    // 保存或上传操作
				    // saveAs(content, "hello.zip");
				});
		}
	}
}
</script>
```