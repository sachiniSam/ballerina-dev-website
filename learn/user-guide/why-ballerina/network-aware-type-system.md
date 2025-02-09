---
layout: ballerina-left-nav-pages-swanlake
title: Network-Aware Type System
description: See how the Ballerina programming language's network-aware type system helps developers work with networked resources in their code.
keywords: ballerina, programming lanaguage, type system, data binding
permalink: /learn/user-guide/why-ballerina/network-aware-type-system/
intro: See how the Ballerina programming language's network-aware type system helps developers work with networked resources in their code.
redirect_from:
- /why/the-network-in-the-language/
- /why/the-network-in-the-language
- /why-ballerina
- /why-ballerina/
- /learn/user-guide/why-ballerina/network-aware-type-system
---

In a programming language, the type system is the foundation for representing data and implementing logic. It provides the means of creating abstractions to the solutions that you provide. While some languages provide basic functionality, others strive to give in-built functionality for specialized domains.

With more services being available in the cloud, the network-distributed programming domain has grown. As the developer is given the added responsibility of working with networked resources in their code, it is critical that the programming language itself aids in this operation. That’s why Ballerina’s network-friendly type system is specialized for this domain.

## Statically Typed and Structural

Ballerina is a statically typed language, which means all the variables are checked at compile-time and only compatible values are assigned. Statically typed languages are generally more robust, easier to debug, and aids in creating better language tooling.

Ballerina’s type system is also structural as opposed to nominal. This means that the type compatibility is identified by considering the structure of the value. This is different from languages like Java, C++, and C# that have nominal type systems where it is bound by the name of the actual type.

### Shapes in Ballerina

Types in Ballerina deal with an abstraction of values that don’t consider storage identity. This abstraction is called a shape. For simple types like int and boolean, there is no difference between a shape and a value because they don’t have a storage identity. To understand the concept of a shape, let’s look at the [`record` type](/learn/by-example/records/) in Ballerina. Because records have storage identity, a reference to the value is stored in the variable rather than storing the actual value. This is comparable to references in Java or pointers in C++.

Here’s an example of a record that stores the state of a door:

```ballerina
type DoorState record {|
    boolean open;
    boolean locked;
|};
```

Now, let’s create some values of the `DoorState` record type:

```ballerina
DoorState v1 = { open: false, locked: true };
DoorState v2 = { open: false, locked: true };
DoorState v3 = { open: false, locked: true };
```

The three variables above all represent a single state of the door being closed and locked. Nonetheless, we have created three different values where each variable is stored in a distinct memory reference. If we ignore the storage identity of these variables, we are left with the representation of the data it has, which is `{ open: false, locked: true }`. This is a single shape of the type `DoorState`.

In this way, there are four possible shapes for DoorState as shown in the variables below:

```ballerina
DoorState ds1 = { open: true, locked: true };
DoorState ds2 = { open: true, locked: false };
DoorState ds3 = { open: false, locked: true };
DoorState ds4 = { open: false, locked: false };
```

A type in Ballerina represents a set of the possible shapes it can have. So any value that belongs to either of the above four shapes will be considered to be of the type `DoorState`.

<img src="/img/why-pages/why-diagram-01.svg"/>
<p class="cCaption">Figure 1: Set of shapes of the type <code>DoorState</code></p>

### Subtypes in Ballerina

Subtyping in Ballerina is semantic. It is defined by means of shapes, where S is a subtype of T, if the shapes representing S are a subset of the shapes representing T. Let’s demonstrate this behavior with a few examples.

The type `boolean` is a simple basic type in Ballerina without a storage identity, so its values become equivalent to its shapes. Therefore, the `boolean` type is defined as having two shapes true and false.

The `boolean` type’s shapes can be defined in set notation as `Sboolean = { true, false }`. This can be visualized as seen in Figure 2 below.

<img src="/img/why-pages/why-diagram-02.svg"/>
<p class="cCaption">Figure 2: Set of shapes of the type <code>boolean</code></p>

Now, according to our subtyping rules, we can derive new types based on the `boolean` type by creating subsets of its shapes. For example, a new type we can create is `boolean_false` where its only supported shape/value would be `false`. The new type is shown in Figure 3 below.

<img src="/img/why-pages/why-diagram-03.svg"/>
<p class="cCaption">Figure 3: Shapes sets of types <code>boolean</code> and <code>boolean_false</code></p>

