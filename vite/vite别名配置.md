在 TypeScript 项目中配置别名可以通过 `tsconfig.json` 文件中的 `paths` 选项来实现。具体步骤如下：

1.  在项目根目录下的 `tsconfig.json` 文件中添加以下代码：

```json
// tsconfig.json
{
  "compilerOptions": {
    // 其他配置项
    "baseUrl": "./",
    "paths": {
      // 这里添加你需要配置的别名
      "@/*": ["src/*"],
      "components/*": ["src/components/*"],
      "utils/*": ["src/utils/*"]
    }
  },
  "include": [
    "src/**/*"
  ]
}
```

2. 在项目根目录下的vite.config.ts文件中添加以下代码:

```json
//vite.config.ts
import { defineConfig } from "vite";

import vue from "@vitejs/plugin-vue";

// https://vitejs.dev/config/

export default defineConfig({

  plugins: [vue()],

  resolve: {

    alias: {

      "@": "/src",

    },

  },

});
```


3.  在 `paths` 对象中添加需要配置的别名，例如上面的代码中添加了三个别名：`@/*`、`components/*`、`utils/*`。其中 `@/*` 表示项目根目录，`src/*` 表示项目中的 `src` 目录。其他别名的配置方法类似。

4.  配置完成后，即可在项目中使用别名来引入模块，例如：

```js
import App from '@/App.vue'
import { formatDate } from 'utils/date-utils'
import Header from 'components/Header.vue'
```

以上代码分别使用了三个配置的别名，分别指向了 `/src/App.vue` 模块、`/src/utils/date-utils.ts` 模块和 `/src/components/Header.vue` 模块。 需要注意的是，在使用别名时需要确保 `tsconfig.json` 文件中的 `include` 选项包含了对应的文件路径，否则 TypeScript 编译器无法找到对应的模块。上面的例子中，我们将 `src` 目录下的所有文件都包含在了编译范围内。