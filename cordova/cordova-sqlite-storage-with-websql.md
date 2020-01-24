# How to use `cordova-sqlite-storage` with WebSQL fallback

**Issue**: `cordova-sqlite-storage` plugin works only on a (Android/iOS) device - hard to develop and test

**Solution**: Use WebSQL fallback while running `ionic serve`

Downside: WebSQL is discontinued and works only in Google Chrome.

## Install steps

```bash
ionic start myApp blank --type=angular
cd myApp
ionic cordova plugin add cordova-sqlite-storage
npm install @ionic-native/sqlite
npm install @types/websql
ionic g service services/db
```

## DB Service

Copy contents to file: `src\app\services\db.service.ts`

This service provides a unified API to run SQL queries on sqlite/webSQL backend.

```ts
import { Injectable } from '@angular/core';
import { Platform } from '@ionic/angular';
import { SQLite, SQLiteObject } from '@ionic-native/sqlite/ngx';

// workaround for: @types/websql is not recognized by 'ionic build'
declare var window: any;

@Injectable({
  providedIn: 'root'
})
export class DbService {
  private DBNAME = 'mydatabase.db';

  private database: SQLiteObject;

  private CREATE_TABLE1 = 'CREATE TABLE IF NOT EXISTS table1 (id INTEGER PRIMARY KEY, name TEXT)';
  private CREATE_TABLE2 = 'CREATE TABLE IF NOT EXISTS table2 (id INTEGER PRIMARY KEY, name TEXT)';

  private DROP_TABLE1 = 'DROP TABLE IF EXISTS table1';
  private DROP_TABLE2 = 'DROP TABLE IF EXISTS table2';

  constructor(
    private platform: Platform, 
    private sqlite: SQLite
  ) {
    this.platform.ready().then(() => {
      this.createDB()
      .then((db: SQLiteObject) => {
        this.database = db;
        this.createTables();
      })
      .catch(err => {
        console.error('createDB() catch ', err);
      })
    });
  }

  createDB(): Promise<any> {
    const dbname = this.DBNAME;
    if (this.platform.is('cordova')) {
      console.debug('db service - createDB sqlite')
      return this.sqlite.create({
        name: dbname,
        location: 'default'
      });
    } else {
      // WebSQL fallback - supported by webkit only :(
      console.debug('db service - createDB websql')
      return new Promise(function (resolve, reject) {
        const db = window.openDatabase(dbname, '1.0', 'default', 10 * 1024 * 1024);
        resolve(db);
      });
    }
  }

  createTables(): Promise<any> {
    console.debug('db service - create tables')
    return this.execSQL(this.CREATE_TABLE1, []).then(() => {
      return this.execSQL(this.CREATE_TABLE2, []);
    }).catch(() => {});
  }

  dropTables(): Promise<any> {
    console.debug('db service - drop tables')
    return new Promise((resolve, reject) => {
      this.execSQL(this.DROP_TABLE1, []).then(resolve, reject);
    }).then(() => {
      return this.execSQL(this.DROP_TABLE2, []);
    });
  }

  execSQL(query: string, params: Array<string|number>): Promise<any> {
    if (params == null) {
      params = [];
    }
    if (this.platform.is('cordova')) {
      console.debug('db service - sqlite execSQL', query, params);
      return this.database.executeSql(query, params);
    } else {
      return new Promise((resolve, reject) => {
        this.database.transaction((tx) => {
          console.debug('db service - websql execSQL', query, params);
          tx.executeSql(query, params, (tx, results) => {
            resolve(results);
          }, reject);
        });
      });
    }
  }
}
```