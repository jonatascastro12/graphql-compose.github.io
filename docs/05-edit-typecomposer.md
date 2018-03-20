---
id: edit-typecomposer
title: Edit TypeComposer
---

Sooner or later you need to edit the types. There are functions to get and set one or more fields, aswell as you can get data from the fields too

### Adding fields

```js
UserTC.addFields({
  fullName: {
    type: 'String',
    resolve: (source) => `${source.firstName} ${source.lastName}`,
    projection: { firstName: true, lastName: true },
  }
});

UserTC.addFields({
  age: {
    type: 'Int',
    // other field config properties
  }
  ageShort: 'Int', // just type
  complex: {
    type: `type Address {
      city: String
      street: String
    }`,
    // other field config properties
  },
  complexShort: `type AddressShort {
    city: String
    street: String
  }`
});
```

### Adding relations with other TypeComposers

Assume that `ArticleTC` is generated by [graphql-compose-mongoose](https://github.com/nodkz/graphql-compose-mongoose). So let take generated `findMany` resolver from `ArticleTC` and connect/relate it with user data:

```js
UserTC.addRelation('last10Articles', {
  resolver: () => ArticleTC.getResolver('findMany'),
  prepareArgs: {
    filter: source => ({ userId: `${source._id}` }), // calculate `filter` argument
    limit: 10, // set value to `limit` argument
    sort: { _id: -1 }, // set `sort` argument
    skip: null, // remove `skip` argument
  },
  projection: { _id: true },
});
```

Read more about relations [here](04-relations.md).

### Remove fields

```js
UserTC.removeField('email');
```

**_REMEMBER: This will remove the field in all objects where this TC is used_**

### Field functions

```js
getFieldNames(): string[]
getField(fieldName: string): ?GraphQLFieldConfig
getFields(): GraphQLFieldConfigMap
setFields(fields: GraphQLFieldConfigMap): void
setField(fieldName: string, fieldConfig: GraphQLFieldConfig)
addFields(newFields: GraphQLFieldConfigMap): void
hasField(fieldName: string): boolean
removeField(fieldNameOrArray: string | Array<string>): void
removeOtherFields(keepFields: Array<string>); // will remove all other fields
getFieldType(fieldName: string): GraphQLOutputType | void
getFieldArgs(fieldName: string): ?GraphQLFieldConfigArgumentMap
getFieldArg(fieldName: string, argName: string): ?GraphQLArgumentConfig
extendField(fieldName: string, parialFieldConfig: GraphQLFieldConfig): GraphQLFieldConfig)
deprecateFields(fieldMap: { [fieldName:string]: deprecation reason string } );
```