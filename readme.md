





## create zen class database

### use zen_class

 insert the all collection i needed (users,codekata,attendance,topics,tasks,company_drives,mentors) 



## 1). Find all the topics and tasks which are thought in the month of October

db.topics.aggregate([
    {
     $lookup:{
         from:"tasks",
         localField:"id",
         foreignField:"id",
         as:"task_information"
     }
   },
     { $match:{ $and:[{ $and:[
                     {topic_date:{$gt:new Date ("2020-10-01")}},
                     {topic_date:{$lt:new Date ("2020-10-30")}}
                     ] } ,
                 {$and:[ {"task_information.due_date":{$gt:new Date ("2020-10-01")}},
                         {"task_information.due_date":{$lt:new Date ("2020-10-30")}}
                         ]} ]}
     }    
   ]);


## 2). Find all the company drives which appeared between 15 oct-2020 and 31-oct-2020

db.company_drives.find({
    $and: [
      { drive_date: { $lte: new Date("2020-10-31") } },
      { drive_date: { $gte: new Date("2020-10-15") } },
    ],
  }).toArray();

## 3). Find all the company drives and students who are appeared for the placement.

  db.company_drives.aggregate([
    {
        $lookup: {
              from:"users",
              localField:"id",
              foreignField:"id",
              as :"user_information"
             }
    },
    {
        $project:{
            _id:0,
            "user_information.name":1,
            company_name:1,
            drive_date:1,
            "user_information.email":1,
            "user_information.user_id":1
        }
    }
]).toArray();

## 4). Find the number of problems solved by the user in codekata

db.codekata.aggregate([
    {
      $lookup: {
        from: "users",
        localField: "id",
        foreignField: "id",
        as: "user_information",
      },
    },
    {
      $project: {
        _id: 0,
        user_id: 1,
        "user_information.name": 1,
        "user_information.email": 1,
        no_of_problems_solved: 1,
      },
    },
  ]).toArray();
  

## 5). Find all the mentors with who has the mentee's count more than 15

db.mentors.find({
    mentee_count:{ $gt:15 }
  }).toArray();

## 6). Find the number of users who are absent and task is not submitted  between 15 oct-2020 and 31-oct-2020

db.tasks.aggregate([
    {
      $lookup: {
        from: "attendance",
        localField: "id",
        foreignField: "id",
        as: "attendance",
      },
    },
    {
      $match: {
        $and: [{ submitted: false }, { "attendance.present": false }],
      },
    },
  ]).toArray();



##  Question no 1
### Exploring the Differences between HTTP/1.1 and HTTP/2
In the rapidly evolving landscape of web development, the protocols that govern the way information is transferred between clients and servers have a significant impact on the performance and user experience of websites. Two major versions of the Hypertext Transfer Protocol (HTTP) that have shaped the modern web are HTTP/1.1 and HTTP/2. Let's delve into the key differences between these two protocols and understand how they have revolutionized web communication.

Introduction to HTTP/1.1 and HTTP/2
HTTP/1.1, the stalwart of web communication, was first introduced in 1997. It ushered in an era of information exchange between clients (typically web browsers) and servers, allowing the internet to flourish. However, as the complexity and demands of websites grew over time, the limitations of HTTP/1.1 became more apparent, leading to the development of HTTP/2, which was standardized in 2015.

Multiplexing and Concurrency
One of the most remarkable improvements that HTTP/2 brings to the table is multiplexing. Unlike its predecessor, which relied on multiple connections to transfer data, HTTP/2 enables multiplexing over a single connection. This means that multiple requests and responses can be interwoven within the same connection, eliminating the need to wait for a response before sending the next request. This drastic improvement in concurrency enhances the efficiency of data transmission.

Header Compression
The headers of HTTP requests and responses contain important information about the data being transferred. In HTTP/1.1, these headers were sent in plaintext, leading to redundancy and increased latency. In contrast, HTTP/2 introduces header compression, where headers are efficiently encoded and decoded, reducing overhead and resulting in faster data transfers. This optimization contributes to a more streamlined communication process.

Server Push
HTTP/2 introduces a feature known as server push, which allows the server to proactively send resources to the client before they are explicitly requested. For instance, if a client requests an HTML page, the server can push associated stylesheets and scripts to the client's cache. This preemptive action eliminates the need for the client to send additional requests for these resources, leading to faster page load times.

Prioritization
While HTTP/1.1 treated all requests with the same level of importance, HTTP/2 allows for the prioritization of requests. This means that critical resources can be assigned a higher priority, ensuring that they are fetched and delivered to the client sooner. This feature greatly enhances the perceived speed of web pages as important content is prioritized over less critical resources.

Binary Protocol
HTTP/1.1 utilized a text-based protocol, which was human-readable but not the most efficient for machines to process. HTTP/2, on the other hand, employs a binary protocol. This change reduces the complexity of parsing data and enables faster and more reliable communication between clients 
and servers.

Backward Compatibility
A significant advantage of HTTP/2 is its seamless integration with existing systems. Servers that support HTTP/2 can still communicate with clients using HTTP/1.1, ensuring a smooth transition and backward compatibility for older systems and browsers.

TLS Usage
Although not mandatory, HTTP/2 implementations often require the use of Transport Layer Security (TLS), which provides encryption and enhances security. While TLS was recommended for HTTP/1.1, it is more widely adopted in the context of HTTP/2, contributing to a safer browsing experience.

Resource Loading
HTTP/1.1 often necessitated resource bundling to reduce the number of connections established. However, this approach could lead to inefficient use of resources. HTTP/2's multiplexing capabilities eliminate the need for bundling, enabling more granular resource loading and improving overall page load times.