The new type `boolean_false` can be defined in Ballerina code in the following manner:

```ballerina
type boolean_false false;
```

Here, we are using the value false in defining the new type `boolean_false`. In a more practical scenario, we can provide multiple values as a union when defining new types using the syntax `T1|T2`. A type created with a single value is called a singleton type. This new type can be used in the code in the following way.

```ballerina
boolean_false bv1 = false;
boolean bv2 = true;
bv2 = bv1;
```

As you can see, `bv1` of type `boolean_false` can be assigned to `bv2` of type boolean because `bv1`’s type is a subset of `bv2`’s type. In simple terms, all the values that can be held in the variable `bv1` can be held in the variable `bv2`, thus the assignment is possible.

We have now seen how Ballerina’s subtyping works in relation to simple types. Let’s take a look at creating subtypes of records by revisiting our `DoorState` scenario. Here, we will create a new type `EmergencyDoorState`, where the `locked` field has to always have the value `false`. The resultant types and their shapes can be seen below in Figure 4.

<img src="/img/why-pages/why-diagram-04.svg"/>
<p class="cCaption">Figure 4: Shapes sets of types <code>DoorState</code> and <code>EmergencyDoorState</code></p>

The type definition of `EmergencyDoorState` type is shown below:

```ballerina
type EmergencyDoorState record {|
    boolean open;
    boolean_false locked = false;
|};
```

In the above type, we have modified the field `locked` to be of type `boolean_false`, which allows its only value to be `false`. In this type definition, default values in Ballerina records have been used, wherein the absence of an explicit value provided by the user, the default value mentioned here will be used.

In this manner, the type `EmergencyDoorState` can only have the shapes `{ open: true, locked: false }` and `{ open: false, locked: false }`. These two elements make it a subset of the `DoorState` shapes set, thus `EmergencyDoorState` is a subtype of `DoorState`.

The following code snippet shows a sample usage of the `EmergencyDoorState` type:

```ballerina
EmergencyDoorState eds1 = { open: true };
DoorState eds2 = eds1;
io:println("Door - Open: ", eds2.open, " Locked: ", eds2.locked);
```

### Benefits of a Structural Type System
A structural type system proves beneficial when you have multiple systems interacting with each other since data exchange and type compatibilities can be resolved easier. Let’s dive into a Ballerina <a href="/learn/by-example/query-expression.html">integrated query</a> example, which shows this behavior.

```ballerina
type Result record {|
    string name;
    string college;
    int grade;
|};
...
Result[] results = from var person in persons
                       let int lgrade = (grades[person.name] ?: 0),
                       where lgrade > 75
                           string targetCollege = "Stanford"
                           select {
                               name: person.name,
                               college: targetCollege,
                               grade: lgrade };
```

In the example above, we filter records from a list and create a new value using the `select` clause. Its structure is defined dynamically at that time and the values are created. These values are assigned to an array of `Result` records, which is possible because the generated values are structurally compatible with the `Result` record type.

In situations such as above, a separate system from our core application may be generating values to be consumed by us. In these cases, instead of worrying about sharing the code for the data type definitions, you can simply concentrate on the compatibility of the data in order to ensure interoperability.

## Open-by-Default

Ballerina’s open-by-default concept is tied around the <a href="https://en.wikipedia.org/wiki/Robustness_principle">Robustness Principle</a>. This means that we should design network-aware programs to accept all the data that is sent to you and make the best effort to understand it. But when sending data, you should make the best effort to conform to the standard protocols that were agreed upon beforehand. This strategy makes sure we have the best chance of interacting with different systems in a reliable manner.

The main facilitator of this in the type system is the open record concept in Ballerina. So far we have looked at closed records. Let’s take a look at a record type to represent the details of a person.

### Get Started

The code snippet below shows a call to a simple HTTP GET request endpoint:

```ballerina
type Ethnicity "Asian"|"White"|"African American"|"Native American/Alaskan Native"|"Pacific Islander"|"Native Hawaiian";
 
type Person record {
    string name;
    int birthYear;  
    boolean married = false;
    Ethnicity ethnicity?;
};
```

