# mono-repos-demo

---

title: Config multi-project với monorepos, lerna, yarn
path: /multi-project-monorepos-lerna-yarn
author: 'thongdo'
date: Sun Sep 22 2019 11:33:22 GMT+0700 (Indochina Time)
summary: Config multi-project với monorepos, lerna, yarn
tags: ['configuration']

---

`Mono repos` một repository chứa nhiều hơn một project trong nó. Các project này có thể không liên quan đến nhau, kết hợp một cách lỏng lẻo hoặc kết nối bằng các công cụ quản lý phụ thuộc(`package.json`).

### `1. Tạo thư mục mono folder`

#### `1.1 câu lệnh`

```sh
$ mkdir mono-folder
$ cd mono-folder
$ npx lerna init
```

#### `1.2 kết quả`

##### `1.2.1 folder structure`

```
mono-folder/
  package.json
  lerna.json
  packages/
```

- 1 folder `packages` (chứa các nested project - mỗi project là một package)

##### `1.2.2 lerna.json`

```json
{
  "packages": ["packages/*"],
  "npmClient": "yarn",
  "useWorkspaces": true,
  "version": "0.0.0"
}
```

- `packages`: cấu hình các thư mục nằm ở đâu trong mono-folder
- `npmClient`: cấu hình sử dụng npm / yarn / ...
- `useWorkspaces`: đảm bảo việc quản lý repos sử dụng các gói gì với một quản lý thống nhất: yarn / npm / ...

### `2.Tạo các package con trong packages`

#### `2.1 câu lệnh`

```sh
$ cd packages
$ câu lệnh tạo ra các project(vue create example/ ...)
```

### `3. sử dụng script trong package.json`

#### `3.1 mono-folder - package.json`

```json
  "scripts": {
    "build": "lerna exec --parallel",
    "dev": "lerna run dev",
    "lerna-help": "lerna run dev --help",
    "dev-only-one": "lerna run dev --scope=node-backend"
  },
```

#### `3.2 node-backend - package.json`

```json
  "scripts": {
    "dev": "node index.js"
  },
```

#### `3.2 vue-frontend - package.json`

```json
  "scripts": {
    "dev": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
```

- khi sử dụng `yarn dev` ở `mono-folder` thì sẽ run đồng thời tất cả các project có trong `packages`
  -> gọi được lệnh tương ứng ở các package.json ở các package con ở trong monorepos

```sh
Usage: lerna <command> [options]

Commands:
  lerna add <pkg> [globs..]  Add a single dependency to matched packages
  lerna bootstrap            Link local packages together and install remaining package dependencies
  lerna changed              List local packages that have changed since the last tagged release                                                                                                                  [aliases: updated]
  lerna clean                Remove the node_modules directory from all packages
  lerna create <name> [loc]  Create a new lerna-managed package
  lerna diff [pkgName]       Diff all packages or a single package since the last release
  lerna exec [cmd] [args..]  Execute an arbitrary command in each package
  lerna import <dir>         Import a package into the monorepo with commit history
  lerna init                 Create a new Lerna repo or upgrade an existing repo to the current version of Lerna.
  lerna link                 Symlink together all packages that are dependencies of each other
  lerna list                 List local packages                                                                                                                                                               [aliases: ls, la, ll]
  lerna publish [bump]       Publish packages in the current project.
  lerna run <script>         Run an npm script in each package that contains that script
  lerna version [bump]       Bump version of packages changed since the last release.

Global Options:
  --loglevel       What level of logs to report.                                                                                                                                                            [string] [default: info]
  --concurrency    How many processes to use when lerna parallelizes tasks.                                                                                                                                    [number] [default: 8]
  --reject-cycles  Fail if a cycle is detected among dependencies.                                                                                                                                                         [boolean]
  --no-progress    Disable progress bars. (Always off in CI)                                                                                                                                                               [boolean]
  --no-sort        Do not sort packages topologically (dependencies before dependents).                                                                                                                                    [boolean]
  --max-buffer     Set max-buffer (in bytes) for subcommand execution                                                                                                                                                       [number]
  -h, --help       Show help                                                                                                                                                                                               [boolean]
  -v, --version    Show version number                                                                                                                                                                                     [boolean]

When a command fails, all logs are written to lerna-debug.log in the current working directory.
```

### `4 Tham khảo`
