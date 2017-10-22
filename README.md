# postgres-driver
pg-promise sugar using proper types for easy (async) crud-commands. Add custom methods.

## Installation
this module is not listed on npm though it can be installed using it:
```shell
npm install git+https://github.com/jakoblorz/postgres-driver.git --save
```

## Usage
To use the methods, just create a new set class which is extending
the exported database class:
```typescript

// import the module
import { Database } from "postgres-driver";

// define a table definition (this is a
// IAccount interface where each key can be undefined)
interface IAccountTableDefinition {
    id?: string;
    name?: string;
    age?: number;
}

// actual account interface
interface IAccount extends IAccountTableDefinition {
    id: string;
    name: string;
    age: number;
}

// create a set class
class AccountSet extends Database<IAccountTableDefinition, "accounts"> {
    constructor(){
        super("postgres://user:password@host:port/database");
    }

    // define a custom method though this example
    // could be done using the createResource method
    public async createNewAccount(name: string) {

        // get the database interface - this is a pg-promise
        // database object
        const connection = await this.connect();

        // insert the dataset
        await connection.none(
            "INSERT INTO accounts (id, name, age) VALUES ($1, $2, $3)", [0, name, 18]);
    }
}

// use the class
const accounts = new AccountSet();

// invoke the read resource method
accounts.readResource<IAccount, { id: number }>("accounts", { id: 0 })
    .then((account: IAccount | null) => console.log(account === null ? 
        "" : JSON.stringify(account))); // { id: 0, name: "name", age: 18 }
```