Here, the type `Person` is an open record type defined with an `inclusive-record-type-descriptor` by using the `"{"` and `"}"` delimiters. This is the default behavior when defining record types in Ballerina. An open record is not limited to the fields that are declared in the record type, so we can set additional fields that are not explicitly mentioned in the type definition.

The earlier `DoorState` record type was defined explicitly as a closed record type with an exclusive-record-type-descriptor by using the `"{|"` and `"|}"` delimiters. Therefore, we were able to list out all the possible shapes in the `DoorState` type. If this type was defined as an open record, we would have an infinite number of shapes since `DoorState` values can have any arbitrary field set in the code.

The `Person` record type above has an <a href="/learn/by-example/record-optional-fields.html">optional field</a> `ethnicity` (denoted by the suffix `"?"`). This means the field value of `ethnicity` of a `Person` record can be skipped without setting a value. Later on, this field can be accessed using the `"?."` operator, which would return a value of type `Ethnicity?`, which is equivalent to the union type `Ethnicity|()`. In Ballerina, the nil value and the type is represented by `()`.

Let’s create a new type `Student`, which will be a subtype of the `Person` type.

```ballerina
type Student record {
    string name;
    int birthYear;  
    boolean married;
    Ethnicity ethnicity?;
    string college;
};
```

The `Student` type defined above has an extra field college of type `string` compared to the `Person` type. All the possible shapes in the `Student` type are included in the set of shapes in `Person` as well. This is possible because the `Person` type is an open record. If we make the `Person` type a closed record, `Student` will no longer be a subtype of `Person`.

Sample usage of the above types is shown below:

```balleirna
public function main() {
   Student s1 = { name: "Tom", birthYear: 1990, married: false,
                  college: "Yale" };
   Student s2 = { name: "Anne", birthYear: 1988, married: true,
                  ethnicity: "White", college: "Harvard" };
   Person p1 = s1;
   Ethnicity? eth = p1?.ethnicity;
 
   if eth != () {
       io:println("P1's ethnicity: ", eth);
   } else {
       io:println("P1's ethnicity: N/A");
   }
 
   Person p2 = s2;
   eth = p2?.ethnicity;
 
   if eth != () {
       io:println("P2's ethnicity: ", eth);
   } else {
       io:println("P2's ethnicity: N/A");
   }
 
   io:println(p2);
}
```

```bash
$ bal run sample.bal 
P1's ethnicity: N/A
P2's ethnicity: White
name=Anne birthYear=1988 married=true ethnicity=White college=Harvard
```

## Network Communication with Data Binding

The type system features for records in Ballerina can be used when implementing <a href="/learn/by-example/http-data-binding.html">data binding</a> operations with structural validation, data types handling, and payload passthrough operations. The functionality will be demonstrated using an HTTP service in Ballerina:

```ballerina
http:Client asianRecordsDB = check new ("http://example.com/");

service / on new http:Listener(8080) {

    resource function get 'record(@http:Payload {} Person entry) returns error? {
        if entry?.ethnicity == "Asian" {
            io:println("Asian Record: ", entry);
            json jsonPayload = check entry.cloneWithType(json);
            _ = check asianRecordsDB->post("/store", <@untainted>jsonPayload);
        } else {
            io:println("Non-Asian Record: ", entry);
        }
    }

}
```

```bash
$ bal run sample.bal 
[ballerina/http] started HTTP/WS listener 0.0.0.0:8080
```

### Test Scenarios

<table class="table cCodeTable" >
                                 <tr>
                                    <th class="cDescription">Description</th>
                                    <th class="cCodeCol">Request</th>
                                    <th class="cCodeCol">Output</th>
                                 </tr>
                                <tr>
                                    <td>
                                       A request is sent without setting the <code>married</code> field. Since this field has a default value in the <code>Person</code> type, the value is automatically populated to that. The <code>ethnicity</code> field is not set since it is marked as optional.
                                    </td>
                                    <td>
                                       <span class="cTableCode">curl -d '{ "name": "John Little",  "birthYear": 1855 }' http://localhost:8080/record</span>
                                    </td>
                                    <td>
                                        <span class="cTableCode">Non-Asian Record: name=John Little birthYear=1855 married=false</span>
                                    </td>
                                 </tr> 
                                 <tr>
                                    <td>A request is sent with a string value given for the integer field <code>birthYear</code>. The service validates the value for the field and the request fails.</td>
                                   <td>
                                       <span class="cTableCode">curl -d '{ "name": "John Little",  "birthYear": "1855" }' http://localhost:8080/record</span>
                                    </td>
                                    <td>
                                        <span class="cTableCode">data binding failed: error {ballerina/lang.typedesc}ConversionError message='map '<' json > ' value cannot be converted to 'Person'</span>
                                    </td>
                                 </tr> 
                              <tr>
                                    <td>A request is sent with the optional <code>ethnicity</code> field also set. </td>
                                    <td>
                                       <span class="cTableCode">curl -d '{ "name": "Sunil Perera",  "birthYear": 1950, "married": true, "ethnicity": "Asian" }' http://localhost:8080/record</span>
                                    </td>
                                    <td>
                                       <span class="cTableCode">Asian Record: name=Sunil Perera birthYear=1950 married=true ethnicity=Asian
