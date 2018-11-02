---
title: ElementRef的学习归纳
date: 2018-10-23 10:36:22
tags: Angular
categories: Angular
description: 本文章是个人学习ElementRef之后的学习归纳，内部代码项目为自写的demo，如有任何问题，欢迎留言。
---


# 前言

在JavaScript中，经常通过DOM语法直接操作或者改变页面的DOM元素。在Angular中，因为单向数据绑定和双向数据绑定等方法，使得Angular项目中很少看见类似DOM语法的东西。其实Angular也是有这种东西的，那就是ElementRef。

# Demo

## 一个简单的例子
	```
	import { Component, ElementRef } from '@angular/core';

	@Component({
	  selector: 'app-root',
	  template: `
	    <h1>首页</h1>
	    <button (click)="changeColor()">点我变色</button>
	    <div class="colorBox" style="width:200px;height:200px;border:1px solid #000;"></div>
	  `
	})
	export class AppComponent {
	  title = 'ElementRefDemo';
	  moreColor = ['white','red','blue','green','yellow','pink','black']
	  num = 0;
	
	  constructor(private elementRef:ElementRef){}
	
	  changeColor(){
	    if(this.num < this.moreColor.length-1){
	      this.num++;
	      this.elementRef.nativeElement.querySelector('.colorBox').style.backgroundColor = this.moreColor[this.num];
	      console.log('if',this.num)
	    }else{
	      this.num=0;
	      this.elementRef.nativeElement.querySelector('.colorBox').style.backgroundColor = this.moreColor[this.num];
	      console.log('else',this.num)
	    }
	    
	  }
	}
	```

ElementRef的使用比较简单，直接注入依赖即可调用他的nativeElement的方法。

> 这里要注意，如果要直接在初始化时控制某个DOM元素的属性值，需要设置一个0秒定时器或者使用生命周期钩子（这里不做代码展现），这是因为页面还未渲染完成，便开始寻找DOM元素，是会找不到元素然后报错的。

## 配合使用内部装饰器

	```
	import { Component, ElementRef, ViewChild } from '@angular/core';
	
	@Component({
	  selector: 'app-root',
	  template: `
	    <h1>首页</h1>
	    <button (click)="changeColor()">点我变色</button>
	    <div class="colorBox" style="width:200px;height:200px;border:1px solid #000;" #color></div>
	  `
	})
	export class AppComponent {
	  title = 'ElementRefDemo';
	  moreColor = ['white','red','blue','green','yellow','pink','black']
	  num = 0;
	
	  // constructor(private elementRef:ElementRef){}
	
	  @ViewChild('color')
	  colorDiv: ElementRef;
	
	  changeColor(){
	    if(this.num < this.moreColor.length-1){
	      console.log('111')
	      this.num++;
	    }else{
	      console.log('222')
	      this.num=0;
	    }
		this.colorDiv.nativeElement.style.backgroundColor = this.moreColor[this.num];
	  }
	}
	```

使用内部装饰器做一个`ElemengRef`的“装饰"，这样在后续频繁使用的时候，代码会更简洁一些。

## 更安全更好的使用方法

> 在angular官网，关于`ElementRef`的内容中，说到了“当需要直接访问 DOM 时，请把本 API 作为最后选择。优先使用 Angular 提供的模板和数据绑定机制。”

> 使用ElementRef其实就是直接访问DOM，这种情况下遇到XSS攻击的时候，你的应用会非常的脆弱。除了优先使用的模板和数据绑定机制以外，还有一种安全的使用方法，那就是利用`Renderer2`。

代码如下：

	```
	import { Component, ElementRef, ViewChild, Renderer2 } from '@angular/core';
	
	@Component({
	  selector: 'app-root',
	  template: `
	    <h1>首页</h1>
	    <button (click)="changeColor()">点我变色</button>
	    <div class="colorBox" style="width:200px;height:200px;border:1px solid #000;" #color></div>
	  `
	})
	export class AppComponent {
	  title = 'ElementRefDemo';
	  moreColor = ['white','red','blue','green','yellow','pink','black']
	  num = 0;
	
	  // constructor(private elementRef:ElementRef){}
	
	  @ViewChild('color')
	  colorDiv: ElementRef;
	
	  constructor(private elementRef:ElementRef,private renderer:Renderer2){}
	
	  changeColor(){
	    if(this.num < this.moreColor.length-1){
	      console.log('111')
	      this.num++;
	    }else{
	      console.log('222')
	      this.num=0;
	    }
	    this.renderer.setStyle(this.colorDiv.nativeElement,'backgroundColor',this.moreColor[this.num])
	  }
	}
	```

# 总结

在使用上，`ElementRef`在某些情况下，会比模板和数据绑定来的更为简单方便。但不可否认，容易被XSS攻击是它的致命缺点。如果要使用`ElementRef`，请千万记得配合`Renderer2`一起使用，使用`Renderer2`还可以减少应用层与渲染层之间的强耦合关系，让我们的应用可以更好地运行在不同环境。