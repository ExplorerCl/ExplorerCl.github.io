---
title: HttpClient的学习归纳
date: 2018-09-25 09:48:52
tags: angular
categories: Angular
description: 本文章是个人学习HttpClient之后的学习归纳，包括各类HttpClient请求和拦截器等，内部代码项目为自写的demo，如有任何问题，欢迎留言。
---

# HttpClient请求

HttpClient有五种请求。

- get
- post
- patsh
- put
- delete

## 导入HttpClientModule模块
	```
	import { BrowserModule } from '@angular/platform-browser';
	import { NgModule } from '@angular/core';
	import { HttpClientModule } from '@angular/common/http';
	
	import { AppComponent } from './app.component';
	
	@NgModule({
	  declarations: [
	    AppComponent
	  ],
	  imports: [
	    BrowserModule,
	    HttpClientModule
	  ],
	  providers: [],
	  bootstrap: [AppComponent]
	})
	export class AppModule { }
	```
## 设置json-server

使用json-server来模拟服务端数据，方便我们进行demo的实际测试。

json文件内容如下

	```
	{
	  "user": [
	    {
	      "name": "gww",
	      "id": "1",
	      "age": "30"
	    },
	    {
	      "name": "ExplorerCl",
	      "id": "2",
	      "age": "23"
	    },
	    {
	      "name": "vue",
	      "id": "3",
	      "age": "24"
	    },
	    {
	      "name": "tao",
	      "id": "4",
	      "age": "25"
	    },
	    {
	      "name": "san",
	      "id": "5",
	      "age": "26"
	    }
	  ]
	}
	```

> 设置好文件之后启动json-server

## Get请求
	```
	import { Component, OnInit } from '@angular/core';
	import { HttpClient, HttpParams, HttpHeaders } from '@angular/common/http';
	
	import { Observable } from 'rxjs';
	import { tap } from 'rxjs/operators';
	
	interface User {
	  name: string;
	  id: number;
	  age: number;
	}
	
	@Component({
	  selector: 'app-root',
	  template: `
	    <ul *ngIf="user$ | async as users else noData">
	      <li *ngFor="let user of users">
	        <span>Name: {{user.name}}</span> | 
	        <span>id: {{user.id}}</span> | 
	        <span>age: {{user.age}}</span>
	      </li>
	    </ul>
	  `,
	  styleUrls: ['./app.component.css']
	})
	export class AppComponent implements OnInit {
	  user$: Observable<User[]>;
	  constructor(private http: HttpClient) {}
	
	  ngOnInit() {
	    this.user$ = this.http
	      .get<User[]>(
	        'http://localhost:3000/user'
	      )
	      .pipe(tap(console.log));
	  }
	}
	```

> tap标签用来监听Observable的生命周期事件，并且不会产生干扰。

> asycn as为异步请求，noData用于检测数据的上报异常。

> subscribe也可以用于获取请求结果，不过与Observable的类型有差别，所以需要做一些修改。

### 使用HttpParams对象
	```
	import { Component, OnInit } from '@angular/core';
	import { HttpClient, HttpParams, HttpHeaders } from '@angular/common/http';
	
	import { Observable } from 'rxjs';
	import { tap } from 'rxjs/operators';
	
	interface User {
	  name: string;
	  id: number;
	  age: number;
	}
	
	const params = new HttpParams().set('id','1')
	
	@Component({
	  selector: 'app-root',
	  template: `
	    <ul *ngIf="user$ | async as users else noData">
	      <li *ngFor="let user of users">
	        <span>Name: {{user.name}}</span> | 
	        <span>id: {{user.id}}</span> | 
	        <span>age: {{user.age}}</span>
	      </li>
	    </ul>
	  `,
	  styleUrls: ['./app.component.css']
	})
	export class AppComponent implements OnInit {
	  user$: Observable<User[]>;
	  constructor(private http: HttpClient) {}
	
	  ngOnInit() {
	    this.user$ = this.http
	      .get<User[]>(
	        'http://localhost:3000/user' , {params}
	      )
	      .pipe(tap(console.log));
	  }
	}
	```

> params用来设置请求的具体参数。set里面的第一个参数为参数名，第二个为具体参数，用于控制返回请求内容的特定参数名下的某个特定参数。

> set可以设置多个，这里需要注意，这里设置多个的请求参数，他们之间为&&关系，所以请求回的数据一定在这些参数的交集范围内。