</span>
                                    </td>
                                 </tr>
                                   <tr>
                                    <td>A request is sent with a non-existing value of the <code>Ethnicity</code> union type. This is validated by the service and the request fails.</td>
                                    <td>
                                       <span class="cTableCode">curl -d '{ "name": "Tim Kern",  "birthYear": 1995, "ethnicity": "Japanese", "country": "Japan", "zipcode": "98101" }' http://localhost:8080/record</span>
                                    </td>
                                    <td>
                                       <span class="cTableCode">data binding failed: error {ballerina/lang.typedesc}ConversionError message='map< json >' value cannot be converted to 'Person'</span>
</td>
                                 </tr>
                                  <tr>
                                    <td>A request is sent with additional fields not explicitly mentioned in the <code>Person</code> type. Since <code>Person</code> is an open record type, the service accepts and makes these extra fields available to be passed through to other systems, e.g. a forwarding service.</td>
                                    <td>
                                       <span class="cTableCode">curl -d '{ "name": "Tim Kern",  "birthYear": 1995, "ethnicity": "Asian", "country": "Japan", "zipcode": "98101" }' http://localhost:8080/record</span>
                                    </td>
                                    <td>
                                       <span class="cTableCode">Asian Record: name=Tim Kern birthYear=1995 married=false ethnicity=Asian country=Japan zipcode=98101</span>
                                    </td>
                                 </tr> 
                              </table>


<!--<div class="row cBallerina-io-Gray-row cContentRows">
   <div class="container">
      <div class="row">
         <div class="col-xs-12 col-sm-12 col-md-12 col-lg-12 cBallerina-io-Home-Middle-col">
            <div class="col-xs-12 col-sm-12" style="padding: 0;">
               <div class="cBlallerina-io-docs-content-container">
                  <div class="wy-nav-content">
                     <div class="rst-content">
                        <div role="main">
                           <div class="section">
                              <p>In a programming language, the type system is the foundation for representing data and implementing logic. It provides the means of creating abstractions to the solutions that you provide. While some languages provide basic functionality, others strive to give in-built functionality for specialized domains.</p>
                              <p>
                                 With more services being available in the cloud, the network-distributed programming domain has grown. As the developer is given the added responsibility of working with networked resources in their code, it is critical that the programming language itself aids in this operation. That’s why Ballerina’s network-friendly type system is specialized for this domain.
                              </p>
                              <h2 id="services">Statically Typed and Structural</h2>
                              <p>Ballerina is a statically typed language, which means all the variables are checked at compile-time and only compatible values are assigned. Statically typed languages are generally more robust, easier to debug, and aids in creating better language tooling.</p>
                              <p>
                                 Ballerina’s type system is also structural as opposed to nominal. This means that the type compatibility is identified by considering the structure of the value. This is different from languages like Java, C++, and C# that have nominal type systems where it is bound by the name of the actual type.
                              </p>
                              <h3 >Shapes in Ballerina</h3>
                              <p>
                                 Types in Ballerina deal with an abstraction of values that don’t consider storage identity. This abstraction is called a <i>shape</i>. For simple types like <code>int</code> and <code>boolean</code>, there is no difference between a shape and a value because they don’t have a storage identity. To understand the concept of a shape, let’s look at the <a href="/learn/by-example/records"><code>record</code> type</a> in Ballerina. Because records have storage identity, a reference to the value is stored in the variable rather than storing the actual value. This is comparable to references in Java or pointers in C++.
                              </p>
                              <p>Here’s an example of a record that stores the state of a door:</p>
                              <pre class="ballerina-pre-wrapper"><code class="language-ballerina cBasicCode hljs">type DoorState record {|
    boolean open;
    boolean locked;
|};
</code></pre>
                              <p>Now let’s create some values of the <code>DoorState</code> record type:</p>
                              <pre class="ballerina-pre-wrapper"><code class="language-ballerina cBasicCode hljs">DoorState v1 = { open: false, locked: true };
