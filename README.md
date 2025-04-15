## 1. Инициализируем Vite проект

В директории, где будет находится папка с будущим проектом, открываем терминал и вводим следующую команду:
```bash
npm create vite@latest
```
Вводим название проекта (нижний регистр, тире вместо пробелов), далее выбираем Vanilla, далее выбираем TypeScript

После этого создаётся проект. Далее переходим в папку с созданным проектом, вводим `npm install`.

Удаляем всё в папке `public`, в `src` удаляем всё, кроме `main.ts` (удаляем всё содержимое), `style.css` (тоже всё удаляем в нём) и `vite-env.d.ts` (в нём ничего не удаляем, но меняем название файла на `vite-environment.d.ts`).

Также в `index.html` вставляем этот шаблон (не забудь поменять title на нужный):
```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <script type="module" src="./src/main.ts"></script>
  </body>
</html>

```

В `package.json` удали `private: true`.

## 2. Обновляем tsconfig

Вставляем в `tsconfig.json` следующее содержимое:

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "useDefineForClassFields": true,
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "skipLibCheck": true,

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "isolatedModules": true,
    "moduleDetection": "force",
    "noEmit": true,

    /* Linting */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "noUncheckedSideEffectImports": true,

    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "rootDir": "./src"
  },
  "include": ["src"]
}

```

## 3. Устанавливаем vite-plugin-checker

Устанавливаем [vite-plugin-checker](https://vite-plugin-checker.netlify.app/introduction/getting-started.html), чтобы отслеживать ошибки кода при пересборке проекта

```bash
npm i vite-plugin-checker -D
```

Далее создаём файл `vite.config.ts` в корне проекта и вставляем в него следующее содержимое:
```typescript
import checker from 'vite-plugin-checker';

export default {
  plugins: [
    checker({
      typescript: true,
    }),
  ],
};

```

## 4. Устанавливаем Prettier

Устанавливаем [Prettier](https://prettier.io/docs/install):

```bash
npm install --save-dev --save-exact prettier
```

> [!NOTE]
> Флаг `--save-exact` нужен для того, чтобы зафиксировать конкретную версию

Далее вводим команду:
```bash
node --eval "fs.writeFileSync('.prettierrc','{}\n')"
```

Она создаёт файл конфигурации `.prettierrc`. В него вставляем следующее содержимое:
```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "useTabs": false,
  "trailingComma": "all",
  "printWidth": 120,
  "endOfLine": "auto"
}

```

Далее вводим следующую команду:
```bash
node --eval "fs.writeFileSync('.prettierignore','# Ignore artifacts:\nbuild\ncoverage\n')"
```

Она создаст файл `.prettierignore`. В него вставляем следующее содержимое:
```
# Ignore artifacts:
build
coverage

/dist
node_modules

```

Далее добавляем в `package.json` скрипты типа:
```json
"format": "prettier . --write",     // поиск и автоматическое исправление ошибок
"ci:format": "prettier . --check"   // поиск ошибок без исправления
```

## 5. Устанавливаем Eslint

Устанавливаем [typescript-eslint](https://typescript-eslint.io/getting-started/):
```bash
npm install --save-dev eslint @eslint/js typescript-eslint
```

Создаём конфиг `eslint.config.mjs` в корне проекта и добавляем следующее содержимое:
```javascript
import eslint from '@eslint/js';
import tseslint from 'typescript-eslint';

export default tseslint.config(
  eslint.configs.recommended,
  tseslint.configs.recommended,
);

```

Устанавливаем [eslint-plugin-prettier и eslint-config-prettier](https://www.npmjs.com/package/eslint-plugin-prettier):
```bash
npm install --save-dev eslint-plugin-prettier eslint-config-prettier
```

Обновляем конфиг eslint:
```javascript
import eslint from '@eslint/js';
import tseslint from 'typescript-eslint';
import eslintConfigPrettier from 'eslint-config-prettier/flat'; // новая строка
import eslintPluginPrettierRecommended from 'eslint-plugin-prettier/recommended'; // новая строка

export default tseslint.config(
  eslint.configs.recommended,
  tseslint.configs.recommended,
  eslintConfigPrettier, // новая строка
  eslintPluginPrettierRecommended, // новая строка
);

```

Устанавливаем [eslint-plugin-unicorn](https://www.npmjs.com/package/eslint-plugin-unicorn):
```bash
npm install --save-dev eslint-plugin-unicorn
```

И обновляем конфиг Eslint:
```javascript
import eslint from '@eslint/js';
import tseslint from 'typescript-eslint';
import eslintConfigPrettier from 'eslint-config-prettier/flat';
import eslintPluginPrettierRecommended from 'eslint-plugin-prettier/recommended';
import eslintPluginUnicorn from 'eslint-plugin-unicorn'; // новая строка

export default tseslint.config(
  eslint.configs.recommended,
  tseslint.configs.recommended,
  eslintConfigPrettier,
  eslintPluginPrettierRecommended,
  eslintPluginUnicorn.configs.recommended, // новая строка

  {                                        // новая строка
    rules: {                               // новая строка
      'unicorn/better-regex': 'warn',      // новая строка
    },                                     // новая строка
  },                                       // новая строка
);

```

Добавляем Eslint в checker в `vite.config.ts`:
```typescript
import checker from 'vite-plugin-checker';

export default {
  plugins: [
    checker({
      typescript: true,
      eslint: {                                     // новая строка
        lintCommand: 'eslint "./src/**/*.{ts,js}"', // новая строка
        useFlatConfig: true,                        // новая строка
      },                                            // новая строка
    }),
  ],
};

```

Добавляем скрипты в `package.json`:
```json
"lint": "eslint",
"lint:fix": "eslint --fix"
```

## 6. Устанавливаем Husky, lint-staged, commitlint

Устанавливаем [husky](https://typicode.github.io/husky/get-started.html):
```bash
npm install --save-dev husky
```

Запускаем команду для первичной настройки husky а проекте:
```bash
npx husky init
```
Эта команда добавлет папку `.husky` в корень проекта. В папке содержаться файлы хуков. При инициализации автоматически создаётся хук `pre-commit`. Также после выплнения этой команды в `package.json` добавляется скрипт `"prepare": "husky"`.

Далее установим [lint-staged](https://github.com/lint-staged/lint-staged):
```bash
npm install --save-dev lint-staged
```

Добавляем настройки lint-staged в `package.json`. В корне конфига помещаем следующий код (например, между `"scripts"` и `"devDependencies"`):
```json
"lint-staged": {
  "*": "prettier --write",
  "*.ts": "eslint --fix"
},
```

Далее в файле хука `pre-commit` меняем содержимое на следующее:
```
npx lint-staged

```

Устанавливаем [commitlint](https://commitlint.js.org/guides/getting-started.html):
```bash
npm install --save-dev @commitlint/config-conventional @commitlint/cli
```

Создаём конфиг путём запуска следующей команды:
```bash
echo '{ "extends": ["@commitlint/config-conventional"] }' > .commitlintrc.json
```

> [!WARNING]
> Проверь кодировку кофига. Если она не будет UTF-8, то там могут быть скрытые символы, на которые будет ругаться Prettier. Поменять кодировку можно как в VS Code (справа снизу на панели), так и через NotePad++.

Создаём хук `commit-msg`:
```bash
echo 'npx --no commitlint --edit $1' > .husky/commit-msg
```
-----------------------------------
Также можно добавить stylelint (очень полезная штука, возможно стоит ещё глянуть плагин, чтобы она с prettier не конфликтовала).
