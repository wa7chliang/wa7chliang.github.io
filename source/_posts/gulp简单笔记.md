---
title: gulp简单笔记
---

### 安装
新建配置文件``` npm init ```
入口文件``` entry point ``` 为 ``` gulpfile.js ```
全局安装gulp
安装gulpfile.js require的所有依赖(gulp-imagemin要使用npm安装 防止压缩报错)
#### gulpfile.js:
```
var gulp = require('gulp'),
	less = require('gulp-less'),
	cleanCSS = require('gulp-clean-css'),   //压缩css
	changed = require('gulp-changed'),
	ugLify = require('gulp-uglify'),
	concat = require('gulp-concat'),
	imagemin = require('gulp-imagemin'),//压缩图片  
	pngquant = require('imagemin-pngquant'), // 深度压缩  
	browserSync = require("browser-sync").create();

// 实时编译less
gulp.task('less', function () {
	gulp.src(['src/less/*.less'])
		.pipe(less())
		.pipe(cleanCSS())   //压缩css
		.pipe(gulp.dest('src/css'))
});

// 压缩js
gulp.task("script", function () {
	gulp.src(['js/zepto.min.js', 'js/swiper.jquery.min.js'])
		.pipe(changed('src/js', { hasChanged: changed.compareSha1Digest }))
		.pipe(concat('index.js'))
		.pipe(ugLify())
		.pipe(gulp.dest('src/js'))
		.pipe(browserSync.reload({ stream: true }));
});

// 压缩图片  
gulp.task('images', function () {
	gulp.src('src/images/*.{png,jpg,gif}')
		.pipe(imagemin({
			progressive: true,
			svgoPlugins: [{ removeViewBox: false }],//不要移除svg的viewbox属性
			use: [pngquant()] //使用pngquant深度压缩png图片的imagemin插件
		}))
		.pipe(gulp.dest('src/img'))
		.pipe(browserSync.reload({ stream: true }));
});

//启动热更新  
gulp.task('serve', function () {
	gulp.start('script', 'less'); //开启没有解压images
	browserSync.init({
		port: 2017,
		server: {
			baseDir: ['src']
		}
	});
	gulp.watch('src/js/*.js', ['script']);         //监控文件变化，自动更新  
	gulp.watch('src/less/*.less', ['less']);
	gulp.watch('src/images/*.{png,jpg}', ['images']);
});

gulp.task('default', ['serve']);

```

### 启动
``` gulp ```