DoorState v2 = { open: false, locked: true };
DoorState v3 = { open: false, locked: true };
</code></pre>
                              <p>The three variables above all represent a single state of the door being closed and locked. Nonetheless, we have created three different values where each variable is stored in a distinct memory reference. If we ignore the storage identity of these variables, we are left with the representation of the data it has, which is <code>{ open: false, locked: true }</code>. This is a single shape of the type <code>DoorState</code>.</p>
                              <p>In this way, there are four possible shapes for DoorState as shown in the variables below:</p>
                              <pre class="ballerina-pre-wrapper"><code class="language-ballerina cBasicCode hljs">DoorState ds1 = { open: true, locked: true };
DoorState ds2 = { open: true, locked: false };
DoorState ds3 = { open: false, locked: true };
DoorState ds4 = { open: false, locked: false };
</code></pre>
                              <p>A type in Ballerina represents a set of the possible shapes it can have. So any value that belongs to either of the above four shapes will be considered to be of the type <code>DoorState</code>. </p>
                             <div class="cInlineImage"> <img src="/img/why-pages/why-diagram-01.svg"/></div>
                              <p class="cCaption">Figure 1: Set of shapes of the type <code>DoorState</code></p>
                              <h3>Subtypes in Ballerina</h3>
                              <p>Subtyping in Ballerina is semantic. It is defined by means of shapes, where S is a subtype of T, if the shapes representing S are a subset of the shapes representing T. Let’s demonstrate this behavior with a few examples. </p>
                              <p>The type <code>boolean</code> is a simple basic type in Ballerina without a storage identity, so its values become equivalent to its shapes. Therefore the <code>boolean</code> type is defined as having two shapes true and false. </p>
                              <p>The <code>boolean</code> type’s shapes can be defined in set notation as S<sub>boolean</sub> = { true, false }. This can be visualized as seen in Figure 2 below. </p>
                               <div class="cInlineImage"> <img src="/img/why-pages/why-diagram-02.svg"/></div>
                              <p class="cCaption">Figure 2: Set of shapes of the type <code>boolean</code></p>
                              <p>Now, according to our subtyping rules, we can derive new types based on the <code>boolean</code> type by creating subsets of its shapes. For example, a new type we can create is <code>boolean_false</code> where its only supported shape/value would be <code>false</code>. The new type is shown in Figure 3 below.</p>
                              <div class="cInlineImage"> <img src="/img/why-pages/why-diagram-03.svg"/></div>
                              <p class="cCaption">Figure 3: Shapes sets of types <code>boolean</code> and <code>boolean_false</code></p>
                              <p>The new type <code>boolean_false</code> can be defined in Ballerina code in the following manner: </p>
                              <pre class="ballerina-pre-wrapper"><code class="language-ballerina cBasicCode hljs">type boolean_false false;
</code></pre>
                              <p>Here, we are using the value <code>false</code> in defining the new type <code>boolean_false</code>. In a more practical scenario, we can provide multiple values as a union when defining new types using the syntax <code>T1|T2</code>. A type created with a single value is called a singleton type. This new type can be used in the code in the following way. </p>
                              <pre class="highlight"><code class="cBasicCode hljs">boolean_false bv1 = false;
