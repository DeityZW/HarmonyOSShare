# Core Database Operation Solutions in HarmonyOS

#### I. Native API Deep Dive

HarmonyOS provides two native database solutions: ​Relational Database (RDB)​​ and ​Object-Relational Mapping (ORM)​, both built on SQLite.

##### 1. Relational Database (RDB)

​Core API Implementation:

```
// Database Initialization (TypeScript)  
import relationalStore from '@ohos.data.relationalStore';  
  
const config = {  
  name: 'user.db',  
  securityLevel: relationalStore.SecurityLevel.S1  
};  
  
async function initDB() {  
  const store = await relationalStore.getRdbStore(config);  
  await store.executeSql(  
    'CREATE TABLE IF NOT EXISTS user (id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT)'  
  );  
}  
  
// Batch Insert with Transactions  
async function batchInsert(users: string[]) {  
  const store = await relationalStore.getRdbStore(config);  
  await store.beginTransaction();  
  try {  
    users.forEach(name => {  
      store.insert('user', { name });  
    });  
    await store.commit();  
  } catch (e) {  
    await store.rollback();  
  }  
}  
```

​Features:

* ​Advantages:

  * Direct SQL control for complex queries (e.g., multi-table joins)

  * Full ACID transaction support

  * Distributed sync capability (API 18+)

* ​Limitations:

  * Manual SQL injection prevention

  * Strict single-writer/multi-reader rules for multi-threading

##### 2. Object-Relational Mapping (ORM)

​Code Example:

```
// Entity Definition  
@Entity(tableName = 'product')  
class Product extends OrmObject {  
  @PrimaryKey(autoGenerate = true)  
  private id: number;  
  @Column(String) name: string;  
}  
  
// Database Operations  
const ormContext = ormCreateDb();  
const product = new Product();  
product.name = 'Smart Watch';  
await ormContext.insert(product);  
const results = await ormContext.query(  
  ormContext.where(Product).equalTo('name', 'Smart Watch')  
);  
```

​Advantages:

* 50% less boilerplate code (no manual SQL)

* Compile-time type safety

* Automatic object serialization

​**​*

#### II. Third-Party Libraries Comparison

##### 1. ObjectBox (Community Recommendation)

​Integration:

```
// package.json  
"dependencies": {  
  "com.objectbox.objectbox": "1.6.0"  
}  
```

​Code Example:

```
// Entity Definition  
@Entity  
class User {  
  @Id(autoincrement = true)  
  id: number;  
  name: string;  
}  
  
// Operations  
const boxStore = MyObjectBox.builder().build();  
const userBox = boxStore.boxFor(User.class);  
userBox.put(new User("Zhang San"));  
const users = userBox.query().build().find();  
```

​Performance:

* Insertion: 1,800 entries/sec vs. Native API's 1,200

* Query Latency: 18ms vs. 25ms

​Use Cases:

* High-frequency IoT logging

* Complex object relationships

##### 2. HSQLDB (Lightweight Option)

​Features:

* Pure Java implementation (no native dependencies)

* In-memory database support

```
import { HSQLDatabase } from '@ohos.hsqldb';  
const db = new HSQLDatabase();  
await db.connect('jdbc:hsqldb:mem:test');  
await db.execute('CREATE TABLE session (id INT PRIMARY KEY)');  
```

​Limitations:

* Limited transaction support (row-level locking only)

* No distributed deployment

​**​*

#### III. Performance & Scenario Comparison

| Aspect        | Native API          | ORM Framework      | ObjectBox |
| :------------ | :------------------ | :----------------- | :-------- |
| Development   | ★★☆ (SQL required)  | ★★★★ (Type-safe)   | ★★★☆      |
| Speed         | ★★★★ (Direct)       | ★★★ (ORM overhead) | ★★★★☆     |
| Learning      | ★★★ (SQL needed)    | ★★☆ (Annotations)  | ★★★☆      |
| Complex Query | ★★★★ (JOIN support) | ★★☆ (Generator)    | ★★★☆      |
| Distributed   | ★★★★ (API 18+)      | ❌                  | ❌         |

​**​*

#### IV. Selection Guide

1. ​Native API:

   * Ideal for complex data models needing distributed sync

   * Use @Transaction for batch operations

2. ​ORM Framework:

   * Top choice for small/medium projects (TypeScript decorators)

   * Note: Manual Date type conversions

3. ​Third-Party:

   * ​ObjectBox: IoT local storage

   * ​HSQLDB: Memory-light apps

​Optimizations:

* Use executeBatch() instead of loops (+3x throughput)

* Specify columns in queries to avoid full scans

* Enable WAL mode (PRAGMA journal_mode=WAL) for concurrency