Impact on SEO
Website speed is a critical factor in search engine optimization (SEO). Given HTTP/2's inherent performance improvements, it indirectly affects SEO rankings positively. Search engines, including Google, consider page speed when determining search result rankings.

Browser and Server Support
HTTP/2 enjoys broad support across modern web browsers and servers. While most current browsers support HTTP/2, older browser versions might not fully capitalize on its advantages. As for servers, major ones like Apache, NGINX, and IIS have incorporated HTTP/2 support.

Migration Considerations
Migrating from HTTP/1.1 to HTTP/2 requires careful planning and configuration adjustments. The benefits are substantial, but a well-executed migration strategy is essential to avoid potential issues during the transition.

Real-World Performance Improvements
Numerous performance benchmarks have illustrated the significant improvements that HTTP/2 brings to the table. Websites utilizing HTTP/2 exhibit faster load times, improved user engagement, and an enhanced overall browsing experience.

Future Evolution
HTTP/2 sets the stage for the future of web communication protocols. Its adaptable architecture allows for the incorporation of new features and improvements, ensuring that it remains relevant and effective in meeting the evolving demands of the digital age.

Difference between HTTP1.1 vs HTTP2

HTTP 1.1
HTTP 2
It  used to work in textual format.
It works on the binary protocol.
There is head of line blocking that blocks all the requests behind it until it doesn’t get its all resources.
It allows multiplexing so one TCP connection is required for multiple requests.
It uses requests resource Inlining for use getting multiple pages
It uses PUSH frame by server that collects all multiple pages 
It compresses data by itself.
It uses HPACK for data compression.


Conclusion
In the grand scheme of web development, the transition from HTTP/1.1 to HTTP/2 represents a paradigm shift. The advancements in multiplexing, header compression, prioritization, and other areas redefine the way we interact with the internet. While HTTP/2 has brought about monumental improvements, the journey doesn't end here. With HTTP/3 and beyond on the horizon, the trajectory of web communication is poised to reach even greater heights, offering users faster, safer, and more efficient online experiences.










Question no 2

Unveiling Objects and Their Internal Representation in JavaScript
In the realm of programming, objects are like the building blocks that allow developers to create complex structures and models within their code. In JavaScript, a versatile and widely used programming language, objects play a pivotal role. Let's delve into the world of objects and explore how they are internally represented in JavaScript.

 Introduction to Objects in JavaScript
Objects are fundamental data structures in JavaScript, used to represent real-world entities, concepts, or abstractions in code. They consist of properties and methods, which store data and define behavior, respectively. Properties are key-value pairs that hold various types of data, such as strings, numbers, arrays, or even other objects. Methods are functions associated with objects, enabling them to perform actions or computations.

  Object Creation
In JavaScript, objects can be created in various ways. The most common method is using object literals:

```javascript
const person = {
  firstName: "John",
  lastName: "Doe",
  age: 30,
  greet: function() {
    console.log(`Hello, I'm ${this.firstName} ${this.lastName}.`);
  }
};
```

 Internal Representation of Objects
Internally, objects in JavaScript are implemented as collections of key-value pairs, where keys are strings (or Symbols) and values can be of any data type. The underlying structure is often referred to as a "hash table" or "dictionary." Each object is associated with a prototype, which acts as a blueprint containing shared properties and methods that can be accessed by instances of that object.

 Reference-based Nature
Unlike primitive data types (such as strings and numbers), objects in JavaScript are reference types. This means that when you assign an object to a variable, you are actually assigning a reference to that object's location in memory, rather than duplicating its entire content. This has implications for how objects are copied and compared.

 Property Access

To access properties and methods of an object, you can use the dot notation or square brackets:

```javascript
console.log(person.firstName); // Using dot notation
console.log(person["age"]);    // Using square brackets
person.greet();                // Calling the greet method
```

  Object Prototypes and Inheritance

Prototypes are a central concept in JavaScript's object-oriented model. Each object has a prototype, which serves as a fallback for properties and methods that are not directly defined on the object itself. This forms the basis of inheritance, allowing objects to inherit properties and methods from their prototypes.

  Creating Objects with Constructors

In addition to object literals, you can create objects using constructor functions. These functions, when invoked with the `new` keyword, create new instances of objects with shared properties and methods:

```javascript
function Person(firstName, lastName, age) {
  this.firstName = firstName;
  this.lastName = lastName;
  this.age = age;
}

const john = new Person("John", "Doe", 30);
```

 ES6 Classes and Objects

ES6 introduced the `class` syntax, which provides a more structured way to define object blueprints and encapsulate behavior. Behind the scenes, classes are still based on prototypes, but they offer a more intuitive syntax:

```javascript
class Person {
  constructor(firstName, lastName, age) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = age;
  }
  
  greet() {
    console.log(`Hello, I'm ${this.firstName} ${this.lastName}.`);
  }
}

const jane = new Person("Jane", "Smith", 25);
```

 Garbage Collection
Since objects are allocated memory in JavaScript, it's essential to manage memory efficiently. JavaScript engines employ automatic garbage collection to identify and reclaim memory that is no longer reachable by the program. Objects that are no longer referenced are considered eligible for garbage collection.

 Conclusion
Objects are a cornerstone of JavaScript's expressive power, enabling developers to model and manipulate complex data structures with ease. Their internal representation as key-value pairs, prototypes, and references to memory locations shapes the way JavaScript applications are structured and executed. Understanding the intricacies of objects and their underlying mechanisms empowers developers to create efficient, maintainable, and sophisticated code. So, the next time you work with objects in JavaScript, remember that they are more than just data containers—they are the conduits through which your code interacts with the digital world.