boolean bv2 = true;
bv2 = bv1;
</code></pre>
                              <p>As you can see, <code>bv1</code> of type <code>boolean_false</code> can be assigned to <code>bv2</code> of type <code>boolean</code> because <code>bv1</code>’s type is a subset of <code>bv2</code>’s type. In simple terms, all the values that can be held in the variable <code>bv1</code> can be held in the variable <code>bv2</code>, thus the assignment is possible.</p>
                              <p>We have now seen how Ballerina’s subtyping works in relation to simple types. Let’s take a look at creating subtypes of records by revisiting our <code>DoorState</code> scenario. Here, we will create a new type <code>EmergencyDoorState</code>, where the <code>locked</code> field has to always have the value <code>false</code>. The resultant types and their shapes can be seen below in Figure 4.</p>
                              <div class="cInlineImage"> <img src="/img/why-pages/why-diagram-04.svg"/></div>
                              <p class="cCaption">Figure 4: Shapes sets of types <code>DoorState</code> and <code>EmergencyDoorState</code></p>
                              <p>
                                 The type definition of <code>EmergencyDoorState</code> type is shown below:
                              </p>
                              <pre class="ballerina-pre-wrapper"><code class="language-ballerina cBasicCode hljs">type EmergencyDoorState record {|
    boolean open;
    boolean_false locked = false;
|};
</code></pre>
                              <p>In the above type, we have modified the field <code>locked</code> to be of type <code>boolean_false</code>, which allows its only value to be <code>false</code>. In this type definition, default values in Ballerina records have been used, wherein the absence of an explicit value provided by the user, the default value mentioned here will be used.</p>
                              <p>In this manner, the type <code>EmergencyDoorState</code> can only have the shapes <code>{ open: true, locked: false }</code> and <code>{ open: false, locked: false }</code>. These two elements make it a subset of the <code>DoorState</code> shapes set, thus <code>EmergencyDoorState</code> is a subtype of <code>DoorState</code>. </p>
                              <p>The following code snippet shows a sample usage of the <code>EmergencyDoorState</code> type: </p>
                              <pre class="ballerina-pre-wrapper"><code class="language-ballerina cBasicCode hljs">EmergencyDoorState eds1 = { open: true };
DoorState eds2 = eds1;
io:println("Door - Open: ", eds2.open, " Locked: ", eds2.locked);
</code></pre>
                              <h3>Benefits of a Structural Type System</h3>
                              <p>A structural type system proves beneficial when you have multiple systems interacting with each other since data exchange and type compatibilities can be resolved easier. Let’s dive into a Ballerina 
                                 <a href="/learn/by-example/query-expression.html">integrated query</a> example, which shows this behavior.
                              </p>
                              <pre class="ballerina-pre-wrapper"><code class="language-ballerina cBasicCode hljs">type Result record {|
    string name;
    string college;
    int grade;
|};
...
Result[] results = from var person in persons
                       let int lgrade = (grades[person.name] ?: 0),
                       where lgrade > 75
                           string targetCollege = "Stanford"
                           select {
                               name: person.name,
                               college: targetCollege,
                               grade: lgrade };
</code></pre>
                              <p>In the example above, we filter records from a list and create a new value using the <code>select</code> clause. Its structure is defined dynamically at that time and the values are created. These values are assigned to an array of <code>Result</code> records, which is possible because the generated values are structurally compatible with the <code>Result</code> record type. </p>
                              <p>
                                 In situations such as above, a separate system from our core application may be generating values to be consumed by us. In these cases, instead of worrying about sharing the code for the data type definitions, you can simply concentrate on the compatibility of the data in order to ensure interoperability. 
                              </p>
                           </div>
                        </div>
                     </div>
                  </div>
               </div>
            </div>
         </div>
      </div>
   </div>
</div>
<div class="row cBallerina-io-Gray-row cGray cContentRows">
   <div class="container">
      <div class="row">
         <div class="col-xs-12 col-sm-12 col-md-12 col-lg-12 cBallerina-io-Home-Middle-col">
            <div class="col-xs-12 col-sm-12" style="padding: 0;">
               <div class="cBlallerina-io-docs-content-container">
                  <div class="wy-nav-content">
                     <div class="rst-content">
                        <div role="main">
                           <div class="section">
                              <h2 id="async-network-protocol">Open-by-Default</h2>
                              <p>Ballerina’s open-by-default concept is tied around the <a href="https://en.wikipedia.org/wiki/Robustness_principle">Robustness Principle</a>. This means that we should design network-aware programs to accept all the data that is sent to you and make the best effort to understand it. But when sending data, you should make the best effort to conform to the standard protocols that were agreed upon beforehand. This strategy makes sure we have the best chance of interacting with different systems in a reliable manner. </p>
                              <p>
                                 The main facilitator of this in the type system is the open record concept in Ballerina. So far we have looked at closed records. Let’s take a look at a record type to represent the details of a person.
                              </p>
                              <h3 >Get Started</h3>
                              <p>The code snippet below shows a call to a simple HTTP GET request endpoint:</p>
                              <pre class="ballerina-pre-wrapper"><code class="language-ballerina cBasicCode hljs">type Ethnicity "Asian"|"White"|"African American"|"Native American/Alaskan Native"|"Pacific Islander"|"Native Hawaiian";
 
