## 一. 准备工作
- [DevExtreme官网](https://js.devexpress.com)
- 安装 DevExtreme 集成组件<br>
`npm install --save devextreme devextreme-angular`
> `--save` 指令可以自动向 package.json 加入下载的库

#### 1. angular.json这个文件是告诉angular项目中各文件都是做什么的(声明)
- `<angular.json>` 中配置 DevExtreme 的 css 样式
```ts
  "styles": [
    "src/styles.css"
  ],
```
↓↓↓ 添加以下 css 文件 ↓↓↓
```ts
  "styles": [
    "node_modules/devextreme/dist/css/dx.common.css",
    "node_modules/devextreme/dist/css/dx.light.css",
    "src/styles.css"
  ],
```
#### 2. `<tsconfig.json>` 中引入库
```json
  "paths": {
    "jszip": [
        "node_modules/jszip/dist/jszip.min.js"
      ]
  }
```
#### 3. `<app.module.ts>` 添加模块 `DxButtonModule`
```ts
imports: [
    BrowserModule,
    DxButtonModule,
    AppRoutingModule
],
```

_**此时当前组件的所有页面都可以使用 DevExtreme 组件**_


## 简单使用

- 可根据 type 更改样式 <br>
`<app.component.html>`
```html
<dx-button text="Click Me" type="default" (onClick)="onTestClick()"></dx-button>
<dx-button text="Click Me" type="normal"></dx-button>
<dx-button text="Click Me" type="success"></dx-button>
<dx-button text="Click Me" type="danger"></dx-button>
<dx-button text="Click Me" type="back"></dx-button>
```

- 弹出提示框 <br>
`<app.component.ts>`
```ts
  onTestClick() {
    notify('内容', 'success', 1000);
    notify('内容', 'error', 1000);
  }
```

- 确认提示框 <br>
`confirm` ( devextreme/ui/dialog 包 )
```ts
  onTestClick() {
    // 返回值是一个 Promise, 用法类似于 Observable, 两者之间可以相互转换
    confirm('超级管理员', '确认删除以下数据吗?')
    .then(result => console.log(result));
  }
```

## 应用

#### TreeView/表格
- 主页面HTML TreeView
`app.component.html`
```
<dx-box
  direction="col"
  width="100%"
  [height]="900">
  <dxi-item
    class="rect demo-dark header"
    [ratio]="1">
    <div style="text-align:center">
      <h1>
        Welcome to {{ title }}!
      </h1>
    </div>
  </dxi-item>
  <dxi-item
    [ratio]="2"
    [baseSize]="0">
    <dx-box
      direction="row"
      width="100%"
      [height]="600">
      <dxi-item
        class="rect demo-dark"
        [ratio]="1">
        <!-- dataStructure="plain"数据是一层结构-->
        <dx-tree-view dataStructure="plain" width="180"
                      [createChildren]="createChildren"
                      (onItemClick)="onMenuClick($event)"></dx-tree-view>
      </dxi-item>
      <dxi-item
        class="rect demo-light"
        [ratio]="1">
        <router-outlet></router-outlet>
      </dxi-item>
    </dx-box>
  </dxi-item>
  <dxi-item
    class="rect demo-dark footer"
    [ratio]="1">
    <dx-button text="点我" type="danger" (onClick)="onTestClick()"></dx-button>
  </dxi-item>
</dx-box>
```

- 主路由
`app-routing.module.ts`
```ts
const routes: Routes = [
  {
    path: 'role',
    loadChildren: () => RoleModule
  }
];

```

- 加载 第三方模块 <br>
`<app.module.ts>`
```ts
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    DxButtonModule,
    // 引入树形菜单
    DxTreeViewModule,
    // 引入盒子模块
    DxBoxModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```
- TreeView控制代码 <br>
`app.component.ts`
```ts
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
  // 使用providers加载service
  providers: [AppService]
})
export class AppComponent {
  title = 'DevExtremeDemo';
  // 告诉TreeView以何种方式来动态加载菜单
  // 这个函数的返回值就是一个数组
  createChildren = (parent) => {
    console.log(parent);
    // 1. 获取 id, 判定 parent 是否为空
    const parentId = parent ? parent.itemData.id : null;
    // 2. 做数据映射, 转换成 TreeView 识别的格式
    let menus: any[] = [];
    console.log('pID: ' + parentId);
    this.service.findMenus(parentId)
      .pipe(
        map(menu => {
          return {
            id: menu.menuId,
            parentId: menu.parentId,
            text: menu.menuName,
            hasItems: !menu.menuPath,
            path: menu.menuPath
          };
        }),
        toArray()
      ).subscribe(items => menus = items);
    console.log('第一级：' + menus);
    return menus;
  }

  constructor(private service: AppService,
              private router: Router) {
  }

  onTestClick() {
    console.log('xxxx');
    // notify('内容'，'error', 1000);
    confirm('超级管理员', '确认删除吗？')
      .then(result => console.log(result));
  }

  onMenuClick(item: any) {
    console.log(item);
    // 获取被点击的菜单绑定的数据
    const menu = item.itemData;
    // 在ts代码中进行路由的跳转
    this.router.navigateByUrl(menu.path);
  }
}
```
- 主页面数据 <br>
`app.service.ts`
```ts
import {Injectable} from '@angular/core';
import {from, Observable} from 'rxjs';
import {filter} from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class AppService {

  items: any[] = [
    {
      menuId: 1,
      menuName: '角色管理'
    },
    {
      menuId: 2,
      menuName: '角色列表',
      menuPath: '/role/list',
      parentId: 1
    },
    {
      menuId: 3,
      menuName: '添加角色',
      menuPath: '/role/add',
      parentId: 1
    },
    {
      menuId: 4,
      menuName: '修改角色',
      menuPath: '/role/modify',
      parentId: 1
    },
    {
      menuId: 5,
      menuName: '管理员'
    },
    {
      menuId: 6,
      menuName: '管理员列表',
      menuPath: '/admin/list',
      parentId: 5
    },
    {
      menuId: 7,
      menuName: '管理员添加',
      menuPath: '/admin/add',
      parentId: 5
    },
    {
      menuId: 8,
      menuName: '管理员修改',
      menuPath: '/admin/modify',
      parentId: 5
    },
    {
      menuId: 9,
      menuName: '资费管理'
    },
    {
      menuId: 10,
      menuName: '资费列表',
      menuPath: '/fee/list',
      parentId: 9
    },
    {
      menuId: 11,
      menuName: '资费添加',
      menuPath: '/fee/add',
      parentId: 9
    },
    {
      menuId: 12,
      menuName: '资费修改',
      menuPath: '/fee/modify',
      parentId: 9
    }
  ];

  constructor() {
  }

  // 可以根据父级的id返回它的所有子菜单; 如果不传递id过来，那么加载一级菜单
  findMenus(parentId?: number): Observable<any> {
    if (parentId) {
      return from(this.items)
        .pipe(
          filter(item => item.parentId === parentId)
    );
    } else { 
    // 取出一级菜单: 需要过滤出没有 parentId 的数据
      return from(this.items)
        .pipe( 
          filter(item => !item.parentId)
        );
    }
  }
}
```
- 子模块Role

`role/role-routing.moudle.ts`
```ts
const routes: Routes = [
  {
    path: 'list',
    component: RoleListComponent
  },
  {
    path: 'add',
    component: RoleEditorComponent
  },
  {
    path: 'modify',
    component: RoleEditorComponent
  }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class RoleRoutingModule { }
```

`role/role-routing.moudle.ts`
```ts
@NgModule({
  imports: [
    CommonModule,
    DxDataGridModule,
    HttpClientModule,
    RoleRoutingModule
  ],
  declarations: [RoleListComponent, RoleEditorComponent],
  providers: [RoleService]
})
export class RoleModule { }
```

`role/role.service.ts`
```
@Injectable({
  providedIn: 'root'
})
export class RoleService {
  constructor(private http: HttpClient) { }

  getRoles(skip, take): Observable<any> {
    return this.http.get('http://localhost:8080/role/list', {
      params: {
        skip: skip,
        take: take
      }
    });
  }
}
```

`role-list/role-list.component.html`

```
<dx-data-grid id="gridContainer"
              [dataSource]="dataSource"
              [showBorders]="true">
  <dxi-column dataField="roleId" caption="角色ID"></dxi-column>
  <dxi-column dataField="roleName" caption="角色名称"></dxi-column>
  <!-- true 代表用的后端数据, 分页/排序向后端发请求
  false代表前端数据进行处理, 分页/排序不重新向后端发数据
  -->
  <dxo-remote-operations
    [sorting]="false"
    [paging]="true">
  </dxo-remote-operations>
  <dxo-paging [pageSize]="1"></dxo-paging>
  <dxo-pager
    [showPageSizeSelector]="true"
    [allowedPageSizes]="[8, 12, 20]"
  ></dxo-pager>
</dx-data-grid>
```

`role-list/role-list.component.ts`
```ts
@Component({
  selector: 'app-role-list',
  templateUrl: './role-list.component.html',
  styleUrls: ['./role-list.component.css']
})
export class RoleListComponent implements OnInit {

  dataSource: any = {};

  constructor(private service: RoleService) {
    this.dataSource.store = new CustomStore({
      load: function (options) {
        console.log(options);
        return service.getRoles(options.skip, options.take).toPromise();
      }
    });
  }

  ngOnInit() {
  }

}
```

- 项目结构 

![image](https://s1.ax1x.com/2018/11/03/i42Arn.png)

- 效果

![image](https://s1.ax1x.com/2018/11/03/i42eaV.png)







