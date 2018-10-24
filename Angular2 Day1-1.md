## 准备工作

### 通过 node.js 安装 Angular2
`npm -g install @angular/cli`

#### 创建项目
`ng new ProjectName`

#### 运行项目
`ng serve --open`

#### 将 Angular2 代码编译为 js 代码
`ng build`


## Angular2

#### index.html
> index.html 中绝大多数情况下不写代码, 可以加入全局 css 或 js

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>AngularDemo</title>
  <base href="/">

  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
  <!-- 可以引入 css 样式、js 等, 全局生效 -->
</head>
<body>
  <!-- app-root 主页对应 AppComponent.ts 组件 -->
  <app-root></app-root>
</body>
</html>
```

#### app.component.ts
```js
@Component({
  // selector: 对应主页标签名
  selector: 'app-root',
  // templateUrl: 对应的模板页面
  templateUrl: './app.component.html',
  // styleUrls: 对应的 css 样式文件
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    /* 主代码 */
    // 定义一个变量, 此变量可于对应的 html (以下略称 html) 中
    // 以 {{title}} 的形式调用其值
    title = 'AngularDemo';

    // 定义一个 ModuleInfo 数据类型的数组,
    // 赋值如下
    modules: ModuleInfo[] = [
    {
      // key 名可使用单引号 (不常用)
      moduleId: 1,
      moduleName: '管理员管理',
      selected: true
    },
    {
      moduleId: 2,
      moduleName: '角色管理',
      selected: true
    },
    {
      moduleId: 3,
      moduleName: '资费管理',
      selected: false
    },
    {
      moduleId: 4,
      moduleName: '账务账号',
      selected: false
    },
    {
      moduleId: 5,
      moduleName: '业务账号',
      selected: true
    }
  ];
}
```

#### 构造方法

- 构造方法,在构造方法里写的变量,默认就是成员变量了,不用重新书写. 必须是public,外面才能调用到
- 构造方法属性后面加一个问号(?),新建对象时这条属性可填可不填
- 加问号(?)的属性最好放在书写位置的最后面,避免新建对象时赋值不明确.
- 如果前一个属性后面加了问号,排在它后面的都要加问号
- 可不用声明成员变量, 直接写在构造方法里

```js
class ModuleInfo {

  // moduleId: number;
  // moduleName: string;
  // selected: boolean;

  // 构造方法
  constructor(public moduleId?: number, public moduleName?: string, public selected?: boolean) {}

  // 方法后加入 : 代表有返回值
  // (可使用 '|' 设置多个返回值的可能性, 即即有可能返回数字类型也可能返回字符串类型)
  getModuleId?(params?: string | number): number | string {
    return this.moduleId;
  }

}
```
- 至此, 在 html 中即可调用对应的变量或方法.

#### app.component.html

- ngFor 循环: 将需要循环的代码写在需要需要循环的标签属性位置<br>

> 需要给一个动态属性时, 使用[]<br>
> 需要将动态变量显示在页面时, 使用{{}}

- 使用 (change) 事件动态同步多选框<br>
> 此事件可使本标签的值发生改变时做对应的处理

```html
<ul>
  <li *ngFor="let module of modules">
    <!--<label *ngIf="module.selected"> 只显示选中的 -->
    <label>
      <!-- 此处 change 的处理为将 module.selected 的 true/false 反转 -->
      <!-- 而其上方的 [checked] 属性即为实时的实体类中的值 -->
      <input type="checkbox"
             name="moduleIds"
             [value]="module.moduleId"
             [checked]="module.selected"
             (change)="module.selected = !module.selected">
      {{module.moduleName}}
    </label>
  </li>
</ul>
```

## 事件
#### 点击事件

- 获取变量时使用 `this.变量名`

> .html

```html
<!-- 用小括号()包裹起来的属性对应的是某个事件 -->
<!-- (click) 即点击事件 -->
<!-- 关于 button 的值: 三目运算符, 根据变量 flag 的值决定'全选'或'取消' -->
<button (click)="onTestClick()">{{flag ? '全选' : '取消'}}</button>
```
> .ts

```js
// 声明一个 flag
flag = true;
// 点击事件: 点击全选后, 选中所有复选框
onTestClick() {
    // 在 ts 里, 声明变量可以使用 let 或 const
    // let 声明的是 '变量', const 声明的是 '常量'
    // 遍历所有 modules, 将当前 flag 的值赋值给所有 modules 以达到全选或取消的目的
    for (const module of this.modules) {
      module.selected = this.flag;
    }
    // 取反
    this.flag = !this.flag;
  }
```
#### [(ngModel)] 动态获取

- 动态获取输入框的值 ※<需在 app.module.ts 中引入模块: FormsModule>

```
@NgModule({
  declarations: [
    AppComponent,
    TestComponent,
  ],
  imports: [
    BrowserModule,
    // 导入 FormsModule
    FormsModule,
    CommonsModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

此时 input 标签中的值将会实时的显示在 p 标签中
```html
<input type="text" [(ngModel)]="text">
<br>
<p>{{text}}</p>
```

```js
text = '输入框中的文字';
```


## 页面间的通信

#### 传递数据

- 创建新的组件 `ng g c Test`



##### 注解: @Input()
- 此注解表示该变量的值是要从该组件的外部传递进来的
- 可传递任意对象, 包括变量, 数组, 实体类对象 等..

##### 注解: @Output()
- 此注解表示向该组件的外部传递数据、事件等

##### 方法: EventEmitter()
- 此方法可向组件外传递事件
- EventEmitter 中可存储很多事件, 当被调用时即会将事件运行一遍<br>
`childClick = new EventEmitter();`
    > ==注: 可将 childClick 理解为声明了一个事件==
- 接下来只需要调用 `this.childClick.emit(变量)` 即可将变量传递出去

```js
// 创建即自动实现 OnInit 接口, 相当于文档就绪
export class TestComponent implements OnInit {

  // 注解: @Input() 表示该值是要从该组件外部传递进来的
  // 可传递任意对象, 比如数组等等..
  @Input()
  title: string;

  // 向该组件的外部传递事件
  // 事件的向外传递
  // EventEmitter 中存储了很多事件, 当调用时会将事件运行一遍
  @Output()
  childClick = new EventEmitter();

  // 向父组件传递值
  // 将值填入点击事件的 emit() 中即可传递出去
  index = 0;

  constructor() { }

  ngOnInit() {
  }

  onChildClick() {
    // 此方法将成员变量的 index 自加, 并通过 .emit()方法传递出去
    this.index++;
    this.childClick.emit(this.index);
  }
}
```

- 在子组件中设置按钮点击事件

```html
<!-- 点击此 button 即可执行 onChildClick() 方法 -->
<button (click)="onChildClick()">子按钮</button>
```

- 在父组件 app.component 中引入新建的 app-test 组件
    - 此时在子组件 <app-test> 标签中即可调用子组件声明的==事件== `(childClick)=""`
    - 与此同时, 父组件需要有方法去接收子组件传递过来的数据 (即下方的 onParentClick() 方法)
    - 关于参数 $event
    > ==当 Angular 在调用我们的事件处理函数时, 会自动帮我们处理调用的参数. 而 $event 则可以自动映射为触发的事件.==

```html
<app-test [title]="text" (childClick)="onParentClick($event)"></app-test>
```

- 在父组件 app.component.ts 中声明方法来接收子组件传递过来的值

```js
// 子组件传递参数时, 父组件用参数来接取
onParentClick(i) {
  console.log('父组件中调用了函数' + i);
  // 复制全选反选代码 (测试当子组件的按钮点击时是否调用了以下代码)
  for (const module of this.modules) {
    module.selected = this.flag;
  }
  this.flag = !this.flag;
}
```


<hr>



## 构造数据
- 实现点击保存按钮后, 构造出数据

1. 建立点击事件

```html
<button (click)="onSaveClick()">保存</button>
```

> ```
> 复习: 表单 POST 提交的格式
> URL: localhost:8080/role/add
> 请求头: Content-Type: x-www-urlencoded
> 请求体: roleName=王八蛋&moduleIds=2&moduleIds=4&moduleIds=5
>
> // POST json 数据
> URL: localhost:8080/role/add
> 请求头: Content-Type: application/json
> 请求体:
>   {
>     'roleName': '王八蛋',
>     'moduleIds': [
>       2,
>       4,
>       5
>     ]
>   }
> ```


2. 声明 onSaveClick() 方法

- 方式1: 构造 Json 数据

```js
onSaveClick() {
  console.log(this.modules);

  // 1. 第一种方式: 构造 json 数据
  // const body = {
  //   roleName: ''
  // };
  // 声明变量: 把类型放在变量后面 -- let f: boolean;
  const body: any = {};
  body.roleName = this.text;
  // 初始化一个数组
  body.moduleIds = [];
  for (const module of this.modules) {
    if (module.selected) {
      // push: 将变量放入数组
      body.moduleIds.push(module.moduleId);
    }
  }
  // console.log(body);
    // 将 body 发送至后台
```

- 方式2: rxJS 框架

```js
  // 2. 第二种方式: 利用 rxJS 框架处理数据
  // from 函数用来处理数组
  // 遍历 --> 过滤 --> 提取属性
  from (this.modules)
    .pipe(
      // 写的就是处理数据的步骤, 即各种操作符
      // filter 其中填写 λ 表达式
      // 解释: 在遍历 modules 过程中取出的每一个 module, 指定到每个已经选择了的单选框

      filter(module => module.selected),

      // map 可根据自定义的逻辑转换, 提取 id 和 name
            // map(module => {
            //   const temp: any = {};
            //   temp.id = module.moduleId;
            //   temp.name = module.moduleName;
            //   return temp;
            // })

      // 此时 map 中的 module 就是过滤后的 module, 接下来提取出 moduleId
      map(module => module.moduleId),

      // 可使用 toArray() 方法将数据放入一个数组中
      toArray(),

      map(ids => {
        return {roleName: this.text, moduleIds: ids};
      })

    )
    .subscribe(id => console.log(id));

}
```


## 分页指示器实例

#### 创建模块 commons
`ng g m commons`

#### 在此模块中创建组件 PageIndicator
`ng g c commons/PageIndicator`

#### ts
- 实现 OnChanges, 以达成当 pageSize 改变时即运行方法: ngOnChanges

> ※ 关于 ngFor: 因 ngFor 不能遍历固定次数, 因此需创建一个固定长度的数组以便遍历.

```js
export class PageIndicatorComponent implements OnInit, OnChanges {

  // 根据传入的数量在 html 页面画 a 标签
  @Input()
  pageSize: number;

  @Output()
  pageSelected = new EventEmitter();

  // 创建一个固定长度的数组以便遍历
  array: number[] = [];

  // 创建全局变量控制上下页
  currentIndex = 1;

  constructor() { }

  ngOnInit() { }

  // 值改变时即运行
  ngOnChanges(changes: SimpleChanges): void {

    // 清空数组
    this.array.splice(0);
    // 循环页数的次数, 填充进数组
    for (let i = 0; i < this.pageSize; i++) {
      this.array.push(i + 1);
    }

  }

  onPrevClick() {
    if (this.currentIndex >= 2) {
      this.currentIndex--;
      this.pageSelected.emit(this.currentIndex);
    }
  }

  onPageSelected(index) {
    // 点击时将页码赋值给成员变量
    this.currentIndex = index;

    // 把当前点击的第几页的值传出
    this.pageSelected.emit(index);
  }

  onNextClick() {
    if (this.currentIndex < this.pageSize) {
      this.currentIndex++;
      this.pageSelected.emit(this.currentIndex);
    }
  }
}
```