### 使用HttpHeaders
	```
	...
	
	const params = new HttpParams().set('id','1')
	const headers = new HttpHeaders().set('token','sin')
	
	@Component({
	  ...
	})
	export class AppComponent implements OnInit {
	  ...
	  ngOnInit() {
	    this.user$ = this.http
	      .get<User[]>(
	        'http://localhost:3000/user' , {headers,params}
	      )
	      .pipe(tap(console.log));
	  }
	}
	```

> 使用浏览器的开发者工具，可以在Network - all中查看请求的文件，然后可以发现Access-Control-Request-Headers的内容是与设置的请求头参数一致的。

> 请求头主要用于客户端浏览器、请求页面和服务器相关信息的说明，方便我们可以查看请求的相关信息等。


## Put请求
	```
	...
	
	const params = new HttpParams().set('id','1')
	const headers = new HttpHeaders().set("Content-type","application/json; charset=UTF-8")
	
	@Component({
	  ...
	})
	export class AppComponent implements OnInit {
	  ...
	  ngOnInit() {
	    this.putHttp();
	    this.user$ = this.http
	      .get<User[]>(
	        'http://localhost:3000/user' , {headers,params}
	      )
	      .pipe(tap(console.log));
	  }
	  putHttp(){
	    this.http.put('http://localhost:3000/user/1',{
	        name: 'gww',
	        id: '1',
	        age: '30'
	      },{
	        headers
	      }
	    )
	    .subscribe(val=>{
	      console.log('Put请求执行成功',val);
	    },
	    error => {
	      console.log('Put请求执行失败',error);
	    },
	    () => {
	      console.log('Put请求执行完成了');
	    }
	    )
	  }
	}
	```

> 配合Get请求，可以看到此时的id为1的相关数据已经被更改。

> Put请求地址需要为具体的某个数据的地址，例如上述demo中的'http://localhost:3000/user/1'，其实就是id为1的数据的地址。

## Patch请求
	```
	...
	const params = new HttpParams().set('id','2')
	const headers = new HttpHeaders().set("Content-type","application/json; charset=UTF-8")
	
	@Component({
	  ...
	})
	export class AppComponent implements OnInit {
	  ...
	  ngOnInit() {
	    this.patchHttp();
	    this.user$ = this.http
	      .get<User[]>(
	        'http://localhost:3000/user' , {headers,params}
	      )
	      .pipe(tap(console.log));
	  }
	  patchHttp(){
	    this.http.patch('http://localhost:3000/user/2',{
	      name:'ExplorerCl'
	    },{headers})
	    .subscribe(val=>{
	        console.log('patch请求执行成功',val);
	      },
	      error => {
	        console.log('patch请求执行失败',error);
	      },
	      () => {
	        console.log('patch请求执行完成了');
	      }
	    )
	  }
	}
	```

> Patch请求用于更新单个数据内的部分参数。

## Put与Patch请求的区别

Put请求和Patch请求在结果上看不出太大区别。

最大的区别在于，Put请求是产生一个新版本，然后用新版本将原版本替换掉。而Patch请求则是在原版本上进行更新改动。

并且，如果Patch请求的资源不存在，那么会依据Patch请求的内容，创建一个新的资源。

Patch请求适合用于增量修改，并且Patch如果要是用，一定要使用正确的方式，否则会产生一些副作用。

## post请求
	```
	...
	const params = new HttpParams().set('id','6')
	const headers = new HttpHeaders().set("Content-type","application/json; charset=UTF-8")
	
	@Component({
	  ...
	})
	export class AppComponent implements OnInit {
	  ...
	  ngOnInit() {
	    this.postHttp();
	    this.user$ = this.http
	      .get<User[]>(
	        'http://localhost:3000/user' , {headers,params}
	      )
	      .pipe(tap(console.log))
	  }

	  postHttp(){
	    this.http.post('http://localhost:3000/user',{
	      name:'React',
	      id:'6',
	      age:'28'
	    },{headers})
	    .subscribe(val=>{
		      console.log('post请求执行成功',val);
		    },
		    error => {
		      console.log('post请求执行失败',error);
		    },
		    () => {
		      console.log('post请求执行完成了');
		    }
		 )
	   }
	}
	```

> 这里可以通过Get方法查看新增的数据，或者直接打开相应的json文件来看，可以发现多了一个id为6的内容。

