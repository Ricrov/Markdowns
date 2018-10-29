## 准备工作
- 安装 DevExtreme 集成组件<br>
`npm install --save devextreme devextreme-angular`
> `--save` 指令可以自动向 package.json 加入下载的库

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
- `<tsconfig.json>` 中引入库
```json
  "paths": {
    "jszip": [
        "node_modules/jszip/dist/jszip.min.js"
      ]
  }
```


- `<app.module.ts>` 添加模块 `DxButtonModule`
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

#### TreeView

- 加载 DxTreeViewModule <br>
`<app.module.ts>`
```ts
imports: [
    BrowserModule,
    DxButtonModule,
    // 引入树形菜单
    DxTreeViewModule,
    AppRoutingModule
  ],
```

- 创建 service
`ng g s App`
  - 创建假数据
```ts
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
```


- 实现函数
  - 可以根据父级的 id 返回它的所有子菜单<br>
  - 如果不传递 id, 那么加载一级菜单<br>
```ts
findMenus(parentId?: number): Observable { // 返回 Observable
    // 首先对 parentId 做判断
    if (parentId) {
      return from(this.items)
        .pipe(
          filter(item => item.parentId === parentId)
        );
    } else { // 取出一级菜单: 需要过滤出没有 parentId 的数据
      return from(this.items)
        .pipe(
          filter(item => !item.parentId)
        );
    }
  }
```

- `app.component.html`
  创建标签 `<dx-tree-view></dx-tree-view>` 并配置属性
```html
<dx-tree-view
  dataStructure="plain"
  width="30%"
  [createChildren]="createChildren">
  <!-- ↑ 告诉 tree-view 以什么样的规则加载子级 -->
</dx-tree-view>
```

- `app.component.ts`

```ts
// 告知 TreeView 以何种方式来动态加载菜单
  createChildren = (parent) => {
    console.log(parent);
    // 1. 获取 id, 判定 parent 是否为空
    const parentId = parent ? parent.itemData.id : null;
    // 2. 做数据映射, 转换成 TreeView 识别的格式

    let menus: any[] = [];

    this.service.findMenus(parentId)
      .pipe(
        map(menu => {
          return {
            id: menu.menuId,
            parentId: menu.parentId,
            text: menu.menuName,
            hasItems: !menu.menuPath
          };
        }), toArray()
      )
      .subscribe(items => menus = items);
    return menus;
  }
```
