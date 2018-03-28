# zip-cropper-vue-component
一个支持压缩图片与裁剪图片的vue组件


Questions
---------
1. 为什么这么简便，连`package.json`都没有？

	 答：原因很简单，我写来的目的本来是给自己用，或者给公司组内分享使用，所以就不写这么多例子什么的了，我默认大家是都懂的。

 2. 这个压缩+裁剪的组件明显用 jsx 写代码更简洁、易读，为什么不用 jsx 写？

	 答：因为目前公司主要做移动端，而且 vue-cli 用的 webpack 模板是 simple 版，而不是完整版, 如果用 jsx 写，意味着我要装4个支持 `vue-jsx` babel 插件，并且还要改 babel 配置，为了大家方便，所以我直接用模板方式来书写组件，而放弃了 render 方式。

 3. 这个组件需要依赖什么包，是否适合移动端？

	 答： 目前需要依赖2个包，压缩包没什么配置，非常简洁，但是裁剪插件的配置就非常多了，下面列出依赖包原地址，后面我再写出我的组件需要的传入的 props ，当然如果你需要完全可以修改组件去加入自己需要的参数，其次我目前的公司主要就是做移动端，所以这个组件首先是为移动端考虑的，pc端的适配未考虑，但你可以自己灵活适配。
	 下面是依赖：
	 1. [压缩插件 lrz](https://github.com/think2011/localResizeIMG)
	 2.  [裁剪插件 vue-cropperjs](https://github.com/Agontuk/vue-cropperjs)
	 3. [clear version: cropperjs 里面有裁剪插件的所有配置参数](https://github.com/fengyuanchen/cropperjs#main)


Options
----
 - **组件内部是使用 slot 插槽的，所以使用组件标签后，内部可任意自定义内容或组件标签**

	Attribute
	---------

	 - **beforeUpload**
		 - type: `Function`,
		 - Require: `false`,
		 - explain: 上传图片前的一个回调，方便用户去对文件进行校验或者其他用处, return 0 || false 都可立刻中断上传操作。
		 - args: file
	 - **isZip**
		 - type: `Boolean`,
		 - default: `true`，
		 - Require: `false`,
		 - explain: 是否要对上传的图片进行压缩处理

	 - **isCrop**
		 - type: `Boolean`,
		 - default: `true`，
		 - Require: `false`,
		 - explain: 是否要对上传的图片进行裁剪处理

	 - **zipPercent**
		 - type: `Number`,
		 - default: `0.7`，
		 - Require: `false`,
		 - explain: 图片压缩的比例

	 - **containerWidth**
		 - type: `Number`,
		 - default: `375`，
		 - Require: `false`,
		 - explain: 裁剪时容器的宽度，移动端建议传入屏幕的宽度, 即 `document.body.clientWidth`

	 - **containerHeight**
		 - type: `Number`,
		 - default: `400`，
		 - Require: `false`,
		 - explain: 裁剪时容器的高度，移动端建议传入屏幕的高度, 即 `document.body.clientHeight`

	 - **cropBoxRatio**
		 - type: `Number`,
		 - Require: `false`,
		 - explain: 裁剪进行时，裁剪容器内裁剪框的裁剪比例，如果需要精确固定裁剪框的宽高，那么这里传入 `minCropBoxWidth/minCropBoxHeight`，此时比例是最小裁剪框宽高的比例，并且由于最小宽高已经设定，所以用户会无法继续拉小，记住这里尽量要传入 `Float` 类型，即比值

	 - **minCropBoxWidth**
		 - type: `Number`,
		 - default: `0`，
		 - Require: `false`,
		 - explain: 裁剪进行时，裁剪容器内裁剪框的最小宽度

	 - **minCropBoxHeight**
		 - type: `Number`,
		 - default: `0`，
		 - Require: `false`,
		 - explain: 裁剪进行时，裁剪容器内裁剪框的最小高度

	 - **cropBoxMovable**
		 - type: `Boolean`,
		 - default: `false`，
		 - Require: `false`,
		 - explain: 裁剪时是否可以移动裁剪框，如果固定裁剪框宽高时，建议为 `false` , 因为图片时默认可以移动并且让用户放大放小的

	 - **cropBoxResizable**
		 - type: `Boolean`,
		 - default: `0`，
		 - Require: `false`,
		 - explain: 裁剪时是否可以由用户改变裁剪框的宽高

	 - **outputCanvasData**
		 - type: `Object`,
		 - Require: `false`,
		 - explain: 裁剪完成后输出的 `canvas` 的一些配置，用于裁剪完图片后，我们可以通过改变 `canvas` 的大小，之后利用 API  `canvas.toDataURL()` 生成为 `base64` 时令高分辨率的图片可以维持清晰度，移动端非常适用，对象内部的参数在下面
		 - Properties:
			 - width: 生成的 `canvas` 的宽度,
			 - height: 生成的 `canvas` 的高度,
			 - minWidth: 生成的 `canvas` 的最小宽度，默认为 `0`,
			 - minHeight: 生成的 `canvas` 的最小高度，默认为 `0`,
			 - maxWidth: 生成的 `canvas` 的最大宽度，默认为 `Infinity`,
			 - maxHeight: 生成的 `canvas` 的最大高度，默认为 `Infinity`,
			 - fillColor: 生成的 `canvas` 的填充颜色，默认为 `transparent`,
			 - imageSmoothingEnabled: 默认生成到 `canvas` 上的图片是否平滑，默认为 `true`, 值还有 `false`,
			 - imageSmoothingQuality: 默认生成到 `canvas` 上的图片质量，默认为 'low', 值还有 'medium' 和 'high'，我们都知道 `canvas` 转为 `base64` 的图片时会默认压缩，所以记得这里的设置与 `canvas.toDataURL这个 API 的参数`

	Methods
	---------

	 - **success**
		 - args: file || canvas
		 - explain: 当压缩或裁剪成功后的自定义回调事件，只会有一个默认参数。
			 - 当只压缩不裁剪时: 返回的参数类型为一个 formData 形式的`Object` ，里面会有压缩后的文件或 `base64` 等等
			 - 当只裁剪不压缩时: 返回的参数为一个 `canvas` 标签
			 - 既压缩也裁剪时: 返回的参数为一个 `canvas` 标签

	 - **error**
		 - args: `error`
		 - explain: 当出错时会返回错误信息

Finally
---------
欢迎大家提出 pr，由于刚写出来，只有自己测试过，未经过多人大量测试，个人只是将自身想到可能出现的情况配上了参数，如果大家需要改动，自己随便改吧，如果希望成为公共组件之一，希望能多提pr，谢谢，此组件仍处于测试改善中。
