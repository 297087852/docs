## ESlint

`ESLint`是一个语法规则和代码风格的检查工具,可以用来保证写出语法正确、风格统一的代码。

官网: https://eslint.org/

```sh
  npm install eslint -D
```

eslint 常用命令(查看[全部](https://eslint.org/docs/user-guide/command-line-interface#options))

```sh
  eslint [options] [file|dir|glob]*
```

- `--init`: Run config initialization wizard
- `--fix`: Automatically fix problems
- `--ext`: Specify JavaScript file extensions
- `--cache`: Only check changed files
- `--h`: Show help

[配置文件](https://eslint.org/docs/user-guide/configuring/configuration-files#configuration-file-formats)的格式(优先级从上到下)

- `.eslintrc.js`
- `.eslintrc.cjs`
- `.eslintrc.yaml`
- `.eslintrc.yml`
- `.eslintrc.json`
- `eslintConfig` in `package.json`

行内注释

```js
// eslint-disable-next-line no-alert, no-console
/* eslint-disable no-alert, no-console */
/* eslint-enable */
/* global var1:writable, var2:writable */
```

```js
{
  "root": true,
  "env": {
      "browser": true,
      "node": true,
      "es2021": true,
  },
  "extends": [
    "eslint:recommended",
    "airbnb",
    "plugin:prettier/recommended",
  ],
  "plugins": [
    "prettier"
  ],
  "parser": "espree", // esprima
  "parserOptions": {
    "ecmaVersion": 6,
    "sourceType": "module",
    "ecmaFeatures": {
      // "globalReturn": true
      // "impliedStrict": true,
      "jsx": true,
    }
  },
  "globals": {
    "var1": "writable",
    "var2": "readonly",
  },
  "rules": {
    "no-console": "off",
  }
  "settings": {
    "sharedData": "Hello"
  },
  // "noInlineConfig": ture,
  "overrides": [
    {
      "files": ["bin/*.js", "lib/*.js"],
      "excludedFiles": "*.test.js",
      "rules": {
        "quotes": ["error", "single"]
      }
    }
  ]
}
```

- [root](https://eslint.org/docs/user-guide/configuring/configuration-files#cascading-and-hierarchy) 根目录标识, 标识当前配置文件为最底层的文件，无需往更上一级的文件目录中进行搜索

- [env](https://eslint.org/docs/user-guide/configuring/language-options#specifying-environments) 用于指定环境，每个环境都有自己预定义的全局变量，可以同时指定多个环境，不矛盾。如 console 属性只有在 browser 环境下才会存在，如果不设置支持 browser，那么可能会 console is undefined

  ```js
    env: {
      browser: true, // 浏览器全局变量。
      node: true, // Node.js全局变量和Node.js范围。
      commonjs: true, // CommonJS全局变量和CommonJS范围（使用Browserify / WebPack的浏览器代码）。
      es6: true, // 启用除模块外的所有ECMAScript 6功能（这会自动将ecmaVersion解析器选项设置为6）。
      es2021: true, // 启用除模块外的所有ECMAScript 2021功能（这会自动将ecmaVersion解析器选项设置为12）。
    },
  ```

- [extends](https://eslint.org/docs/user-guide/configuring/configuration-files#extending-configuration-files) 可以是一个字符串或字符串数组，数组中每个配置项继承它前面的配置，可以对继承的规则进行修改、覆盖和拓展

  ```js
    "extends": [
      "eslint:recommended",
      "airbnb",
      "plugin:prettier/recommended",
    ]
  ```

- [plugins](https://eslint.org/docs/user-guide/configuring/plugins#configuring-plugins) 新增校验规则

  ```js
    plugins: ['prettier'],
  ```

- [parser](https://eslint.org/docs/user-guide/configuring/plugins#specifying-parser) 指定一个不同的解析器, `ESLint`默认使用`Espree`作为其解析器, 以下解析器与 ESLint 兼容

  - `Esprima`

  - `@babel/eslint-parse` 对 Babel 解析器的包装，使其能够与 ESLint 兼容(`babel-eslint`不再更新维护)

  - `typescript-eslint-parser` 把 TypeScript 转换为 ESTree 兼容格式的解析器，这样它就可以在 ESLint 中使用了。这样做的目的是通过 ESLint 来解析 TypeScript 文件（尽管不一定必须通过所有的 ESLint 规则）

- [parserOptions](https://eslint.org/docs/user-guide/configuring/language-options#specifying-parser-options) 指定解析器要想使用的环境配置参数

  ```js
    parserOptions: {
      ecmaVersion: 2021,  // 默认是5
      sourceType: 'module',  // 默认'script'，但一般使用ECMAScript模块形式''module
      ecmafeatures: {
        globalReturn: false,  //允许在全局作用域下使用return语句
        impliedStrict: false,  //启用全局strict模式（严格模式）
        jsx: false,  //启用JSX
        experimentalObjectRestSpread: false  //启用对实验性的objectRest/spreadProperties的支持
      }
    }
  ```

- [processor](https://eslint.org/docs/user-guide/configuring/plugins#specifying-processor) 设置处理器, 处理器可以从其他文件中提取 JavaScript 代码, 然后让 ESLint lint 处理 JavaScript 代码, 或者处理器可以在预处理中转换 JavaScript 代码

- [globals](https://eslint.org/docs/user-guide/configuring/language-options#specifying-globals) 设置全局变量

- [rules](https://eslint.org/docs/user-guide/configuring/rules#configuring-rules) 设置规则提示等级

  “off” 或 0: 关闭规则

  “warn” 或 1: 开启规则，使用警告级别的错误：warn (不会导致程序退出)

  “error” 或 2: 开启规则，使用错误级别的错误：error (当被触发的时候，程序会退出)

  #### ESLint 的规则不仅只有关闭和开启这么简单，每一条规则还有自己的配置项。如果需要对某个规则进行配置，就需要使用数组形式的参数

  ```js
  {
      "rules": {
        // 使用数组形式，对规则进行配置
        // 第一个参数为是否启用规则
        // 后面的参数才是规则的配置项
        "quotes": [
          "error",
          "single",
          {
            "avoidEscape": true // 允许字符串中使用单引号或者双引号
          }
        ]
      }
    }
  ```

- [settings](https://eslint.org/docs/user-guide/configuring/configuration-files#adding-shared-settings) 提供给具体规则项，每个参数值，每个规则项都会被注入该变量

- [noInlineConfig](https://eslint.org/docs/user-guide/configuring/rules#disabling-inline-comments) 是否允许行内注释

- [overrides](https://eslint.org/docs/user-guide/configuring/configuration-files#configuration-based-on-glob-patterns) 对特定文件进行特定的 eslint 检测

#### [Ignoring Code](https://eslint.org/docs/user-guide/configuring/ignoring-code)忽略掉不被 lint 的文件

```js
  // .eslintrc
  {
      "ignorePatterns": ["temp.js", "**/vendor/*.js"],
      "rules": {
          //...
      }
  }
```

或

```makefile
  # .eslintignore
  /dist/
```

#### 社区流行的三种风格指南

- [Airbnb](https://github.com/airbnb/javascript)(324)

- [Standard](https://github.com/standard/eslint-config-standard)(160)

- [Google](https://github.com/google/eslint-config-google)(65)

查询 npm 包 `peerDependencies`

```sh
  npm info [repo_name] peerDependencies
```

```json
  // eslint-config-airbnb-base
  "peerDependencies": {
    "eslint": "^5.16.0 || ^6.8.0 || ^7.2.0",
    "eslint-plugin-import": "^2.22.1"
  }
```

```json
  // eslint-config-standard
  "peerDependencies": {
    "eslint": "^7.12.1",
    "eslint-plugin-import": "^2.22.1",
    "eslint-plugin-node": "^11.1.0",
    "eslint-plugin-promise": "^4.2.1"
  }
```

```json
  // eslint-config-google
  "peerDependencies": {
      "eslint": ">=5.16.0"
  }
```

## prettier

官网: https://prettier.io

安装:

```sh
  npm install prettier -D
```

```sh
  npx prettier --write
```

[配置文件](https://prettier.io/docs/en/configuration.html)

- `prettier` in `package.json`
- `.prettierrc.json`、`.prettierrc.yarm`
- `.prettierrc.js`、`.prettierrc.cjs`, `prettier.config.js`、`prettier.config.cjs`
- `.prettierrc.toml`

```js
// prettier.config.js
module.exports = {
  trailingComma: 'es5',
  tabWidth: 4,
  semi: false,
  singleQuote: true
}
```

#### 禁用文件或者代码块

```js
  // prettier-ignore
```

或

```makefile
  # .prettierignore
  dist
```

#### 与 eslint 搭配使用

[eslint-config-prettier](https://github.com/prettier/eslint-config-prettier)

[eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier)

## husky

官网: https://typicode.github.io/husky/#/

```sh
  npx husky-init && npm install
```

- 安装`husky`为开发依赖
- 在项目根目录创建`.husky`
- 在.husky 创建`pre-commit` hook && 初始化 `pre-commit` 命令为 `npm test`
- 在`package.json`的`scripts`中，增加脚本`"prepare": "husky install"`
- 在`.git`的`config`中，增加 `hooksPath = .husky`(git 版本需要>=2.9)

`prepare` 为 npm 自带`Life Cycle Scripts`, 可以在 npm install(不带参数)之后自动执行(见 [npm 文档](https://docs.npmjs.com/cli/v7/using-npm/scripts#life-cycle-scripts))

`git hooksPath`设置`hooks`脚本目录的路径

当我们在`.husky`配置好相关`hooks`后提交到代码库，其他成员拉取项目并执行`npm install`时，触发`prepare`执行`husky install`后，更改`.git`文件夹`config`中 `hooksPath = .husky`
保持项目设置同步

#### 新增 git hooks

```sh
  npx husky add .husky/commit-msg "npm run commitlint"
```

#### 跳过校验

```
  git add . && git commit --no-verify -m "代码规范强制提交测试"
```

## lint-staged

`lint-staged` 是一个在`git`暂存文件上运行 linters 的工具

官网: http://github.com/okonet/lint-staged

```sh
  npx mrm lint-staged
```

[mrm](https://github.com/sapegin/mrm) 是一个可以自动化项目配置的库

- 安装`lint-staged` `husky`为开发依赖
- 在`.husky`创建`pre-commit` hook && 初始化 `pre-commit` 命令为 `npx lint-staged`
- 检测 package.json 中的依赖项，如开发依赖中包含`eslint`、`prettier && !eslint-plugin-prettier`、`stylelint`, 将会自动配置在`package.json` 的`lint-staged`中

```json
// package.json
{
  "lint-staged": {
    "*.js": "eslint --fix",
    "*.css": "stylelint --fix"
  }
}
```

## stylelint

官网: https://stylelint.io/

```sh
  npm install stylelint stylelint-config-standard -D
```

#### 常用插件

- [stylelint-scss](https://github.com/kristerkari/stylelint-scss)
- [stylelint-order](https://github.com/hudochenkov/stylelint-order)
- [stylelint-config-standard](https://github.com/stylelint/stylelint-config-standard)
- [stylelint-config-recess-order](https://github.com/stylelint/stylelint-config-standard)

#### stylelint.config.js 配置

```js
  processors: ["stylelint-my-processor"],
  plugins: ["stylelint-order"],
  extends: "stylelint-config-standard",
  defaultSeverity: "warning",
  rules: {
    "at-rule-empty-line-before": "always"|"never",
    "at-rule-name-case": "lower"|"upper",
    "block-no-empty": true,
    "at-rule-no-unknown": [ true, {
      ignoreAtRules: ['extend', 'at-root', 'debug', 'warn', 'error', 'if', 'else', 'for', 'each', 'while', 'mixin', 'include', 'content', 'return', 'function']
    }],
    "selector-class-pattern": /^[a-z]+[a-z0-9]*(-[a-z0-9]+)*$/
  },
  ignoreFiles:  ["test/*.css"]
```

#### 禁用文件或者代码块

```js
/* stylelint-disable */
/* stylelint-disable-next-line block-no-empty */
```

或

```makefile
  # .stylelintignore
  /test/*.scss
```

## 提交规范

目前，社区有多种 Commit message 的[写法规范](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages)，[Angular 规范](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#type)目前是使用最广的写法，比较合理和系统化，并且有配套的工具:

Commit message 都包括三个部分：Header, Body 和 Footer

```
  <Header>
  <BLANK LINE>
  <Body>
  <BLANK LINE>
  <Footer>
```

其中, Header 是必需的, Body 和 Footer 可以省略。

不管是哪一个部分，任何一行都不得超过 72 个字符（或 100 个字符）。这是为了避免自动换行影响美观

### Header 部分只有一行，包括三个字段：type（必需）、scope（可选）和 subject（必需）

```
  <type>(<scope>): <short summary>
  │       │             │
  │       │             └─⫸ Summary in present tense. Not capitalized. No period at the end.
  │       │
  │       └─⫸ Commit Scope: animations|bazel|benchpress|common|compiler|compiler-cli|core|
  │                          elements|forms|http|language-service|localize|platform-browser|
  │                          platform-browser-dynamic|platform-server|router|service-worker|
  │                          upgrade|zone.js|packaging|changelog|dev-infra|docs-infra|migrations|
  │                          ngcc|ve
  │
  └─⫸ Commit Type: build|ci|docs|feat|fix|perf|refactor|test

```

其中 `<type>` 和 `<summary>` 是必须的 `<scope>` 是可选的

#### Type

  - build: 影响构建系统或外部依赖项的更改(gulp, broccoli, npm)
  - ci: 对CI配置文件和脚本的更改(Circle, BrowserStack, SauceLabs)
  - docs: 文档变更
  - feat: 新功能（feature）
  - fix: 修补 bug
  - perf: 优化, 提高性能相关
  - refactor: 重构（即不是新增功能，也不是修改 bug 的代码变动）
  - test: 增加测试
  - revert: 代码版本回退

#### Scope

  用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同

#### Summary

  - 以动词开头，使用第一人称现在时，比如change，而不是changed或changes
  - 第一个字母小写
  - 结尾不加句号（.）

### Body 部分是对本次 commit 的详细描述，可以分成多行

```console
  More detailed explanatory text, if necessary.  Wrap it to about 72 characters or so.

  Further paragraphs come after blank lines.

  - Bullet points are okay, too
  - Use a hanging indent
```

### Footer 部分只用于两种情况。

- 不兼容变动

  如果当前代码与上一个版本不兼容，则 Footer 部分以 BREAKING CHANGE 开头，后面是对变动的描述、以及变动理由和迁移方法。

  ```console
    BREAKING CHANGE: isolate scope bindings definition has changed.

        To migrate the code follow the example below:

        Before:

        scope: {
          myAttr: 'attribute',
        }

        After:

        scope: {
          myAttr: '@',
        }

        The removed `inject` wasn't generaly useful for directives so there should be no code using it.
  ```

- 关闭 Issue

  ```console
    Closes #666, #667
  ```
#### Revert

还有一种特殊情况，如果当前 commit 用于撤销以前的 commit，则必须以revert:开头，后面跟着被撤销 Commit 的 Header

```
  revert: feat(pencil): add 'graphiteWidth' option

  This reverts commit 667ecc1654a317a13331b17617d973392f415f02.
```
## commitlint

官网: https://commitlint.js.org/#/

```sh
  npm install  @commitlint/cli @commitlint/config-conventional -D
```

#### commitlint.config.js 文件配置

```js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  parserPreset: 'conventional-changelog-atom',
  formatter: '@commitlint/format',
  rules: {
    'type-enum': [
      2,
      'always',
      [
        'feat',
        'fix',
        'docs',
        'style',
        'refactor',
        'test',
        'revert',
        'config',
        'chore'
      ]
    ],
    'type-empty': [2, 'never'],
    'subject-empty': [2, 'never'],
    'subject-full-stop': [0, 'never'],
    'subject-case': [0, 'never']
  },
  ignores: [commit => commit === ''],
  defaultIgnores: true,
  prompt: {
    messages: {},
    questions: {
      type: {
        description: 'please input type:'
      }
    }
  }
}
```

## editorconfig

`EditorConfig`帮助开发人员在不同的编辑器和 IDE 之间定义和维护一致的编码样式。EditorConfig 项目由用于定义编码样式的文件格式和一组文本编辑器插件组成，这些插件使编辑器能够读取文件格式并遵循定义的样式。EditorConfig 文件易于阅读，并且与版本控制系统配合使用

官网: https://editorconfig.org/

```makefile
  # .editorconfig
  root = true

  [*]
  charset = utf-8
  indent_style = tab
  indent_size = 2
  end_of_line = lf
  insert_final_newline = true
  trim_trailing_whitespace = true

  [*.{md,css,yml}]
  indent_style = space
  indent_size = 2

  [package.json]
  indent_style = space
  indent_size = 2
```

## jsconfig.json

目录中存在 `jsconfig.json` 文件表示该目录是 `JavaScript` 项目的根目录。`jsconfig.json` 文件指定根文件和 `JavaScript` 语言服务提供的功能选项。

`jsconfig.json`源于`tsconfig.json`，是`TypeScript`的配置文件。`jsconfig.json`相当于`tsconfig.json`的`allowJs`属性设置为`true`。

```json
// jsconfig.json
{
  "compilerOptions": {
    "baseUrl": "./",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "exclude": ["node_modules", "dist"],
  "include": ["src/**/*"]
}
```