type Person record {
    string name;
    int birthYear;  
    boolean married = false;
    Ethnicity ethnicity?;
};
                              </code></pre>
                              <p>Here, the type <code>Person</code> is an open record type defined with an inclusive-record-type-descriptor by using the "<code>{</code>" and "<code>}</code>" delimiters. This is the default behavior when defining record types in Ballerina. An open record is not limited to the fields that are declared in the record type, so we can set additional fields that are not explicitly mentioned in the type definition.</p>
                              <p>The earlier <code>DoorState</code> record type was defined explicitly as a closed record type with an exclusive-record-type-descriptor by using the "<code>{|</code>" and "<code>|}</code>" delimiters. Therefore we were able to list out all the possible shapes in the <code>DoorState</code> type. If this type was defined as an open record, we would have an infinite number of shapes since <code>DoorState</code> values can have any arbitrary field set in the code.</p>
                              <p>The <code>Person</code> record type above has an <a href="/learn/by-example/record-optional-fields.html">optional field</a> <code>ethnicity</code> (denoted by the suffix "<code>?</code>"). This means the field value of <code>ethnicity</code> of a <code>Person</code> record can be skipped without setting a value. Later on, this field can be accessed using the "<code>?.</code>" operator, which would return a value of type <code>Ethnicity?</code>, which is equivalent to the union type <code>Ethnicity|()</code>. In Ballerina, the nil value and the type is represented by <code>()</code>.</p>
                              <p>Let’s create a new type <code>Student</code>, which will be a subtype of the <code>Person</code> type. </p>
                              <pre class="ballerina-pre-wrapper"><code class="language-ballerina cBasicCode hljs">type Student record {
    string name;
    int birthYear;  
    boolean married;
    Ethnicity ethnicity?;
    string college;
};
</code></pre>
                              <p>The <code>Student</code> type defined above has an extra field <code>college</code> of type <code>string</code> compared to the <code>Person</code> type. All the possible shapes in the <code>Student</code> type are included in the set of shapes in <code>Person</code> as well. This is possible because the <code>Person</code> type is an open record. If we make the <code>Person</code> type a closed record, <code>Student</code> will no longer be a subtype of <code>Person</code>.</p>
                              <p>Sample usage of the above types is shown below:</p>
                              <pre class="ballerina-pre-wrapper"><code class="language-ballerina cBasicCode hljs">public function main() {
   Student s1 = { name: "Tom", birthYear: 1990, married: false,
                  college: "Yale" };
   Student s2 = { name: "Anne", birthYear: 1988, married: true,
                  ethnicity: "White", college: "Harvard" };
   Person p1 = s1;
   Ethnicity? eth = p1?.ethnicity;
 
   if eth != () {
       io:println("P1's ethnicity: ", eth);
   } else {
       io:println("P1's ethnicity: N/A");
   }
 
   Person p2 = s2;
   eth = p2?.ethnicity;
 
   if eth != () {
       io:println("P2's ethnicity: ", eth);
   } else {
       io:println("P2's ethnicity: N/A");
   }
 
   io:println(p2);
}
</code></pre>
                              <pre class="ballerina-pre-wrapper"><code class="language-ballerina cBasicCode hljs">$ bal run sample.bal 
P1's ethnicity: N/A
P2's ethnicity: White
name=Anne birthYear=1988 married=true ethnicity=White college=Harvard
</code></pre>
                              <h3>Network Communication with Data Binding</h3>
                              <p>
                                 The type system features for records in Ballerina can be used when implementing <a href="/learn/by-example/http-data-binding.html">data binding</a> operations with structural validation, data types handling, and payload passthrough operations. The functionality will be demonstrated using an HTTP service in Ballerina: 
                              </p>
                              <pre class="ballerina-pre-wrapper"><code class="language-ballerina cBasicCode hljs">http:Client asianRecordsDB = check new ("http://example.com/");