## Delete请求
	```
	...
	const headers = new HttpHeaders().set("Content-type","application/json; charset=UTF-8")
	
	@Component({
	  ...
	})
	export class AppComponent implements OnInit {
	  ...
	  ngOnInit() {
	    this.deleteHttp()
	    this.user$ = this.http
	      .get<User[]>(
	        'http://localhost:3000/user' , {headers}
	      )
	      .pipe(tap(console.log))
	  }
	  deleteHttp(){
	    this.http.delete('http://localhost:3000/user/6',{headers})
	    .subscribe(val=>{
	        console.log('delete请求执行成功',val);
	      },
	      error => {
	        console.log('delete请求执行失败',error);
	      },
	      () => {
	        console.log('delete请求执行完成了');
	      }
	    )
	  }
	}

> 这里使用Delete请求，删除刚刚使用Post请求新增的数据，demo中请求都封装成方法，可以自己多次调用方法来查看各个请求的作用结果。

# HttpClient拦截器

拦截器用于监听和转换从应用发送到服务器的HTTP请求，或者监视和转换从该服务器返回到本应用的那些响应。

拦截器可以用常规、标准的方式对每次的HTTP请求/响应任务执行从认证到记日志等多种的隐式任务，方便开发人员进行检查。

## 一个空白拦截器
	```
	import {Injectable} from '@angular/core';
	import { HttpInterceptor, HttpRequest, HttpHandler, HttpEvent } from '@angular/common/http';
	import { Observable } from 'rxjs';
	
	@Injectable()
	export class AutiInterceptor implements HttpInterceptor{
	    
	    intercept(req:HttpRequest<any>, next:HttpHandler):
	        Observable<HttpEvent<any>> {
	            return next.handle(req);
	        }
	}
	```

> 这里的intercept()方法接收两个固定的参数：req:HttpRequest代表请求对象；next:HttoHandler表示拦截器链表中的下一个拦截器，让请求流能走到下一个拦截器，该对象下有一个handle()方法，该方法返回一个Observable对象。

> 拦截器无法修改原始的请求对象。

## 记录日志的拦截器

logging.interceptors.ts

	```
	import {Injectable} from '@angular/core';
	import { HttpInterceptor, HttpRequest, HttpHandler } from '@angular/common/http';
	import { finalize, tap } from 'rxjs/operators';
	
	@Injectable()
	export class LoggingInterceptor implements HttpInterceptor{

	    intercept(req:HttpRequest<any>, next:HttpHandler){
	        const startTime = Date.now();
	        let status: string;
	
	        return next.handle(req).pipe(
	            tap(
	                event => status = event instanceof HttpRequest ? 'succeeded' : '',
	                error => status = 'failed'
	            ),
	            finalize(()=>{
	                const elapsed = Date.now() - startTime;
	                const message = req.method + " " + req.urlWithParams + " " + status + "in" + elapsed + "ms";
	
	                console.log(message)
	            })
	        );
	    }
	}
	```

### 在AppModule中配置拦截器
	```
	...
	import { LoggingInterceptor } from './logging.interceptors';
	
	@NgModule({
	  ...
	  providers: [
	    {provide: HTTP_INTERCEPTORS, useClass: LoggingInterceptor,multi:true}
	  ],
	  ...
	})
	export class AppModule { }
	```

> 这里只是demo，所以只有一个拦截器，正常项目下一般会有多个拦截器，这个时候就可以新建一个providers文件，用来存储所有拦截器的配置，然后把相应的providers文件配置在AppModule中。

> 这里的multi:true表示HTTP_INTERCEPTORS是一个多重提供商的令牌，表示它会注入一个多值的数组，而不是单个值。


之后我们可以通过浏览器的开发者工具查看日志内容，每一个请求的请求时间都会在日志中被记录。

# 总结和心得

五种请求其实看下来，很符合编程很常见的操作，增(post)删(delete)改(put、patch)查(get)，虽然部分的请求会有多种功能，但是按照正常思路而言，还是以最主要的功能来判断使用什么请求，避免不必要的麻烦。

拦截器的intercept()自带的req和next参数其实已经表明了，一个正规的网址一般会有多个拦截器共同去作用。拦截器的作用过程其实与过滤器有些相似，对特定的对象按照设定的要求做相应的操作。