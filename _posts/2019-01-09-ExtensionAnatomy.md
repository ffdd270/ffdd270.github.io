https://code.visualstudio.com/api/get-started/extension-anatomy

# 확장 해부해보기

저번 토픽에서는 간단한 확장을 한번 돌려봤습니다. 그런데 그 코드가 수면 밑에선 어떻게 돌아갈까요?

Hello World 확장은 3가지 일을 합니다:

* onCommand 실행 이벤트 : onCommnand:extension.helloWorld를 등록합니다. 그래서 사용자가 Hello World 명령을 실행 했을 때 확장이 활성화 된 것입니다.

* contributes.commands라는 Contribution Point를 사용하여 명령 팔레트에서 Hello World 명령을 사용할 수있게 하고, 명령 ID extension.helloWorld에 바인딩합니다.

* 'commands.registerCommand'라는 VS Code API를 사용하여 함수를 등록 되어 있는 명령 ID 'extension.helloWorld'에 바인딩합니다.

이 세 가지 개념을 이해하는 것이 VS 코드에서 확장 기능을 작성하는 데 매우 중요합니다 :

* 활성화 이벤트들 : 확장이 활성화 될 때 실행되는 이벤트들입니다.

* Contribution Points: VS.Extension Manifest에서 VS 코드를 확장하기 위해 사용자가 한 정적 선언입니다.

* VS Code API: 확장기능의 코드에서 실행할 수 있는 JavaScript API 세트입니다.

일반적으론, 확장 기능은 Contribution Points와 VS Code API를 조합하여 VS Code의 기능을 확장할 수 있습니다.
Extension Capabilities Overview 주제는 Extension에 맞는 Contribution Point 및 VS Code API를 찾는 데 도움이됩니다.

그러면 Hello World 샘플의 코드들을 더 가까이 보며 위 컨셉이 어떻게 적용되었는 지 확인해 봅시다.

## 확장 파일 구조

```js
.
├── .vscode
│   ├── launch.json     // Config for launching and debugging the extension
│   └── tasks.json      // Config for build task that compiles TypeScript
├── .gitignore          // Ignore build output and node_modules
├── README.md           // Readable description of your extension's functionality
├── src
│   └── extension.ts    // Extension source code
├── package.json        // Extension manifest
├── tsconfig.json       // TypeScript configuration

```

설정 파일에 대해 더 자세히 알아보겠습니다 :

* ```launch.json```은 VS Code의 디버깅을 설정하는 데 쓰입니다.

* ```tasks.json```는 VS Code Taks를 정의하는 데 쓰입니다.

* ```tsconfig.json``` Typescript Handbook을 참조하세요. (역주 - Typescript의 컴파일 옵션을 조정하는 파일입니다. )

어쨌든, 한번 `package.json` 파일과 `extensions.ts` 파일로 초점을 옮겨봅시다. 그 두 파일은 Hello World 확장 기능을 이해하는데 필수적이니까요.

# 확장 Menifest

각각의 VS Code 확장 기능들은 ```package.json```를 무조건 가지고 있어야 합니다.  ```package.json```는 Node.js의 필드들인  ```scripts```와 ```dependencies```와 VS Code의 필드들인 ```publisher```, ```activationEvents``` 와 ```contributes```들을 섞은 내용을 가지고 있습니다. Extension Manifest Reference에서 모든 VS Code에 관련된 필드들에 대한 설명을 찾을 수 있습니다. 밑에는 가장 중요한 필드 몇 개를 적어놓았습니다:

* ```name``` 과 ```publisher```: VS Code는 ```<publisher>.<name>```를 확장의 유일한 ID로 사용합니다. 예를 들어, 이 Hello World 예제는  ```vscode-samples.helloworld-sample```라는 ID를 가지고 있습니다. VS Code는 ID를 확장 기능의 특별히 구별되는 구분자로 사용합니다.

* ```main```: 확장 기능의 진입점입니다.

* ```activationEvents``` 과 ```contributes```: Activation Events 과 Contribution Points 입니다.

* ```engines.vscode```: 이 구별자들은 이 확장 기능이 지원하는 VS Code API의 최소 버전입니다.

* ```postinstall``` 스크립트: 만약 VS Code API 1.25 버전을 설치하셨다면 ```engines.vscode```라는 구별자일 것입니다. ```vscode.d.ts```파일이 ```node_modules/vscode/vscode.d.ts```에 다운로드 되었다면, IntelliSense가 활성화되고, 모든 VS CODE API에 대해 정의로 건너뛰기와 에러 체크를 할 수 있습니다.


```json
{
	"name": "helloworld-sample",
	"displayName": "helloworld-sample",
	"description": "HelloWorld example for VS Code",
	"version": "0.0.1",
	"publisher": "vscode-samples",
	"repository": "https://github.com/Microsoft/vscode-extension-samples/helloworld-sample",
	"engines": {
		"vscode": "^1.25.0"
	},
	"categories": ["Other"],
	"activationEvents": ["onCommand:extension.helloWorld"],
	"main": "./out/extension.js",
	"contributes": {
		"commands": [
			{
				"command": "extension.helloWorld",
				"title": "Hello World"
			}
		]
	},
	"scripts": {
		"vscode:prepublish": "npm run compile",
		"compile": "tsc -p ./",
		"watch": "tsc -watch -p ./",
		"postinstall": "node ./node_modules/vscode/bin/install"
	},
	"devDependencies": {
		"@types/node": "^8.10.25",
		"tslint": "^5.11.0",
		"typescript": "^2.6.1",
		"vscode": "^1.1.22"
	}
}
```

# 확장 시작 파일

확장 기능의 진입 파일은 두 함수들을 내보내는데, `activate` 와 `deactivate`입니다. `activate`는 *Activation Event*를 등록했을 때 실행됩니다. `deactivate`는 확장 기능의 실행이 끝나기 전 마지막으로 정리할 기회를 줍니다.

`vscode` 모듈은 `node ./node_modules/vscode/bin/install`에 있는 스크립트를 포함하고 있습니다. 이 스크립트는 `package.json`에 있는 `engines.vscode` 필드에 따라 VS Code API 정의 파일을 가져옵니다. 이 스크립트가 실행된 후엔, IntelliSense가 활성화되고, 정의로 점프할 수 있고, 그 밖에 TypeScript 언어 기능이 추가됩니다.

```ts
// The module 'vscode' contains the VS Code extensibility API
// Import the module and reference it with the alias vscode in your code below
import * as vscode from 'vscode';

// this method is called when your extension is activated
// your extension is activated the very first time the command is executed
export function activate(context: vscode.ExtensionContext) {
	// Use the console to output diagnostic information (console.log) and errors (console.error)
	// This line of code will only be executed once when your extension is activated
	console.log('Congratulations, your extension "helloworld-sample" is now active!');

	// The command has been defined in the package.json file
	// Now provide the implementation of the command with registerCommand
	// The commandId parameter must match the command field in package.json
	let disposable = vscode.commands.registerCommand('extension.helloWorld', () => {
		// The code you place here will be executed every time your command is executed

		// Display a message box to the user
		vscode.window.showInformationMessage('Hello World!');
	});

	context.subscriptions.push(disposable);
}

// this method is called when your extension is deactivated
export function deactivate() {}

```
