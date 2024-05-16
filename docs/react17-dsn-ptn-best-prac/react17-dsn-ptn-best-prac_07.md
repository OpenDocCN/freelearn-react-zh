# 通过一个真实项目了解 GraphQL

**GraphQL**是用于 API 的查询语言，可以帮助它们与您现有的数据进行交互。它提供了 API 中数据的完整描述，您只能请求确切需要的数据，而不会多余。它还使得改进 API 变得更容易，并且具有非常强大的开发人员工具。

在本章中，我们将学习如何在一个真实项目中使用 GraphQL，通过创建一个基本的登录和用户注册系统。

本章将涵盖以下主题：

+   安装 PostgreSQL

+   使用`.env`文件创建环境变量

+   配置 Apollo Server

+   定义 GraphQL 查询和变更

+   与解析器一起工作

+   创建 Sequelize 模型

+   实施 JWT

+   使用 GraphQL Playground

+   执行身份验证

# 第八章：技术要求

要完成本章，您将需要以下内容：

+   Node.js 12+

+   Visual Studio Code

+   PostgreSQL

+   Homebrew ([`brew.sh`](https://brew.sh))

+   pgAdmin 4 ([`www.pgadmin.org/download/`](https://www.pgadmin.org/download/))

+   OmniDB ([`omnidb.org`](https://omnidb.org))

您可以在本书的 GitHub 存储库中找到本章的代码：[`github.com/PacktPublishing/React-17-Design-Patterns-and-Best-Practices-Third-Edition/tree/main/Chapter05`](https://github.com/PacktPublishing/React-17-Design-Patterns-and-Best-Practices-Third-Edition/tree/main/Chapter05)。

# 安装 PostgreSQL

在本示例中，我们将使用 PostgreSQL 数据库，因此您需要安装 PostgreSQL 才能在您的机器上运行此项目。

如果您有一台 macOS 机器，安装 PostgreSQL 的最简单方法是使用 Homebrew。您只需要运行以下命令：

```jsx
brew install postgres
```

安装完成后，您需要运行以下命令：

```jsx
ln -sfv /usr/local/opt/postgresql/*.plist ~/Library/LaunchAgents
```

然后，您可以创建两个新的别名来启动和停止您的 PostgreSQL 服务器：

```jsx
alias pg_start="launchctl load ~/Library/LaunchAgents"
alias pg_stop="launchctl unload ~/Library/LaunchAgents"
```

现在，您应该能够使用`pg_start`启动您的 PostgreSQL 服务器，或者使用`pg_stop`停止它。

之后，您需要创建您的第一个数据库，就像这样：

```jsx
createdb `whoami`
```

现在，您可以使用`psql`命令连接到 PostgreSQL。

如果您收到“角色"postgresql"不存在”的错误，请通过运行以下命令来修复它：

```jsx
createuser -s postgres
```

如果您一切都做对了，您应该能看到类似于这样的东西：

如果你使用 Windows，你可以在[`www.postgresql.org/download/windows/`](https://www.postgresql.org/download/windows/)下载 PostgreSQL，而对于使用 Linux（Ubuntu）的用户，你可以从[`www.postgresql.org/download/linux/ubuntu/`](https://www.postgresql.org/download/linux/ubuntu/)下载。

## PostgreSQL 数据库管理的最佳工具

PostgreSQL 数据库管理中最好的工具之一是**pgAdmin 4**（[`www.pgadmin.org/download/`](https://www.pgadmin.org/download/)）。我喜欢这个工具，因为它可以用来创建新的服务器、用户和数据库。我喜欢使用的另一个工具是**OmniDB**（[`omnidb.org`](https://omnidb.org)）。我强烈建议你安装这两个工具。

记得创建一个数据库以便在这个示例中使用。

有时，当你启动你的 PostgreSQL 服务器时，你可能会遇到一个错误，它可能会说

`FATAL: lock file "postmaster.pid" already exists`.

如果你遇到这个错误，你可以通过运行`rm /usr/local/var/postgres/postmaster.pid`命令来轻松修复它。然后，你就可以启动你的 PostgreSQL 服务器了。

# 创建我们的.env 文件和配置文件

首先，你需要在你的 GraphQL 项目中创建一个后端目录（`graphql/backend`），之后让我们来审查你需要安装的大量 NPM 包的列表（最相关的）。

```jsx
npm init --yes

npm install @contentpi/lib @graphql-tools/load-files @graphql-tools/merge apollo-server dotenv express jsonwebtoken pg pg-hstore sequelize ts-node

npm install --save-dev husky jest prettier sequelize-mock ts-jest ts-node-dev typescript eslint @types/jsonwebtoken
```

你的`package.json`文件中应该有以下脚本：

```jsx
"scripts": {
  "dev": "ts-node-dev src/index.ts",
  "start": "ts-node dist/index.js",
  "build": "tsc -p .",
  "lint": "eslint . --ext .js,.tsx,.ts",
  "lint:fix": "eslint . --fix --ext .js,.tsx,.ts",
  "test": "jest src"
}
```

在下一节中，我们将配置我们的环境变量。

## 配置我们的.env 文件

`.env`文件（也称为`dotenv`）是一个配置文件，用于指定应用程序的环境变量。通常情况下，你的应用程序不会从开发、暂存或生产环境中改变，但它们通常需要不同的配置：最常见的变量更改是基本 URL、API URL，甚至是你的 API 密钥。

在我们开始实际的登录代码之前，我们需要创建一个名为`.env`的文件（通常，这个文件被`.gitignore`忽略），这将允许我们使用私人数据，比如数据库连接和安全秘钥。存储库中已经存在一个名为`.env.example`的文件；你只需要将其重命名并将你的连接数据放入其中。这将看起来像这样：

```jsx
DB_DIALECT=postgres
DB_PORT=5432
DB_HOST=localhost
DB_DATABASE=<your-database>
DB_USERNAME=<your-username>
DB_PASSWORD=<your-password>
```

## 创建一个基本的配置文件

对于这个项目，我们需要创建一个配置文件，应该创建在`/backend/config/config.json`。在这里，我们将定义一些基本配置，比如我们服务器的端口和一些安全信息：

```jsx
{
  "server": {
    "port": 5000
  },
  "security": {
    "secretKey": "C0nt3ntP1",
    "expiresIn": "7d"
  }
}
```

然后，您需要创建一个`index.ts`文件。这将使用`dotenv`包将我们在`.env`文件中定义的所有数据库连接信息导入，并导出三个配置变量，称为`$db`，`$security`和`$server`：

```jsx
// Dependencies
import dotenv from 'dotenv'

// Configuration
import config from './config.json'

// Loading .env vars
dotenv.config()

// Types
type Db = {
  dialect: string
  host: string
  port: string
  database: string
  username: string
  password: string
}

type Security = {
  secretKey: string
  expiresIn: string
}

type Server = {
  port: number
}

// Extracting data from .env file
const {
  DB_DIALECT = '',
  DB_PORT = '',
  DB_HOST = '',
  DB_DATABASE = '',
  DB_USERNAME = '',
  DB_PASSWORD = '',
} = process.env

const db: Db = {
  dialect: DB_DIALECT,
  port: DB_PORT,
  host: DB_HOST,
  database: DB_DATABASE,
  username: DB_USERNAME,
  password: DB_PASSWORD
}

// Configuration
const { security, server } = config

export const $db: Db = db
export const $security: Security = security
export const $server: Server = server
```

如果您的`.env`文件不在根目录中或不存在，那么所有变量都将是`undefined`。

# 配置 Apollo Server

Apollo Server 是最流行的开源库，可以与 GraphQL（服务器和客户端）一起使用。它有很多文档，非常容易实现。

以下图解释了 Apollo Server 在客户端和服务器中的工作原理：

![](img/f53621c2-5f75-4fa9-b8fd-a38cee633d22.png)

我们将使用 Express 来设置我们的 Apollo Server 和 Sequelize ORM 来处理我们的 PostgreSQL 数据库。因此，最初，我们需要进行一些导入。所需的文件可以在`/backend/src/index.ts`找到：

```jsx
// Dependencies
import { ApolloServer, makeExecutableSchema } from 'apollo-server'

// Models
import models from './models'

// Type Definitions & Resolvers
import resolvers from './graphql/resolvers'
import typeDefs from './graphql/types'

// Configuration
import { $server } from '../config'
```

首先，我们需要使用`makeExecutableSchema`创建我们的模式，通过传递`typeDefs`和`resolvers`：

```jsx
// Schema
const schema = makeExecutableSchema({
  typeDefs,
  resolvers
})
```

然后，我们需要创建一个`ApolloServer`的实例，在这里我们需要传递模式和上下文中的模型：

```jsx
// Apollo Server
const apolloServer = new ApolloServer({
  schema,
  context: {
    models
  }
})
```

最后，我们需要同步 Sequelize。在这里，我们传递了一些可选变量（`alter`和`force`）。如果`force`为`true`并且更改了 Sequelize 模型，**这将删除您的表，包括它们的值**，并强制您重新创建表，而如果`force`为`false`并且`alter`为`true`，**那么您只会更新表字段，而不会影响您的值**。因此，您需要小心使用此选项，因为您可能会意外丢失所有数据。然后，在同步之后，我们必须运行我们的 Apollo Server，它正在监听端口`5000`（`$server.port`）：

```jsx
const alter = true
const force = false

models.sequelize.sync({ alter, force }).then(() => {
  apolloServer
    .listen($server.port)
    .then(({ url }) => {
      // eslint-disable-next-line no-console
      console.log(`Running on ${url}`)
    })
})
```

这将帮助我们将数据库与我们的模型同步，以便每当我们对模型进行更改时，表都将被更新。

# 定义我们的 GraphQL 类型，查询和变异

现在您已经创建了 Apollo Server 实例，您需要创建您的 GraphQL 类型。在这种情况下，我们将为用户创建一些类型，查询和变异。

您需要做的第一件事是在`/backend/src/graphql/types/Scalar.graphql`中定义您的标量类型：

```jsx
scalar UUID
scalar Datetime
scalar JSON
```

现在，让我们创建我们的`User.graphql`文件，其中包含我们的初始`User`类型：

```jsx
type User {
  id: UUID!
  username: String!
  password: String!
  email: String!
  privilege: String!
  active: Boolean!
  createdAt: Datetime!
  updatedAt: Datetime!
}
```

如您所见，我们正在使用一些标量类型，如`UUID`和`Datetime`，来定义我们`User`类型中的一些字段。在这种情况下，当您在 GraphQL 中定义类型时，您需要使用`type`关键字，后跟类型的大写名称。然后，您可以在大括号`{}`中定义您的字段。

在 GraphQL 中有一些原始数据类型，如`String`、`Boolean`、`Float`和`Int`。您可以像我们使用`UUID`、`Datetime`和`JSON`一样定义自定义标量类型，还可以定义自定义类型，如`User`类型，并指定我们是否需要该类型的数组；例如，`[User]`。

类型后面的`!`字符表示该字段是**非空**的。

## 查询

GraphQL 查询用于从数据存储中读取或获取值。

现在您知道如何定义自定义类型了，让我们定义我们的`Query`类型。在这里，我们将定义`getUsers`和`getUserData`。第一个将检索用户列表，而第二个将为我们带来特定用户的数据：

```jsx
type Query {
 getUsers: [User!]
 getUserData(at: String!): User!
}
```

在这种情况下，我们的`getUsers`查询将返回一个用户数组（`[User!]`），而我们的`getUserData`查询，需要`at`（**访问令牌**）属性，将返回一个单一的`User!`。请记住，您在此添加的任何查询，稍后都需要在解析器下定义（我们将在下一节中进行）。

## 变异

变异用于编写或发布值-即修改数据存储中的数据-并在需要执行任何 POST、PUT 或 DELETE 操作的情况下返回一个值，如果您想进行一些与 REST 的比较。`Mutation`类型与`Query`类型完全相同，您需要在其中定义您的变异，并指定您将接收的参数和您将返回的数据：

```jsx
type Mutation {
  createUser(input: CreateUserInput): User!
  login(input: LoginInput): AuthPayload!
}
```

如您所见，我们已经定义了两个变异。第一个是`createUser`，用于在我们的数据存储中注册或创建新用户，而第二个是执行`login`。正如您可能已经注意到的，两者都接收了`input`参数，并带有一些不同的值（`CreateUserInput`和`LoginInput`），称为**输入类型**，这些类型用作查询或变异参数。最后，它们将分别返回`User!`类型和`AuthPayload!`。让我们学习如何定义这些输入：

```jsx
input CreateUserInput {
  username: String!
  password: String!
  email: String!
  privilege: String!
  active: Boolean!
}

input LoginInput {
  email: String!
  password: String!
}

type AuthPayload {
  token: String!
}
```

输入通常与变异一起使用，但您也可以将其与查询一起使用。

# 合并我们的类型定义

现在我们已经定义了所有的类型、查询和变异，我们需要合并所有的 GraphQL 文件来创建我们的 GraphQL 模式，这基本上是一个包含所有我们的 GraphQL 定义的大文件。

为此，您需要创建一个名为`/backend/src/graphql/types/index.ts`的文件，其中包含以下代码：

```jsx
import path from 'path'
import { loadFilesSync } from '@graphql-tools/load-files'
import { mergeTypeDefs } from '@graphql-tools/merge'

const typesArray = loadFilesSync(path.join(__dirname, './'), { extensions: ['graphql'] })

export default mergeTypeDefs(typesArray)
```

我们正在使用`@graphql-tools`包来加载我们的 GraphQL 文件，并使用`mergeTypesDefs`方法将它们合并到`typesArray`中。

# 创建我们的解析器

解析器是负责为 GraphQL 模式中的字段生成数据的函数。它通常可以以任何想要的方式生成数据，可以从数据库中获取数据，也可以使用第三方 API。

要创建我们的用户解析器，您需要创建一个名为`/backend/src/graphql/resolvers/user.ts`的文件。让我们创建一个解析器应该是什么样子的框架。在这里，我们需要指定在我们的 GraphQL 模式下定义的`Query`和`Mutation`下定义的函数。因此，您的解析器应该如下所示：

```jsx
export default {
  Query: {
    getUsers: () => {},
    getUserData: () => {},
  },
  Mutation: {
    createUser: () => {},
    login: () => {}
  }
}
```

正如您所看到的，我们返回了一个具有两个名为`Query`和`Mutation`的主节点的对象，并且我们正在映射我们在 GraphQL 模式中定义的查询和变异（`User.graphql`文件）。当然，我们需要做一些更改来接收一些参数并返回一些数据，但我想先向您展示解析器文件的基本框架。

您需要做的第一件事是向文件添加一些导入：

```jsx
// Lib
import { getUserData } from '../../lib/jwt'

// Interfaces
import {
  IUser,
  ICreateUserInput,
  IModels,
  ILoginInput,
  IAuthPayload
} from '../../types'

// Utils
import { doLogin, getUserBy } from '../../lib/auth'
```

我们将在下一节中创建`doLogin`和`getUserBy`函数。

## 创建 getUsers 查询

我们的第一个方法将是`getUsers`查询。让我们看看我们需要如何定义它：

```jsx
getUsers: (
  _: any,
  args: any,
  ctx: { models: IModels }
): IUser[] => ctx.models.User.findAll(),
```

在任何查询或变异方法中，我们总是接收四个参数：父级（定义为**_**），参数（定义为`args`），上下文（定义为`ctx`）和`info`（可选）。

如果您想简化代码，可以像这样解构上下文：

```jsx
getUsers: (
  _: any,
  args: any,
  { models }: { models: IModels }
): IUser[] => models.User.findAll(),
```

在我们下一个解析器函数中，我们也将解构我们的参数。作为提醒，上下文是在我们的 Apollo Server 设置中传递的（我们之前做过这个）：

```jsx
// Apollo Server
const apolloServer = new ApolloServer({
  schema,
  context: {
    models
  }
})
```

当我们需要在解析器中全局共享一些东西时，上下文非常重要。

## 创建 getUserData 查询

这个函数需要是异步的，因为我们需要执行一些异步操作，比如通过`at`（访问令牌）获取已连接的用户，如果用户已经有一个有效的会话。然后，我们可以通过查看我们的数据库来验证这是否是一个真实的用户。这有助于阻止人们修改 cookie 或尝试进行某种形式的注入。如果我们找不到已连接的用户，那么我们将返回一个包含空数据的用户对象：

```jsx
getUserData: async (
  _: any,
  { at }: { at: string },
  { models }: { models: IModels }
): Promise<any> => {
  // Get current connected user
  const connectedUser = await getUserData(at)

  if (connectedUser) {
    // Validating if the user is still valid
    const user = await getUserBy(
      {
        id: connectedUser.id,
        email: connectedUser.email,
        privilege: connectedUser.privilege,
        active: connectedUser.active
      },
      models
    )

    if (user) {
      return connectedUser
    }
  }

  return {
    id: '',
    username: '',
    password: '',
    email: '',
    privilege: '',
    active: false
  }
}
```

## 创建变异

我们的变异非常简单-我们只需要执行一些函数并通过扩展输入值传递所有参数（这是来自我们的 GraphQL 模式）。让我们看看我们的`Mutation`节点应该是什么样子的：

```jsx
Mutation: {
  createUser: (
    _: any,
    { input }: { input: ICreateUserInput },
    { models }: { models: IModels }
  ): IUser => models.User.create({ ...input }),
  login: (
    _: any,
    { input }: { input: ILoginInput },
    { models }: { models: IModels }
  ): Promise<IAuthPayload> => doLogin(input.email, input.password, models)
}
```

您需要将电子邮件、密码和模型传递给`doLogin`函数。

# 合并我们的解析器

就像我们对类型定义所做的那样，我们需要使用`@graphql-tools`包合并所有我们的解析器。您需要在`/backend/src/graphql/resolvers/index.ts`创建以下文件：

```jsx
import path from 'path'
import { loadFilesSync } from '@graphql-tools/load-files'
import { mergeResolvers } from '@graphql-tools/merge'

const resolversArray = loadFilesSync(path.join(__dirname, './'))
const resolvers = mergeResolvers(resolversArray)

export default resolvers
```

这将把所有你的解析器合并成一个解析器数组。

# 创建 Sequelize 模型

在我们跳入身份验证功能之前，我们需要在 Sequelize 中创建我们的`User`模型。为此，我们需要在`/backend/src/models/User.ts`创建一个文件。我们的模型将具有以下字段：

+   `id`

+   `username`

+   `password`

+   `email`

+   `privilege`

+   `active`

让我们看看代码：

```jsx
// Dependencies
import { encrypt } from '@contentpi/lib'

// Interfaces
import { IUser, IDataTypes } from '../types'

export default (sequelize: any, DataTypes: IDataTypes): IUser => {
  const User = sequelize.define(
    'User',
    {
      id: {
        primaryKey: true,
        allowNull: false,
        type: DataTypes.UUID,
        defaultValue: DataTypes.UUIDV4()
      },
      username: {
        type: DataTypes.STRING,
        allowNull: false,
        unique: true,
        validate: {
          isAlphanumeric: {
            args: true,
            msg: 'The user just accepts alphanumeric characters'
          },
          len: {
            args: [4, 20],
            msg: 'The username must be from 4 to 20 characters'
          }
        }
      },
      password: {
        type: DataTypes.STRING,
        allowNull: false
      },
      email: {
        type: DataTypes.STRING,
        allowNull: false,
        unique: true,
        validate: {
          isEmail: {
            args: true,
            msg: 'Invalid email'
          }
        }
      },
      privilege: {
        type: DataTypes.STRING,
        allowNull: false,
        defaultValue: 'user'
      },
      active: {
        type: DataTypes.BOOLEAN,
        allowNull: false,
        defaultValue: false
      }
    },
    {
      hooks: {
        beforeCreate: (user: IUser): void => {
          user.password = encrypt(user.password)
        }
      }
    }
  )

  return User
}
```

如您所见，我们正在定义一个名为`beforeCreate`的 Sequelize Hook，它在数据保存之前帮助我们加密（使用`sha1`）用户密码。最后，我们返回`User`模型。

# 将 Sequelize 连接到 PostgreSQL 数据库

现在我们已经创建了用户模型，我们需要将 Sequelize 连接到我们的 PostgreSQL 数据库并将所有模型放在一起。您需要将以下代码添加到`/backend/src/models/index.ts`文件中：

```jsx
// Dependencies
import { Sequelize } from 'sequelize'

// Configuration
import { $db } from '../../config'

// Interfaces
import { IModels } from '../types'

// Db Connection
const { dialect, port, host, database, username, password } = $db

// Connecting to the database
const uri = `${dialect}://${username}:${password}@${host}:${port}/${database}`
const sequelize = new Sequelize(uri)

// Models
const models: IModels = {
  User: require('./User').default(sequelize, Sequelize),
  sequelize
}

export default models
```

# 身份验证功能

我们正在一步一步地把所有的拼图拼在一起。现在，让我们来看看我们正在使用的身份验证功能，以验证用户是否已连接并获取用户的数据。为此，我们需要使用 JSON Web Tokens（JWTs）。

## 什么是 JSON Web Token？

**JWT**是一个开放标准 - RFC 7519 ([`tools.ietf.org/html/rfc7519`](https://tools.ietf.org/html/rfc7519)) - 用于在各方之间传输信息作为 JSON 对象。JWT 的优势在于它们是数字签名的，这就是为什么它们可以被验证和信任的原因。它使用 HMAC 算法通过使用秘密或 RSA 或 ECDSA 的公钥对令牌进行签名。

## JWT 功能

让我们创建一些函数来帮助验证 JWT 并获取用户数据。为此，我们需要创建`jwtVerify`，`getUserData`和`createToken`函数。该文件应该在`/backend/src/lib/jwt.ts`中创建：

```jsx
// Dependencies
import jwt from 'jsonwebtoken'
import { encrypt, setBase64, getBase64 } from '@contentpi/lib'

// Configuration
import { $security } from '../../config'

// Interface
import { IUser } from '../types'

const { secretKey } = $security

export function jwtVerify(accessToken: string, cb: any): void {
  // Verifiying our JWT token using the accessToken and the secretKey
  jwt.verify(
    accessToken,
    secretKey,
    (error: any, accessTokenData: any = {}) => {
      const { data: user } = accessTokenData

      // If we get an error or the user is not found we return false
      if (error || !user) {
        return cb(false)
      }

      // The user data is on base64 and getBase64 will retreive the 
      // information as JSON object
      const userData = getBase64(user)

      return cb(userData)
    }
  )
}

export async function getUserData(accessToken: string): Promise<any> {
  // We resolve the jwtVerify promise to get the user data
  const UserPromise = new Promise(resolve =>
    jwtVerify(accessToken, (user: any) => resolve(user))
  )

  // This will get the user data or false (if the user is not connected)
  const user = await UserPromise

  return user
}

export const createToken = async (user: IUser): Promise<string[]> => {
  // Extracting the user data
  const { id, username, password, email, privilege, active } = user

  // Encrypting our password by combining the secretKey and the password 
  // and converting it to base64
  const token = setBase64(`${encrypt($security.secretKey)}${password}`)

  // The "token" is an alias for password in this case
  const userData = {
    id,
    username,
    email,
    privilege,
    active,
    token
  }

  // We sign our JWT token and we save the data as Base64
  const _createToken = jwt.sign(
    { data: setBase64(userData) },
    $security.secretKey,
    { expiresIn: $security.expiresIn }
  )

  return Promise.all([_createToken])
}
```

如您所见，`jwt.sign`用于创建新的 JWT，而`jwt.verify`用于验证我们的 JWT。

## 创建身份验证功能

现在我们已经创建了 JWT 功能，我们需要创建一些函数来帮助我们在`/backend/src/lib/auth.ts`登录：

```jsx
// Dependencies
import { AuthenticationError } from 'apollo-server'

// Utils
import { encrypt, isPasswordMatch } from '@contentpi/lib'

// Interface
import { IUser, IModels, IAuthPayload } from '../types'

// JWT
import { createToken } from './jwt'

export const getUserBy = async (
  where: any,
  models: IModels
): Promise<IUser> => {
  // We find a user by a WHERE condition
  const user = await models.User.findOne({
    where,
    raw: true
  })

  return user
}

export const doLogin = async (
  email: string,
  password: string,
  models: IModels
): Promise<IAuthPayload> => {
  // Finding a user by email
  const user = await getUserBy({ email }, models)

  // If the user does not exists we return Invalid Login
  if (!user) {
    throw new AuthenticationError('Invalid Login')
  }

  // We verify that our encrypted password is the same as the user.password 
  // value
  const passwordMatch = isPasswordMatch(encrypt(password), user.password)

  // We validate that the user is active
  const isActive = user.active

  // If the password does not match we return invalid login
  if (!passwordMatch) {
    throw new AuthenticationError('Invalid Login')
  }

  // If the account is not active we return an error
  if (!isActive) {
    throw new AuthenticationError('Your account is not activated yet')
  }

 // If the user exists, the password is correct and the account is active 
 // then we create the JWT token
  const [token] = await createToken(user)

  // Finally we return the token to Graphql
  return {
    token
  }
}
```

在这里，我们正在验证用户是否存在通过电子邮件，密码是否正确，以及账户是否处于活动状态以创建 JWT。

## 类型和接口

最后，我们需要为所有 Sequelize 模型和 GraphQL 输入定义我们的类型和接口。为此，您需要在`/backend/src/types/types.ts`创建一个文件：

```jsx
export type User = {
  username: string
  password: string
  email: string
  privilege: string
  active: boolean
}

export type Sequelize = {
  _defaults?: any
  name?: string
  options?: any
  associate?: any
}
```

现在，让我们在`/backend/src/types/interfaces.ts`创建我们的接口：

```jsx
// Types
import { User, Sequelize } from './types'

// Sequelize
export interface IDataTypes {
  UUID: string
  UUIDV4(): string
  STRING: string
  BOOLEAN: boolean
  TEXT: string
  INTEGER: number
  DATE: string
  FLOAT: number
}

// User
export interface IUser extends User, Sequelize {
  id: string
  token?: string
  createdAt?: Date
  updatedAt?: Date
}

export interface ICreateUserInput extends User {}

export interface ILoginInput {
  email: string
  password: string
}

export interface IAuthPayload {
  token: string
}

// Models
export interface IModels {
  User: any
  sequelize: any
}
```

最后，我们需要在`/backend/src/types/index.ts`中导出这两个文件：

```jsx
export * from './interfaces'
export * from './types'
```

当您需要添加更多模型时，请记住始终将您的类型和接口添加到这些文件中。

最后，您需要在根目录创建您的`tsconfig.json`文件：

```jsx
{
  "compilerOptions": {
    "baseUrl": "./src",
    "esModuleInterop": true,
    "module": "commonjs",
    "noImplicitAny": true,
    "outDir": "dist",
    "resolveJsonModule": true,
    "sourceMap": true,
    "target": "es6",
    "typeRoots": ["./src/@types", "./node_modules/@types"]
  },
  "include": ["src/**/*.ts"],
  "exclude": ["node_modules"]
}
```

在下一节中，我们将运行我们的项目并创建我们的表。

# 首次运行我们的项目

如果您按照前面的部分正确操作并运行`npm run dev`命令，您应该能够看到`Users`表正在被创建，并且 Apollo Server 正在端口`5000`上运行：

![](img/5f430c43-93a6-4a50-ba6f-edeeb9be19f8.png)

现在，假设您想修改用户模型并将`"username"`字段更改为`"username2"`。让我们看看会发生什么：

```jsx
[INFO] 23:45:16 Restarting: /Users/czantany/projects/React-Design-Patterns-and-Best-Practices-Third-Edition/Chapter05/graphql/backend/src/models/User.ts has been modified
Executing (default): CREATE TABLE IF NOT EXISTS "Users" ("id" UUID NOT NULL , "username2" VARCHAR(255) NOT NULL UNIQUE, "password" VARCHAR(255) NOT NULL, "email" VARCHAR(255) NOT NULL UNIQUE, "privilege" VARCHAR(255) NOT NULL DEFAULT 'user', "active" BOOLEAN NOT NULL DEFAULT false, "createdAt" TIMESTAMP WITH TIME ZONE NOT NULL, "updatedAt" TIMESTAMP WITH TIME ZONE NOT NULL, PRIMARY KEY ("id"));
Executing (default): ALTER TABLE "public"."Users" ADD COLUMN "username2" VARCHAR(255) NOT NULL UNIQUE;
Executing (default): ALTER TABLE "Users" ALTER COLUMN "password" SET NOT NULL;ALTER TABLE "Users" ALTER COLUMN "password" DROP DEFAULT;ALTER TABLE "Users" ALTER COLUMN "password" TYPE VARCHAR(255);
Executing (default): ALTER TABLE "Users" ALTER COLUMN "email" SET NOT NULL;ALTER TABLE "Users" ALTER COLUMN "email" DROP DEFAULT;ALTER TABLE "Users" ADD UNIQUE ("email");ALTER TABLE "Users" ALTER COLUMN "email" TYPE VARCHAR(255) ;
Executing (default): ALTER TABLE "Users" ALTER COLUMN "privilege" SET NOT NULL;ALTER TABLE "Users" ALTER COLUMN "privilege" SET DEFAULT 'user';ALTER TABLE "Users" ALTER COLUMN "privilege" TYPE VARCHAR(255);
Executing (default): ALTER TABLE "Users" ALTER COLUMN "active" SET NOT NULL;ALTER TABLE "Users" ALTER COLUMN "active" SET DEFAULT false;ALTER TABLE "Users" ALTER COLUMN "active" TYPE BOOLEAN;
Executing (default): ALTER TABLE "Users" ALTER COLUMN "createdAt" SET NOT NULL;ALTER TABLE "Users" ALTER COLUMN "createdAt" DROP DEFAULT;ALTER TABLE "Users" ALTER COLUMN "createdAt" TYPE TIMESTAMP WITH TIME ZONE;
Running on http://localhost:5000/
```

这将执行以下 SQL 查询：

```jsx
Executing (default): ALTER TABLE "public"."Users" ADD COLUMN "username2" VARCHAR(255) NOT NULL UNIQUE;
Executing (default): ALTER TABLE "public"."Users" DROP COLUMN "username";
```

现在，假设您将`index.ts`文件中的`force`常量更改为`true`。将会发生以下情况：

![](img/8beddecf-b8ea-45e8-a30a-de8695d88145.png)

如你所见，如果 `force` 是 `true`，它将执行 `DROP TABLE IF EXISTS "Users" CASCADE;`。这将完全删除你的表和值，然后从头开始重新创建你的表。这就是为什么当你使用 `force` 选项时需要小心。

此时，如果你打开 `http://localhost:5000`，你应该能够看到你的 GraphQL Playground：

![](img/3ff9f5e1-fda9-49ee-8b39-21dcd31a3593.png)

现在，我们准备测试我们的查询和变更。

# 测试我们的 GraphQL 查询和变更

太棒了！此时，你非常接近执行你的第一个 GraphQL 查询和变更。我们将执行的第一个查询是 `getUsers`。以下是运行查询的正确语法：

```jsx
query {
  getUsers {
    id
    username
    email
    privilege
  }
}
```

当你没有任何属性要传递给查询时，你只需要在 `query {...}` 块下指定查询的名称，然后在执行查询后指定要检索的字段。在这种情况下，我们想要获取 `id`、`username`、`email` 和 `privilege` 字段。

如果你运行这个查询，你可能会得到一个空数组的数据。这是因为我们还没有注册任何用户：

![](img/ebde4ff2-1a7a-4738-af50-6d465f34a1c7.png)

这意味着我们需要执行我们的 `createUser` 变更以注册我们的第一个用户。我喜欢 GraphQL Playground 的一件事是你可以在右侧的 DOCS 选项卡中找到所有的模式文档。如果你点击 DOCS 选项卡，你会看到所有的查询和变更列出来。让我们点击那里并选择我们的 `createUser` 变更，看看需要调用什么以及可能返回什么数据：

![](img/7cc48a8b-a914-4078-9c57-6b96520abc21.png)

如你所见，`createUser` 变更需要一个输入参数，即 `CreateUserInput`。让我们点击这个输入：

![](img/c1ec6778-c671-409b-aad8-d7337661de61.png)

太棒了！现在，我们知道我们需要传递 `username`、`password`、`email`、`privilege` 和 `active` 字段以创建一个新用户，并且我们将收到相同的字段，以及用户的生成 ID。让我们来做这个！

创建一个新的选项卡，这样你就不会丢失你的第一个查询的代码，然后写下变更：

```jsx
mutation {
  createUser(
    input: {
      username: "admin",
      email: "admin@js.education",
      password: "123456",
      privilege: "god",
      active: true
    }
  ) {
    id
    username
    email
    password
    privilege
  }
}
```

如你所见，你的变更需要写在 `mutation {...}` 块下，并且你必须将输入参数作为对象传递。最后，你必须指定在变更正确执行后要检索的字段。如果一切正常，你应该会看到类似这样的东西：

![](img/06124e63-8eaf-4ba4-b5c6-692ca2f79ef8.png)

如果你好奇并希望看一下你运行 Apollo Server 的终端，你会看到为这个用户执行的 SQL 查询：

```jsx
Executing (default): INSERT INTO "Users" ("id","username","password","email","privilege","active","createdAt","updatedAt") VALUES ($1,$2,$3,$4,$5,$6,$7,$8) RETURNING "id","username","password","email","privilege","active","createdAt","updatedAt";
```

`VALUES`变量由 Apollo Server 处理，所以你在那里看不到实际的值，但你可以找出在数据库中执行的操作。

现在，回到你的第一个查询（`getUsers`）并再次运行它！

![](img/1f0832dd-8995-4248-baf9-fb6d4565b703.png)

不错 - 这是你在 GraphQL 中第一次正确执行的查询和变异。如果你想在数据库中看到这些数据，你可以使用 OmniDB 来查看你的 PostgreSQL 数据库中的`Users`表：

![](img/cdb74ae3-3a5f-4d96-b7f4-2b20fa8890d3.png)

正如你所看到的，我们的第一条记录有它自己的`id`字段（UUID），并且还有一个加密的`password`字段（你还记得我们在用户模型中的`beforeCreate` Hook 吗？）。默认情况下，Sequelize 会创建`createdAt`和`updatedAt`字段。

## 验证

正如你可能记得的，关于我们的用户模型，你会想确保我们所做的所有验证都能正常工作，比如用户是否唯一，以及他们的电子邮件是否有效和唯一。你只需要再次执行完全相同的变异：

![](img/a9f45ac1-2c20-422e-9aa0-f27175f9dd65.png)

正如你所看到的，我们会收到一个“用户名必顺是唯一的”错误消息，因为我们已经注册了`"admin"`用户名。现在，让我们尝试将用户名更改为`"admin2"`，但保持电子邮件不变（`admin@js.education`）：

![](img/8d918236-1d29-4074-80c2-bcd31fadbcd8.png)

我们还会收到一个“电子邮件必须是唯一的”错误。现在，尝试将电子邮件更改为一些无效的内容，比如`admin@myfakedomain`：

![](img/c4ae35ab-dd85-4b0e-b04b-7e8e727ec89d.png)

现在，我们收到了一个“无效的电子邮件”错误消息。这真是太棒了，你不觉得吗？现在，让我们停止玩验证，并添加一个新的有效用户（`username: admin2`，`email: admin2@js.education`）。一旦你创建了第二个用户，再次运行我们的`getUsers`查询。然而，这一次，将`"active"`字段添加到我们想要返回的字段列表中：

![](img/e884bf63-a32e-4c8f-b0c6-7adea767f4aa.png)

现在，我们有两个注册用户，都是非活跃账户（`active = false`）。

我喜欢 GraphQL 的一件事是，当你编写你的查询或突变时，如果你不记得某个字段，GraphQL 总是会显示该查询或突变的可用字段列表。例如，如果你只是写了字母`p`作为密码，你会看到类似这样的东西：

![](img/44ce1fd2-0f38-44ea-8072-0607a3647109.png)

现在，我们准备尝试登录！

# 执行登录

我想祝贺你在这本书中达到了这一点 - 我知道我们已经涵盖了很多内容，但我们几乎到了！现在，我们将尝试使用 GraphQL 登录（这有多疯狂？）。

首先，我们需要编写我们的登录突变：

```jsx
mutation {
  login(
    input: {
      email: "fake@email.com",
      password: "123456"
    }
  ) {
    token
  }
}
```

然后，我们需要使用`"fake@email.com"`作为我们的电子邮件和`"123456"`作为我们的密码来登录我们的用户。这些在我们的数据库中不存在：

![](img/2ac6af0e-81ca-4bb4-9c62-74e7c9714aac.png)

因为电子邮件在我们的数据库中不存在，将返回一个`"无效登录"`错误消息。现在，让我们添加正确的电子邮件，但使用一个虚假的密码：

![](img/d1e1b450-ddbb-4f87-b953-17c830ef667d.png)

正如你所看到的，我们收到了完全相同的错误（`"无效登录"`）。这是因为我们不希望提供关于登录出了什么问题的太多信息，因为有人可能正在尝试黑入另一个用户。如果我们说诸如`"无效密码"`或`"您的电子邮件在我们的系统中不存在"`之类的话，我们就给了攻击者额外的信息，他们可能会发现有用。

现在，让我们尝试使用正确的用户和密码（`admin@js.education / 123456`）进行连接，看看会发生什么：

![](img/3184a87d-55d9-472d-a074-6694f4d8686e.png)

现在，我们收到了一个错误，指出`"您的帐户尚未激活"`。这没关系，因为我们的用户还没有被激活。通常，当用户在系统中注册时，您需要发送一个链接到他们的电子邮件，以便他们可以激活他们的帐户。我们目前没有这个功能，但假设我们发送了那封电子邮件，并且用户已经激活了他们的帐户。我们可以通过手动更改我们的数据库中的值来模拟这一点。我们可以通过执行`UPDATE` SQL 查询来做到这一点：

![](img/4592d78c-f4e9-4d76-aa46-fc85a1394287.png)

现在，让我们再次尝试登录！

![](img/111759ec-1952-4aa5-a3c0-0992240a5e26.png)

不错 - 我们成功了！**你现在**：

![](img/c1670e9c-4548-440a-80a0-9e9baab11647.jpeg)

*我们是匿名的，我们是一体的，我们不会原谅，我们不会忘记，期待我们！*

现在我们已经登录并检索到了我们的 JWT，让我们复制那个巨大的字符串，并在我们的`getUserData`查询中使用它，看看我们是否可以获取用户的数据：

```jsx
query {
  getUserData(at: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjoiZXlKcFpDSTZJalEzTnpsaU16QTJMV1U0TW1NdE5HVmtNUzFoWldNM0xXSXdaVEl5TWpSaU5UUTNaU0lzSW5WelpYSnVZVzFsSWpvaVlXUnRhVzRpTENKbGJXRnBiQ0k2SW1Ga2JXbHVRR3B6TG1Wa2RXTmhkR2x2YmlJc0luQnlhWFpwYkdWblpTSTZJbWR2WkNJc0ltRmpkR2wyWlNJNmRISjFaU3dpZEc5clpXNGlPaUpOUkdjeldWUkZNMXBVWjNwTmJWVjVXV3BWTWs1SFJtMWFiVTB6V21wTk5GbFVRWGxhVkdSb1RVUm9iVTFIVlROTmJWa3dXVlJrYWs1SFJUUmFSRUUxV1RKRmVrNTZXWGxaVjFreVRWZFZNVTlVVlhsTlJHc3dUVEpTYWsxcVdUQlBWRkp0VDBSck1FMVhTVDBpZlE9PSIsImlhdCI6MTYxNzY5ODY4OSwiZXhwIjoxNjE4MzAzNDg5fQ.6icaBFibjEOICUt5QQ0OPAoDsb7_ohb8W10JzHnbf7k") {
    id
 email
 privilege
 active
  }
}
```

如果一切顺利，那么你应该得到用户的数据：

![](img/0b4f0c18-ec40-4914-bca5-119771e37789.png)

如果你改变或删除字符串中的任何字母（意味着令牌无效），那么你应该得到空的用户数据：

![](img/519b4e1e-6271-4d4d-be9a-71ff7e72fcc7.png)

现在我们的后端登录系统完美运行，是时候在前端应用程序中实现了。我们将在下一节中进行此操作。

# 使用 Apollo Client 构建前端登录系统

在上一节中，我们学习了如何使用 Apollo Server 构建登录系统的后端，以创建我们的 GraphQL 查询和变异。你可能会想，“太好了，我已经让后端工作了，但我怎么在前端使用呢？”你是对的 - 我总是喜欢用完整的例子来解释事情，而不仅仅是展示基本的东西，即使这样做会花费更长的时间，所以让我们开始吧！

您可以在本节的示例代码中找到[`github.com/PacktPublishing/React-17-Design-Patterns-and-Best-Practices-Third-Edition/tree/main/Chapter05/graphql/frontend`](https://github.com/PacktPublishing/React-17-Design-Patterns-and-Best-Practices-Third-Edition/tree/main/Chapter05/graphql/frontend)。

## 配置 Webpack 5

我们将不再使用`create-react-app`项目，而是使用 Webpack 5 和 Node 从头开始配置一个 React 项目。

我们需要做的第一件事是安装我们将要使用的所有软件包：

```jsx
npm init --yes

npm install @apollo/client @contentpi/lib cookie-parser cors express express-session jsonwebtoken react react-dom react-cookie react-router-dom styled-components

npm install --save-dev @babel/core @babel/preset-env @babel/preset-react buffer cross-env crypto-browserify dotenv prettier stream-browserify ts-loader ts-node ts-node-dev typescript webpack webpack-cli webpack-dev-server html-webpack-plugin
```

缓冲区`crypto-browserify`和`stream-browserify`是 Webpack <= 4 中默认包含的 polyfill。然而，在最新版本（Webpack 5）中，这些不再包含在内，所以你会得到以下错误：

![](img/b89f72fb-3f13-4d52-919d-20fef237f684.png)

您需要在您的`package.json`中有这些脚本：

```jsx
"scripts": {
    "start": "ts-node src/server",
    "dev": "ts-node-dev src/server",
    "webpack": "cross-env NODE_ENV=development webpack serve --mode development",
    "build": "cross-env NODE_ENV=production webpack --mode production",
    "clean": "rimraf dist/ && rimraf public/app",
    "lint": "eslint . --ext .js,.tsx,.ts",
    "lint:fix": "eslint . --fix --ext .js,.tsx,.ts",
    "test": "jest src",
    "test:coverage": "jest src --coverage"
  }
```

让我们检查我们的 Webpack 5 配置文件（`/frontend/webpack.config.ts`）：

```jsx
// Dependencies
import path from 'path'
import webpack, { Configuration } from 'webpack'
import HtmlWebPackPlugin from 'html-webpack-plugin'

// Environment
const isProduction = process.env.NODE_ENV === 'production'

const webpackConfig: Configuration = {
  devtool: !isProduction ? 'source-map' : false,
  target: 'web',
  mode: isProduction ? 'production' : 'development',
  entry: './src/index.tsx',
  output: {
    path: path.join(__dirname, 'dist'),
    filename: '[name].js',
    publicPath: '/'
  },
  resolve: {
    extensions: ['.ts', '.tsx', '.js', '.json'],
    fallback: { // This is to fix the polifylls errors
      buffer: require.resolve('buffer'),
      crypto: require.resolve("crypto-browserify"),
      stream: require.resolve("stream-browserify")
    }
  },
  module: {
    rules: [
      {
        test: /\.(ts|tsx)$/,
        use: {
          loader: 'ts-loader',
          options: {
            transpileOnly: true
          }
        },
        exclude: /node_modules/
      }
    ]
  },
  optimization: {
    splitChunks: { // This will split our bundles into vendor.js and 
    // main.js
      cacheGroups: {
        default: false,
        commons: {
          test: /node_modules/,
          name: 'vendor',
          chunks: 'all'
        }
      }
    }
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin(),
    new HtmlWebPackPlugin({
      template: './src/index.html',
      filename: './index.html',
      publicPath: !isProduction ? 'http://localhost:8080/' : '' // For dev 
      // we will read the bundle from localhost:8080 (webpack-dev-server)
    })
  ]
}

export default webpackConfig
```

在这一点上，您需要创建`index.html`文件，应该在`/frontend/src/index.html`：

```jsx
<!DOCTYPE html>
<html>
 <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1, 
      maximum-scale=1" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Login System</title>
 </head>

 <body>
 <div id="root"></div>
 </body>
</html>
```

在下一节中，我们将配置我们的 TypeScript。

## 配置我们的 TypeScript

我们的`tsconfig.json`文件应该是这样的：

```jsx
{
  "compilerOptions": {
    "sourceMap": true,
    "target": "es5",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "module": "commonjs",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "noImplicitAny": false,
    "types": ["node", "express"]
  },
  "include": ["src"]
}
```

现在，让我们学习如何配置 Express 服务器。

## 配置 Express 服务器

我们的应用程序需要 Express 服务器，以便我们可以进行验证。这将帮助我们找出用户是否已连接（使用自定义中间件，稍后我会解释），还可以配置我们的 Express 会话。我们网站上有四个主要路由：

+   `/`：我们的主页**(由 React 处理)**。

+   `/dashboard`：我们的仪表板，受保护。只有具有 god 或 admin 权限的连接用户被允许**(首先由 Express 处理，然后由 React 处理)**。

+   `/login`：我们的登录页面**(由 React 处理)**。

+   `/logout`：这将删除我们现有的会话**(由 Express 处理)**。

让我们看看我们的服务器代码。以下文件应存在于`/frontend/src/server.ts`：

```jsx
// Dependencies
import express, { Request, Response, NextFunction } from 'express'
import path from 'path'
import cookieParser from 'cookie-parser'
import cors from 'cors'
import session from 'express-session'

// Middleware
import { isConnected } from './lib/middlewares/user'

// Config
import config from './config'

// Express app
const app = express();
const port = process.env.NODE_PORT || 3000
const DIST_DIR = path.join(__dirname, '../dist')
const HTML_FILE = path.join(DIST_DIR, 'index.html')

// Making the dist directory static
app.use(express.static(DIST_DIR));

// Middlewares
app.use(
  session({
    resave: false,
    saveUninitialized: true,
    secret: config.security.secretKey
  })
)
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(cookieParser(config.security.secretKey))
app.use(cors({ credentials: true, origin: true }))

// Routes
app.get('/dashboard',
  isConnected(
    true,
    ['god', 'admin'], // Those are the allowed permissions
    `/login?redirectTo=/dashboard` // If the user is not allowed will be 
    // redirect to this path
  ),
  (req: Request, res: Response, next: NextFunction) => {
    // If the user isConnected then we allow the access to the dashboard 
    // page otherwise will be redirect to /login
    next()
  }
)

// Forcing only No connected users to access to /login, if a connected user 
// try to access will be redirect to the homepage
app.get('/login', isConnected(false), (req: Request, res: Response, next: NextFunction) => {
  next()
})

app.get(`/logout`, (req: Request, res: Response) => {
  // This will cler our "at" cookie and redirect to home
  res.clearCookie('at')
  res.redirect('/')
})

app.get('*', (req: Request, res: Response) => {
  // We render our React application
  res.sendFile(HTML_FILE)
})

// Listening
app.listen(port, () => console.log(`Running at http://localhost:${port}`))
```

正如您所看到的，我们正在使用`isConnected`中间件保护我们的仪表板路由。在这里，我们正在验证我们只接受在`login`路由中未连接的用户。

## 创建我们的前端配置

现在，我们需要创建我们的前端配置。因此，让我们在`/frontend/src/config/common.json`创建`common.json`配置：

```jsx
{
  "server": {
    "port": 3000
  },
  "security": {
    "secretKey": "C0nt3ntP1", // This needs to be the same as the backend 
      // secretKey
    "expiresIn": "7d"
  }
}
```

现在，让我们创建我们的`local.json`文件：

```jsx
{
  "baseUrl": "http://localhost:3000",
  "apiUrl": "http://localhost:5000/graphql"
}
```

现在，我们需要创建我们的`production.json`文件；目前，由于我们没有实际的生产环境，我们将使用相同的本地主机 URL，但是一旦将此项目放入生产环境中，您将需要更改为实际的域名：

```jsx
{
  "baseUrl": "http://localhost:3000",
  "apiUrl": "http://localhost:5000/graphql"
}
```

现在我们已经定义了我们的配置文件，我们需要创建一个`index.ts`文件，以便我们可以将我们的配置合并并导出为一个对象：

```jsx
// Configuration
import common from './common.json'
import local from './local.json'
import production from './production.json'

// Interface
interface IConfig {
 baseUrl: string
 apiUrl: string
 server: {
 port: number
 }
 security: {
 secretKey: string
 expiresIn: string
 }
}

const { NODE_ENV = 'development' } = process.env

// development => local
let environment = 'local'

if (NODE_ENV !== 'development') {
 environment = NODE_ENV
}

// Configurations by environment
const config: IConfig = {
 ...common,
 ...(environment === 'local' ? local : production)
}

// Environments validations
export const isLocal = () => environment === 'local'
export const isProduction = () => environment === 'production'

export default config
```

现在，我们需要创建一个名为`middleware`的用户和`jwt`函数，以验证用户是否已连接并具有正确的权限。

## 创建用户中间件

中间件是一个函数，可以访问请求对象（req）、响应对象（res）和应用程序请求-响应周期中的下一个函数。当调用时，next 函数是 Express 路由中的一个函数，执行当前中间件后继的中间件。以下图表描述了中间件流程：

![](img/f353071c-3986-4906-ab1d-43ad4fa4c0db.png)

在我们的情况下，我们将创建`isConnected`中间件，以验证用户是否已连接并具有正确的权限。如果没有，我们将中断流程并将其重定向到登录页面。如果用户有效，我们将执行下一个中间件，这将呈现我们的 React 应用程序。

以下图表描述了这个过程：

![](img/941997a7-1499-45cc-b9a3-45511468fce2.png)

让我们将理论部分应用到我们的代码中。所需的文件应该存在于`/frontend/src/lib/middlewares/user.ts`中：

```jsx
// Dependencies
import { Request, Response, NextFunction } from 'express'

// Lib
import { getUserData } from '../jwt'

export const isConnected = (isLogged = true, privileges = ['user'], redirectTo = '/') => async (
  req: Request,
  res: Response,
  next: NextFunction
): Promise<void> => {
  // Getting the user information by passing our 'at' cookie
  const user = await getUserData(req.cookies.at)

  if (!user && !isLogged) {
    // This is to allow No connected users
    return next()
  }

  // Allowing just connected users and validating privileges...
  if (user && isLogged) {
    // If the user is connected and is god...
    if (privileges.includes('god') && user.privilege === 'god') {
      return next()
    }

    // If the user is conencted and is admin...
    if (privileges.includes('admin') && user.privilege === 'admin') {
      return next()
    }

    // If the user is connected but is not god or admin.
    res.redirect(redirectTo)
  } else {
    // If the user is not connected
    res.redirect(redirectTo)
  }
}
```

基本上，通过这个中间件，我们可以控制是否要验证用户是否连接（`isLogged = true`）。然后，我们可以验证特定的特权（`privileges = ['god', 'admin']`）并重定向用户，如果他们没有连接或没有正确的特权（`redirectTo = '/'`）。

正如你所看到的，我们正在使用`jwt`中的`getUserData`函数。我们将在下一节中创建我们的`jwt`函数。

## 创建 JWT 函数

在前面的部分，当我解释后端代码时，我谈到了 JWT。在前端，我们需要这些函数来验证我们的令牌并获取用户的数据。让我们在`/frontend/src/lib/jwt.ts`中创建一个包含以下代码的文件：

```jsx
// Dependencies
import jwt from 'jsonwebtoken'
import { getBase64 } from '@contentpi/lib'

// Configuration
import config from '../config'

// Getting our secretKey
const {
  security: { secretKey }
} = config

export function jwtVerify(accessToken: any, cb: any): void {
  // Validating our accessToken
  jwt.verify(accessToken, secretKey, (error: any, accessTokenData: any = 
   {}) => {
    const { data: user } = accessTokenData

    // If we got an error or the user is not connected we return false
    if (error || !user) {
      return cb(false)
    }

    // Getting the user data
    const userData = getBase64(user)

    return cb(userData)
  })
}

export async function getUserData(accessToken: any): Promise<any> {
  // This is an async function to retrieve the user data from the 
  // jwtVerify function
  const UserPromise = new Promise(resolve => jwtVerify(accessToken, (user: 
   any) => resolve(user)))

  const user = await UserPromise

  return user
}
```

正如你所看到的，我们的`getUserData`函数将使用从 cookies 中获取的`accessToken`来检索用户数据。JWT 的有效性非常重要。

## 创建我们的 GraphQL 查询和变异

我们已经在后端项目中创建了所需的查询和变异。在这一点上，我们需要创建一些文件来在前端项目中执行它们。现在，我们只需要定义我们的`getUserData`查询和我们的登录变异。

让我们在`/frontend/src/graphql/user/getUserData.query.ts`中创建我们的`getUserData`查询：

```jsx
// Dependencies
import { gql } from '@apollo/client'

export default gql`
  query getUserData($at: String!) {
    getUserData(at: $at) {
      id
      email
      username
      privilege
      active
    }
  }
`
```

我们的登录变异应该在`/frontend/src/graphql/user/login.mutation.ts`中。

```jsx
// Dependencies
import { gql } from '@apollo/client'

export default gql`
  mutation login($email: String!, $password: String!) {
    login(input: { email: $email, password: $password }) {
      token
    }
  }
`
```

现在我们已经定义了我们的查询和变异，让我们创建用户上下文，以便我们可以使用它们。

## 创建我们的用户上下文来处理登录和连接的用户

在我们的用户上下文中，我们将有一个登录方法，将执行我们的变异，并验证电子邮件和密码是否正确。我们还将导出用户数据。

让我们在`/frontend/src/contexts/user.tsx`中创建这个上下文：

```jsx
// Dependencies
import { FC, createContext, ReactElement, useState, useEffect } from 'react'
import { useCookies } from 'react-cookie'
import { getGraphQlError, redirectTo, getDebug } from '@contentpi/lib'
import { useQuery, useMutation } from '@apollo/client'

// Mutations
import LOGIN_MUTATION from '../graphql/user/login.mutation'

// Queries
import GET_USER_DATA_QUERY from '../graphql/user/getUserData.query'

// Interfaces
interface IUserContext {
  login(input: any): any
  connectedUser: any
}

interface IProps {
  page?: string
  children: ReactElement
}

// Creating context
export const UserContext = createContext<IUserContext>({
  login: () => null,
  connectedUser: null
})

const UserProvider: FC<IProps> = ({ page = '', children }): ReactElement => {
  const [cookies, setCookie] = useCookies()
  const [connectedUser, setConnectedUser] = useState(null)

  // Mutations
  const [loginMutation] = useMutation(LOGIN_MUTATION)

  // Queries
  const { data: dataUser } = useQuery(GET_USER_DATA_QUERY, {
    variables: {
      at: cookies.at || ''
    }
  })

  // Effects
  useEffect(() => {
    if (dataUser) {
      if (!dataUser.getUserData.id && page !== 'login') {
 // If the user session is invalid and is on a different page than 
        // login 
 // we redirect them to login
        redirectTo('/login?redirectTo=/dashboard')
      } else {
        // If we have the user data available we save it in our 
       // connectedUser state
        setConnectedUser(dataUser.getUserData)
      }
    }
  }, [dataUser, page])

  async function login(input: { email: string; password: string }):
   Promise<any> {
    try {
      // Executing our loginMutation passing the email and password
      const { data: dataLogin } = await loginMutation({
        variables: {
          email: input.email,
          password: input.password
        }
      })

      if (dataLogin) {
        // If the login was success, we save the token in our "at" cookie
        setCookie('at', dataLogin.login.token, { path: '/' })

        return dataLogin.login.token
      }
    } catch (err) {
      // If there is an error we return it
      return getGraphQlError(err)
    }
  }

 // Exporting our context
  const context = {
    login,
    connectedUser
  }

  return <UserContext.Provider value={context}>{children}</UserContext.Provider>
}

export default UserProvider
```

正如你所看到的，我们正在处理登录并在我们的上下文中获取`connectedUser`数据。在这里，我们一直执行`GET_USER_DATA_QUERY`来验证用户是否连接（验证数据库而不仅仅是使用 cookies）。

## 配置我们的 Apollo 客户端

到目前为止，我们已经创建了很多代码，但如果我们不配置我们的 Apollo 客户端，它们中的任何一个都不会起作用。要配置它，我们需要将它添加到我们的索引文件中`/frontend/src/index.tsx`：

```jsx
// Dependencies
import { render } from 'react-dom'

// Apollo
import { ApolloProvider, ApolloClient, InMemoryCache } from '@apollo/client';

// Components
import AppRoutes from './AppRoutes'

// Config
import config from './config'

// Apollo Client configuration
const client = new ApolloClient({
  uri: config.apiUrl,
  cache: new InMemoryCache()
});

render(
  <ApolloProvider client={client}>
    <AppRoutes />
 </ApolloProvider>
, document.querySelector('#root'))
```

基本上，我们正在传递`config.apiUrl`，这是 GraphQL Playground 正在运行的地方（`http://localhost:5000/graphql`），然后用`ApolloProvider`组件包装我们的`AppRoutes`组件。

## 创建我们的应用程序路由

我们将使用`react-router-dom`来创建我们的应用程序路由。让我们在`/frontend/src/AppRoutes.tsx`中创建所需的代码：

```jsx
// Dependencies
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom'

// Components
import HomePage from './pages/home'
import DashboardPage from './pages/dashboard'
import LoginPage from './pages/login'
import Error404 from './pages/error404'

const AppRoutes = () => (
  <Router>
 <Switch>
 <Route path="/" component={HomePage} exact />
      <Route path="/dashboard" component={DashboardPage} exact />
      <Route path="/login" component={LoginPage} exact />
      <Route component={Error404} />
 </Switch>
 </Router>
)pag

export default AppRoutes
```

正如您所看到的，我们正在为我们的路由添加一些页面，比如`HomePage`，`DashboardPage`（受保护），和`LoginPage`。如果用户尝试访问不同的 URL，那么我们将显示一个`Error404`组件。我们将在下一节创建这些页面。

## 创建我们的页面

`Home`页面应该位于`/frontend/src/pages/home.tsx`：

```jsx
const Page = () => (
  <div className="home">
    <h1>Home</h1>

    <ul>
 <li><a href="/dashboard">Go to Dashboard</a></li>
 </ul>
 </div>
)

export default Page
```

`Dashboard`页面应该位于`/frontend/src/pages/dashboard.tsx`：

```jsx
// Components
import DashboardLayout from '../components/dashboard/DashboardLayout'

// Contexts
import UserProvider from '../contexts/user'

const Page = () => (
  <UserProvider>
 <DashboardLayout />
 </UserProvider>
)

export default Page
```

`Login`页面应该位于`/frontend/src/pages/login.tsx`：

```jsx
// Dependencies
import { FC, ReactElement } from 'react'
import { isBrowser } from '@contentpi/lib'

// Contexts
import UserProvider from '../contexts/user'

// Components
import LoginLayout from '../components/users/LoginLayout'

interface IProps {
  currentUrl: string
}

const Page: FC<IProps> = ({
  currentUrl = isBrowser() ? window.location.search.replace
    ('?redirectTo=', '') :''}): ReactElement => (
  <UserProvider page="login">
 <LoginLayout currentUrl={currentUrl} />
  </UserProvider>
)

export default Page
```

最后，我们需要创建我们的`Error404`页面（`/frontend/src/pages/error404.tsx`）：

```jsx
const Page = () => (
  <div className="error404">
 <h1>Error404</h1>
 </div>
)

export default Page
```

我们快要完成了。这个谜题的最后一块是创建`Login`和`Dashboard`组件。我们将在下一节完成。

## 创建我们的登录组件

我为我们的登录和仪表板创建了一些基本组件。当然，它们的样式可以改进，但让我们看看它们是如何工作的，以及我们的登录系统将会是什么样子。

您需要创建的第一个文件是`LoginLayout.tsx`，位于`/frontend/src/components/users/LoginLayout.tsx`：

```jsx
// Dependencies
import { redirectTo } from '@contentpi/lib'
import { FC, ReactElement, useContext, useEffect } from 'react'

// Contexts
import { UserContext } from '../../contexts/user'

// Components
import Login from './Login'

// Interfaces
interface IProps {
  currentUrl: string
}

const Layout: FC<IProps> = ({ currentUrl }): ReactElement => {
  const { login } = useContext(UserContext)

  return (
    <Login login={login} currentUrl={currentUrl} />
  )
}

export default Layout
```

布局文件很好，当我们想要为我们的组件添加特定的布局时。它也很适合从上下文中消费数据并将数据或函数作为 props 传递。

我们的`Login`组件应该像这样（`/frontend/src/components/users/Login.tsx`）：

```jsx
// Dependencies
import { FC, ReactElement, useState, ChangeEvent } from 'react'
import { redirectTo } from '@contentpi/lib'

// Interfaces
import { IUser } from '../../types'

// Styles
import { StyledLogin } from './Login.styled'

interface IProps {
  login(input: any): any
  currentUrl: string
}

const Login: FC<IProps> = ({ login, currentUrl }) => {
  // States
  const [values, setValues] = useState({
    email: '',
    password: ''
  })
  const [errorMessage, setErrorMessage] = useState('')
  const [invalidLogin, setInvalidLogin] = useState(false)

  // Methods
  const onChange = (e: ChangeEvent<HTMLInputElement>): void => {
    const {
      target: { name, value }
    } = e

    if (name) {
      setValues((prevValues: any) => ({
        ...prevValues,
        [name]: value
      }))
    }
  }

  const handleSubmit = async (user: IUser): Promise<void> => {
    // Here we execute the login mutation
    const response = await login(user)

    if (response.error) {
      // If the login is invalid...
      setInvalidLogin(true)
      setErrorMessage(response.message)
    } else {
      // If the login is correct...
      redirectTo(currentUrl || '/')
    }
  }

  return (
    <>
      <StyledLogin>
        <div className="wrapper">
          {invalidLogin && <div className="alert">{errorMessage}</div>}
          <div className="form">
            <p>
              <input
                autoComplete="off"
                type="email"
                className="email"
                name="email"
                placeholder="Email"
                onChange={onChange}
                value={values.email}
              />
            </p>

            <p>
              <input
                autoComplete="off"
                type="password"
                className="password"
                name="password"
                placeholder="Password"
                onChange={onChange}
                value={values.password}
              />
            </p>

            <div className="actions">
              <button name="login" onClick={(): Promise<void> => 
 handleSubmit(values)}>
                Login
              </button>
            </div>
          </div>
        </div>
      </StyledLogin>
    </>
  )
}

export default Login
```

我们将在下一节创建`Dashboard`组件。

## 创建我们的仪表板组件

现在，让我们创建我们的`Dashboard`组件。第一个应该是`DashboardLayout.tsx`文件，位于`/frontend/src/components/dashboard/DashboardLayout.tsx`：

```jsx
// Dependencies
import { FC, ReactElement, useContext } from 'react'

// Contexts
import { UserContext } from '../../contexts/user'

// Components
import Dashboard from './Dashboard'

const Layout: FC = () => {
  const { connectedUser } = useContext(UserContext)

  // We only render the Dashboard if the user is connected
  if (connectedUser) {
    return (
      <Dashboard connectedUser={connectedUser} />
    )
  }

  return <div />
}

export default Layout
```

这就是我们如何保护我们的`Dashboard`页面，只允许连接的用户。现在，让我们在`/frontend/src/components/dashboard/Dashboard.tsx`中创建我们的`Dashboard`组件：

```jsx
interface IProps {
  connectedUser: any
}

const Dashboard = ({ connectedUser }) => (
  <div className="dashboard">
    <h1>Welcome, {connectedUser.username}!</h1>

    <ul>
 <li><a href="/logout">Logout</a></li>
    </ul>
 </div>
)

export default Dashboard
```

有了这个，我们就完成了！我们将在下一节测试登录系统。

## 测试我们的登录系统

如果您正确地按照前面的部分进行了操作，那么您应该能够成功运行登录系统。为此，我们需要打开三个终端：

+   在第一个终端中，您需要运行您的后端项目（`npm run dev`）。

+   在你的前端项目中的第二个中，你需要构建你的项目 (`npm run build`)。

+   在最后一个中，你需要在前端项目中运行节点服务器 (`npm run dev`)。

当你第一次打开 `http://localhost:3000` 时，你应该能够看到主页：

![](img/300b98ca-c7dc-4116-9b17-2417f87a0e27.png)

然后，如果你点击  转到仪表板  (`http://localhost:3000/dashboard`) 链接，你将被重定向到 `http://localhost:3000/login?redirectTo=/dashboard`，如下面的截图所示：

![](img/215eb66b-b6f9-4eaa-9070-f49f202e3b27.png)

这是我们的登录表单。如果你尝试用一些虚假的凭据登录，你应该会收到一个错误：

![](img/44a235c3-665c-4d3c-92a5-11de5afec9bc.png)

如果你想查看 GraphQL 请求，你可以在 Chrome 网络选项卡上这样做：

![](img/edf13895-ebe2-4975-8585-97dd94bb7752.png)

在这里，你可以看到你正在执行的查询以及你正在发送的变量（电子邮件和密码）。你可以在  预览  选项卡上看到响应：

![](img/b9ef4f2e-65f3-47ae-9746-822ac1ac45cf.png)

正如你所看到的，我们收到了一个 "无效登录" 的错误消息，这就是为什么我们在我们的  `Login`  组件中呈现它。现在，让我们尝试用正确的帐户连接 (`admin@js.education / 123456`)。 

如果你的登录是正确的，那么你应该被重定向到仪表板，你将看到以下页面：

![](img/a99ef623-b988-4ca3-a9db-4dc8011b4ad7.png)

此外，你可以看一下正在执行以检索用户数据的查询 (`getUserData`)：

![](img/c65ff951-c095-4049-9389-0a603375be0e.png)

在这里，你将看到返回的有效负载：

![](img/fb078044-e451-4e0e-9d62-c0f539471caa.png)

我们正在从访问令牌 (`at`) 中获取用户信息。现在，如果你刷新页面，你应该保持连接到页面。这是因为我们保存了一个包含我们令牌的 cookie：

![](img/c7d2fe96-af44-479f-a81b-5b0587730ea0.png)

现在，让我们尝试通过改变令牌的任意字母来修改 cookie。例如，让我们把前两个字母 (`ey`) 改成 `XX`：

![](img/27a38ecb-0983-426c-82fe-450799d78762.png)

在这里，你将收到用户的空数据。这将使会话失效并再次将你重定向到登录页面：

![](img/5733fbfd-0098-458b-9861-e814f0eca71b.png)

到目前为止，你已经学会了如何在后端实现 GraphQL 以及如何在前端消耗查询和变异。

这个登录系统是我在 YouTube 上做的一个课程的一部分，我在课程中教观众如何从头开始开发一个无头 CMS，所以如果你渴望学到更多，可以在[`www.youtube.com/watch?v=4n1AfD6aV4M`](https://www.youtube.com/watch?v=4n1AfD6aV4M)上查看课程。

# 总结

我真的希望你喜欢阅读这一章，其中包含了关于 GraphQL 以及如何创建 JWT、执行登录和使用 Sequelize 创建模型的大量信息。

现在是时候谈谈数据获取和单向数据流了，这是我们将在下一章中讨论的内容。
