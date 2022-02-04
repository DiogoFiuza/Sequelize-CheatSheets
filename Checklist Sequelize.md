<div align="center">
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/sequelize/sequelize-original.svg" height='100'/>
<h1>Checklist Sequelize</h1>
</div>



## Sumário

- [Objetivo](#Objetivo)
- [Guia](#Guia)
  - [1-Instalando as dependências](#1---Instalando-as-dependências)
  - [2 - Sequelize](#2---Sequelize)
  - [3 - Model](#3---Model)
  - [4 - Migration](#4---Migration)
  - [5 - Seed](#5---Seed)

---
<br>


# Objetivo 

O objetivo desse checklist é servir como um documento de consulta para facilitar na elaboração de aplicações com o sequelize.
<br>

# Guia

<b>

## 1 - Instalando as dependências:
---

<br>

### Iniciar a aplicação
~~~bash
$ npm init -y
~~~

### Instalar o sequelize
~~~bash
$ npm install sequelize
~~~

### Instalar a CLI
~~~bash
$ npm install sequelize-cli
~~~

### Instalar o `mysql2`
~~~bash
$ npm install mysql2
~~~

### (Bônus) Atalho para fazer as 3 instalações anteriores em um único comando
~~~bash
$ npm install sequelize sequelize-cli mysql2 
~~~
<b>

## 2 - Sequelize
---
### Inicialização de um projeto sequelize
- Comando para criar as pastas models, migrations, seeders e config.

~~~bash
$ npx sequelize-cli init
~~~

### Configurar o arquivo config.json gerado pelo init do CLI

~~~JSON
{
  "development": {
    "username": "root",
    "password": "",
    "database": "db",
    "host": "127.0.0.1",
    "dialect": "mysql"
  }

  // No resto do arquivo você vai encontrar as convenções para conectar o Sequelize em outros ambientes
}
~~~

### Criar o banco usando o CLI do Sequelize

~~~bash
$ npx sequelize db:create
~~~
<br>

## 3 - Model
---
<br>

### Criação de um arquivo model
~~~bash
$ npx sequelize model:generate --name NomeDoModel --attributes nomeDoAtributo:string
~~~

### Alterar o model do formato de classe:

~~~JavaScript
'use strict';

const {
  Model
} = require('sequelize');
module.exports = (sequelize, DataTypes) => {
  class User extends Model {
    /**
     * Helper method for defining associations.
     * This method is not a part of Sequelize lifecycle.
     * The `models/index` file will call this method automatically.
     */
    static associate(models) {
      // define association here
    }
  };
  User.init({
    fullName: DataTypes.STRING
  }, {
    sequelize,
    modelName: 'User',
  });
  return User;
};
~~~

- Para função:

~~~JavaScript
const User = (sequelize, DataTypes) => {
  const User = sequelize.define("User", {
    fullName: DataTypes.STRING,
  }, {
    underscored: true
  });
  return User;
};

module.exports = User;
~~~

### Alterar as migrations, caso seja necessário

~~~JavaScript
'use strict';

module.exports = {
  up: async (queryInterface, Sequelize) => {
    await queryInterface.createTable('Users', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      fullName: {
        type: Sequelize.STRING
      },
      createdAt: {
        allowNull: false,
        type: Sequelize.DATE
      },
      updatedAt: {
        allowNull: false,
        type: Sequelize.DATE
      }
    });
  },
  down: async (queryInterface, Sequelize) => {
    await queryInterface.dropTable('Users');
  }
};
~~~

<b>

## 4 - Migration
---

### Criação de um arquivo migration
~~~bash
$ npx sequelize migration:generate --name add-column-phone-table-users
~~~

### Execução dos arquivos migration
~~~bash
$ npx sequelize db:migrate
~~~
### Reverter a migration:
~~~bash
$ npx sequelize db:migrate:undo
~~~
### Caso seja necessária a modificação de alguma tabela, você pode rodar um comando para gerar uma nova migration e então fazer as alterações que você precisar:


~~~JavaScript
'use strict';

module.exports = {
  up: async (queryInterface, Sequelize) => {
   await queryInterface.addColumn('Users', 'phone_num', {
     type: Sequelize.STRING,
   });
  },

  down: async (queryInterface, Sequelize) => {
    await queryInterface.removeColumn('Users', 'phone_num');
  }
};
~~~


### Em seguida rodar o comando para executar a nova migration:

~~~bash
$ npx sequelize db:migrate
~~~

### E alterar o model para incluit a nova coluna:

~~~JavaScript
const User = (sequelize, DataTypes) => {
  const User = sequelize.define("User", {
    fullName: DataTypes.STRING,
    phone_num: DataTypes.STRING,
  });

  return User;
};

module.exports = User;
~~~

<b>

## 5 - Seed
---


### Criação de um arquivo seed
~~~bash
$ npx sequelize seed:generate --name users
~~~

### Adicionar as informações que serão colocadas no banco

~~~JavaScript
'use strict';

module.exports = {
  up: async (queryInterface, Sequelize) => queryInterface.bulkInsert('Users',
    [
      {
        fullName: 'Leonardo',
        phone_num: '999999999',
        createdAt: Sequelize.literal('CURRENT_TIMESTAMP'),
        updatedAt: Sequelize.literal('CURRENT_TIMESTAMP'),
      },
      {
        fullName: 'Eduardo',
        phone_num: '999999998',
        createdAt: Sequelize.literal('CURRENT_TIMESTAMP'),
        updatedAt: Sequelize.literal('CURRENT_TIMESTAMP'),
      },
    ], {}),

  down: async (queryInterface) => queryInterface.bulkDelete('Users', null, {}),
};
~~~

### Executar o seed

~~~bash
$ npx sequelize db:seed:all
~~~

### E para reverter:

~~~bash
$ npx sequelize db:seed:undo:all
~~~
