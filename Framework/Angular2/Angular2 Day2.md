## 路由 Routing

#### 创建项目时选择创建路由模块
- 会在 `app/` 下生成 `app-routing-modules.ts`

#### app.component.html

- `<router-outlet></router-outlet>` 路由出口

#### 创建子组件 first-child & second-child

#### `app-routing-modules.ts`

```ts
const routes: Routes = [
  // 每一个对象都是一个路由配置
  {
    path: 'first-child',
    component: FirstChildComponent
  },
  {
    path: 'second-child',
    component: SecondChildComponent
  }
];
```
此时在地址栏输 `http://localhost:4200/first-child` 即可路由到子组件.
也可在 html 主页添加 `a` 标签以链接
> `routerLink` 属性: 可以在应用中链接到特定的路由
```html
<a routerLink="first-child">第一个组件</a><br>
<a routerLink="second-child">第二个组件</a><br>
```
#### 实现当访问首页时 (4200) 显示 first-child 页面, 路径地址也变为 /first-child
```ts
const routes: Routes = [
  // 实现访问主页面自动跳转
  {
    path: '',
    redirectTo: 'first-child',
    pathMatch: 'full'
  }
];
```

#### 单页面 web 应用 SPA (Single Page-web Application)
`app-routing.module.ts`
> `{useHash: true}` 开启时路径会拼接 '#', 在资源臃肿时使用此参数可以实现局部缓存的功能

```ts
@NgModule({
  imports: [RouterModule.forRoot(routes, {useHash: true})],
  exports: [RouterModule]
})
```

### 创建模块+路由+组件
`ng g m Role --routing` <br>
`ng g c Role`

- `app-routing.module.ts` 配置加载新创建的路由

- 延迟加载 -- loadChildren
  > loadChildren是延迟加载子模块, 这对于加载页面的性能有很好的提升. <br>
  通俗的讲就是说进入主模块的时候, 子模块不加载, 等需要进入子模块的时候才加载.<br>
  项目划分模块的时候, 使用loadChildren配置路由是最佳选择方案.

```ts
const routes: Routes = [
  {
    path: 'role',
    loadChildren: () => RoleModule
  }
];
```

- 在 role 下创建组件 RoleEditor
`ng g c role/RoleEditor`

- 配置子路由
`role-routing.module.ts`
```ts
const routes: Routes = [
  /*
  /role/list     -- 列表页
  /role/add      -- 添加页面
  /role/modify/1 -- id 为1的角色的修改页面
   */
   {
     path: 'list',
     component: RoleComponent
   },
   {
     path: 'add',
     component: RoleEditorComponent
   },
   {
     path: 'modify/:roleId',
     component: RoleEditorComponent
   }
];
```

#### 铺数据
`role.component.ts`
```ts
export class RoleComponent implements OnInit {

  dataSource = [
    {
      roleId: 1,
      roleName: '超级管理员'
    },
    {
      roleId: 2,
      roleName: '管理员'
    },
    {
      roleId: 3,
      roleName: '王八蛋'
    }
  ];
  constructor() { }
  ngOnInit() { }
}
```

`role.component.html`
```html
<div style="width: 30%;">
  <button routerLink="/role/add" style="float: right;">添加</button>
  <table>
    <tr>
      <th>角色id</th>
      <th>角色名称</th>
      <th>操作</th>
    </tr>
    <tr *ngFor="let role of dataSource">
      <td>{{role.roleId}}</td>
      <td>{{role.roleName}}</td>
      <td colspan="2">
        <!-- modify 后需要拼接 id -->
        <button routerLink="/role/modify/{{role.roleId}}">修改</button>
        <button>删除</button>
      </td>
    </tr>
  </table>
</div>
```

#### 在 role-editor 组建中拿到数据
`role-editor.component.ts`

```ts
export class RoleEditorComponent implements OnInit {

  // 对路由进行注册
  // 只要访问了该组件, 下面的 λ 事件就会执行
  constructor(private route: ActivatedRoute) {
    this.route.paramMap.subscribe(params => {
      const roleId = params.get('roleId');
      // 对 roleId 做非空判断
      // 则可区分是点击添加或是编辑按钮进入该组件的
      console.log(roleId);
    });
  }

  ngOnInit() {
  }
}
```

### 创建 service 以发送请求
`ng g s role/Role` ※ 此命令不会自动文件夹, 需手动填写
自动生成类 RoleService

将假数据放入其中

```ts
export class RoleService {

  dataSource = [
    {
      roleId: 1,
      roleName: '超级管理员'
    },
    {
      roleId: 2,
      roleName: '管理员'
    },
    {
      roleId: 3,
      roleName: '王八蛋'
    }
  ];

  constructor() { }
}

```

