---
layout: post
title: TreeView Guide
---

# Tree View Guide

이 가이드에서는 Visual Studio 코드에 대한 View Container 및 Tree View를 제공하는 확장을 작성하는 방법을 설명합니다. 위 [링크]( https://github.com/Microsoft/vscode-extension-samples/tree/master/tree-view-sample)에서 소스 코드가있는 샘플 확장을 찾을 수 있습니다.

## View Container

View Container는 기본으로 제공되는 View Container옆에 표시되는 View 목록을 포함합니다.

![View Container](images/tree-view/view-container.png)

View Container에 View를 추가하려면 먼저 [`contributes.viewsContainers`](/api/references/contribution-points#contributes.viewsContainers)를 사용하여 등록해야합니다. 'package.json`에 있는 Contribution Point에서(직역하면 기여 포인트이지만, '리스트를 추가하는 장소'정도로 생각하시면 됩니다. - 역주) 다음 필수 입력란을 지정해 주어야 합니다.

- `id` : 생성할 새로운 View Container의 이름
- `title` : View Container의 상단에 나타날 이름
- `icon` : 액티비티 바의 View Container에 표시 될 이미지

```json
"contributes": {
  "viewsContainers": {
    "activitybar": [
      {
        "id": "package-explorer",
        "title": "Package Explorer",
        "icon": "media/dep.svg"
      }
    ]
  }
}
```

## Tree View

View는 View Container 안에 표시되는 UI 섹션입니다. [`contributes.views`](/api/references/contribution-points#contributes.views)에 있는 Contribution Point를 사용하면 내장되거나, 생성되어 있는 View Container에 새로운 View를 추가 할 수 있습니다.


![View](images/tree-view/view.png)

View를 제공하려면 먼저 [`contributes.views`](/api/references/vscode-api)를 사용하여 등록해야합니다.`package.json`의 Contribution Point에 있는 View의 식별자와 이름을 지정해야하며 다음 위치에 작성할 수 있습니다.

- `explorer` : 사이드 바에있는 탐색기View
- `debug` : 사이드 바에있는 디버그 View
- `scm` : 사이드 바에있는 소스 컨트롤View
- `test` : 사이드 바에있는 탐색기View 테스트
- 생성한 View Container

예제:

```json
"contributes": {
  "views": {
    "package-explorer": [
      {
        "id": "nodeDependencies",
        "name": "Node Dependencies",
        "when": "explorer"
      }
    ]
  }
}
```

사용자가 View를 열면 VS Code는 activationEvent[`onView:${viewId}`](/api/references/activation-events#onView)를 실행합니다. (위의 예에서`onView:nodeDependencies`). `when` 컨텍스트 값을 제공하여 View를 언제 보여줄 지를 제어 할 수도 있습니다.

## View Actions

View의 다음 위치에서 작업을 제공 할 수 있습니다.

- `view/title` : View 제목에 액션을 표시 할 위치입니다. 기본 또는 인라인 동작은 `"group": "navigation"`을 사용하고 나머지는 `...` 메뉴에 있는 보조 동작입니다.

- `view/item/context` : Tree 항목에 대한 액션을 보여주는 위치. 인라인 액션은``group ": "inline "`을 사용하고 나머지는 `...` 메뉴에 있는 보조 동작입니다.

`when` 속성을 사용하여 이러한 액션을 언제 보여줄 지를 제어 할 수 있습니다.

예제 :

```json
"contributes": {
  "commands": [
    {
      "command": "nodeDependencies.refreshEntry",
      "title": "Refresh",
      "icon": {
        "light": "resources/light/refresh.svg",
        "dark": "resources/dark/refresh.svg"
      }
    },
    {
      "command": "nodeDependencies.addEntry",
      "title": "Add"
    },
    {
      "command": "nodeDependencies.editEntry",
      "title": "Edit",
      "icon": {
        "light": "resources/light/edit.svg",
        "dark": "resources/dark/edit.svg"
      }
    },
    {
      "command": "nodeDependencies.deleteEntry",
      "title": "Delete"
    }
  ],
  "menus": {
    "view/title": [
      {
        "command": "nodeDependencies.refreshEntry",
        "when": "view == nodeDependencies",
        "group": "navigation"
      },
      {
        "command": "nodeDependencies.addEntry",
        "when": "view == nodeDependencies"
      }
    ],
    "view/item/context": [
      {
        "command": "nodeDependencies.editEntry",
        "when": "view == nodeDependencies && viewItem == dependency",
        "group": "inline"
      },
      {
        "command": "nodeDependencies.deleteEntry",
        "when": "view == nodeDependencies && viewItem == dependency"
      }
    ]
  }
}
```

**참고:** 특정 항목에 대한 작업을 표시하려면 `TreeItem.contextValue`를 사용하여 Tree 항목의 컨텍스트를 정의하고 `when` 연산자의 키 `viewItem`에 대한 컨텍스트 값을 지정할 수 있습니다.

예제 :

```json
"contributes": {
  "menus": {
    "view/item/context": [
      {
        "command": "nodeDependencies.deleteEntry",
        "when": "view == nodeDependencies && viewItem == dependency"
      }
    ]
  }
}
```

## TreeDataProvider

확장 작성자는 [`TreeDataProvider`](/api/references/vscode-api#TreeDataProvider)를 프로그래밍 방식으로 등록하여 View의 데이터를 채워야합니다.

```typescript
vscode.window.registerTreeDataProvider('nodeDependencies', new DepNodeProvider());
```

구현을 위해 [nodeDependencies.ts](https://github.com/Microsoft/vscode-extension-samples/tree/master/tree-view-sample/src/nodeDependencies.ts)를 참조하십시오.

## TreeView

프로그래밍 방식으로 View에 대해 일부 UI 작업을 수행하려면`window.registerTreeDataProvider` 대신 `window.createTreeView`를 사용할 수 있습니다. 그러면 View 조작을 수행하는 데 사용할 수있는 View에 대한 액세스 권한이 얻을 수 있습니다.

```typescript
vscode.window.createTreeView('ftpExplorer', { treeDataProvider: new FtpTreeDataProvider() });
```

구현을 위해 [ftpExplorer.ts](https://github.com/Microsoft/vscode-extension-samples/tree/master/tree-view-sample/src/ftpExplorer.ts)를 참조하십시오.