service / on new http:Listener(8080) {

    resource function get 'record(@http:Payload {} Person entry) returns error? {
        if entry?.ethnicity == "Asian" {
            io:println("Asian Record: ", entry);
            json jsonPayload = check entry.cloneWithType(json);
            _ = check asianRecordsDB->post("/store", <@untainted>jsonPayload);
        } else {
            io:println("Non-Asian Record: ", entry);
        }
    }

}
</code></pre>
                              <pre class="ballerina-pre-wrapper"><code class="language-ballerina cBasicCode hljs">$ bal run sample.bal 
[ballerina/http] started HTTP/WS listener 0.0.0.0:8080

</code></pre>
                              <h4>Test Scenarios</h4>
                              <table class="table cCodeTable" >
                                 <tr>
                                    <th class="cDescription">Description</th>
                                    <th class="cCodeCol">Request</th>
                                    <th class="cCodeCol">Output</th>
                                 </tr>
                                <tr>
                                    <td>
                                       A request is sent without setting the <code>married</code> field. Since this field has a default value in the <code>Person</code> type, the value is automatically populated to that. The <code>ethnicity</code> field is not set since it is marked as optional.
                                    </td>
                                    <td>
                                       <span class="cTableCode">curl -d '{ "name": "John Little",  "birthYear": 1855 }' http://localhost:8080/record</span>
                                    </td>
                                    <td>
                                        <span class="cTableCode">Non-Asian Record: name=John Little birthYear=1855 married=false</span>
                                    </td>
                                 </tr> 
                                 <tr>
                                    <td>A request is sent with a string value given for the integer field <code>birthYear</code>. The service validates the value for the field and the request fails.</td>
                                   <td>
                                       <span class="cTableCode">curl -d '{ "name": "John Little",  "birthYear": "1855" }' http://localhost:8080/record</span>
                                    </td>
                                    <td>
                                        <span class="cTableCode">data binding failed: error {ballerina/lang.typedesc}ConversionError message='map '<' json > ' value cannot be converted to 'Person'</span>
                                    </td>
                                 </tr> 
                              <tr>
                                    <td>A request is sent with the optional <code>ethnicity</code> field also set. </td>
                                    <td>
                                       <span class="cTableCode">curl -d '{ "name": "Sunil Perera",  "birthYear": 1950, "married": true, "ethnicity": "Asian" }' http://localhost:8080/record</span>
                                    </td>
                                    <td>
                                       <span class="cTableCode">Asian Record: name=Sunil Perera birthYear=1950 married=true ethnicity=Asian
</span>
                                    </td>
                                 </tr>
                                   <tr>
                                    <td>A request is sent with a non-existing value of the <code>Ethnicity</code> union type. This is validated by the service and the request fails.</td>
                                    <td>
                                       <span class="cTableCode">curl -d '{ "name": "Tim Kern",  "birthYear": 1995, "ethnicity": "Japanese", "country": "Japan", "zipcode": "98101" }' http://localhost:8080/record</span>
                                    </td>
                                    <td>
                                       <span class="cTableCode">data binding failed: error {ballerina/lang.typedesc}ConversionError message='map< json >' value cannot be converted to 'Person'</span>
</td>
                                 </tr>
                                  <tr>
                                    <td>A request is sent with additional fields not explicitly mentioned in the <code>Person</code> type. Since <code>Person</code> is an open record type, the service accepts and makes these extra fields available to be passed through to other systems, e.g. a forwarding service.</td>
                                    <td>
                                       <span class="cTableCode">curl -d '{ "name": "Tim Kern",  "birthYear": 1995, "ethnicity": "Asian", "country": "Japan", "zipcode": "98101" }' http://localhost:8080/record</span>
                                    </td>
                                    <td>
                                       <span class="cTableCode">Asian Record: name=Tim Kern birthYear=1995 married=false ethnicity=Asian country=Japan zipcode=98101</span>
                                    </td>
                                 </tr> 
                              </table>
                           </div>
                        </div>
                     </div>
                  </div>
               </div>
            </div>
         </div>
      </div>
   </div>
</div>-->

<style>
.nav > li.cVersionItem {
    display: none !important;
}
.cBalleinaBreadcrumbs li:nth-child(3) , .cBalleinaBreadcrumbs li:nth-child(2) {
   display:none !important;
}
</style>