##### 在 service 提供一个方法

```ts
  // 此方法为同步方法 -- 即在组件中调用此方法时需要等待数据返回.
  // 解决方法: 使用 rxJS 返回 Observable -- 异步
  // 可使用泛型 <>
  getRoleList(): Observable<any[]> {
    // of 操作符: 把数组当做一个整体进行发送
    return of(this.dataSource);
  }
```
##### 在 role 组件中依赖注入
`role.component.spec.ts`
```ts
@Component({
  selector: 'app-role',
  templateUrl: './role.component.html',
  styleUrls: ['./role.component.css'],
  // 已声明到模块中
  // 如果某个 service 只需要在该组件下使用, 则再次在此处声明
  // providers: [RoleService]
})
export class RoleComponent implements OnInit {
  // 对于此类来说, 调用方法即可拿到请求的数据. 而不是从此类发送请求的. 需新创建一个类为组件提供数据.

  // 声明一个全局的 DataSource
  dataSource: any[] = [];

  // 依赖注入, 自动将 RoleService 的对象通过构造方法传递进来. (单例)
  constructor(private roleService: RoleService) { }

  ngOnInit() {
    // resp: 方法返回的结果 -- 接到的数组
    this.roleService.getRoleList().subscribe(resp => {
      // 把请求的数据赋值给成员变量
      this.dataSource = resp;
    });
  }
}
```

##### 将 service 声明到模块中
`role.module.ts`

```ts
@NgModule({
  imports: [
    CommonModule,
    RoleRoutingModule
  ],
  declarations: [RoleComponent, RoleEditorComponent],
  // 声明到模块中, 那么该模块下的所有组件都可以使用
  providers: [RoleService]
})
export class RoleModule { }

```


### 发送网络请求

##### 所需依赖
`role.module.ts`

```ts
@NgModule({
  imports: [
    CommonModule,
    // 发送请求所需的依赖
    HttpClientModule,
    RoleRoutingModule
  ],
  declarations: [RoleComponent, RoleEditorComponent],
  // 声明到模块中, 那么该模块下的所有组件都可以使用
  providers: [RoleService]
})
```

##### 发送请求
`role.service.ts`
- step 01: 声明不变的 url 地址片段 <br>
`host = 'http://localhost:8080/';`

- step 02: 利用构造方法使用 client 对象发送请求 <br>
```ts
constructor(private client: HttpClient) {
}
```

- step 03: 向 'http://localhost:8080' 发送一个 GET 请求 <br>
```ts
getRoleList(): Observable<any[]> {
    return this.client.get(`${this.host}role/list`);
}
```

- step 04: 解决 CORS 跨域请求问题
> 将跨域请求加入响应头 - 后台创建拦截器 - 加入响应头

```java
public class CorsInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        response.setHeader("Access-Control-Allow-Origin", "http://localhost:4200");
        response.setHeader("Access-Control-Allow-Methods", "POST, GET");
        // 3600秒之内预检请求都有效, 在这个时间内不需要预请求
        response.setHeader("Access-Control-Max-Age", "3600");
        // 将自定义请求头添加进允许列表
        response.setHeader("Access-Control-Allow-Headers", "Content-Type, Access-Control-Allow-Headers, Authorization, X-Requested-With, name");


        return true;
    }
}
```

- step 05: 可自定义请求头
```ts
// 自定义请求头
  headers: HttpHeaders = new HttpHeaders({
    name: 'Ric'
  });
```
- 发送带参数的请求
```ts
getRoleList(): Observable<any> {
    // of 操作符: 把数组当做一个整体进行发送
    // return of(this.dataSource);
    // 向 'http://localhost:8080' 发送一个 GET 请求
    // 可在 options 中添加配置请求头等
    // 若添加了自定义请求头, 则会自动发送两次请求
    // 需在后台设定允许自定义请求头
    return this.client.get(`${this.host}role/list`, {
      headers: this.headers,
      params: {
        page: '1',
        size: '20'
      }
    });
  }
```
- step 06: 发送 POST 请求
```ts
// POST 请求
addRole(role: any): Observable<any> {
  // 自动转为 json 格式发送至后台 (亦可在 options 中添加参数)
  return this.client.post(`${this.host}role/add`, role, {});
}
```

- step 07: role 组件调用方法传递数据
`role.component.ts`
```ts
ngOnInit() {
    // 声明需要传递的数据
    const body = {roleId: 10, roleName: '缺心眼'};
    this.roleService.addRole(body).subscribe(resp => console.log(resp));
  }
```

- step 08: JAVA 后台接收数据
```ts
@PostMapping("/add")
public String add (@RequestBody RoleEntity role) {
    System.out.println(role);
    return "redirect:/role/role_list.html";
}
```
