# GraphQL ISO Date

[![npm version](https://badge.fury.io/js/graphql-iso-date.svg)](http://badge.fury.io/js/graphql-iso-date)
[![Build Status](https://travis-ci.org/excitement-engineer/graphql-iso-date.svg?branch=master)](https://travis-ci.org/excitement-engineer/graphql-iso-date)

GraphQL ISO Date is a set of [RFC 3339](./rfc3339.txt) compliant date/time scalar types to be used with [graphQL.js](https://github.com/graphql/graphql-js).

> RFC 3339 *"defines a date and time format for use in Internet
protocols that is a profile of the ISO 8601 standard for
representation of dates and times using the Gregorian calendar."*

> **Date and Time on the Internet: Timestamps, July 2002.**

A basic understanding of [GraphQL](http://facebook.github.io/graphql/) and of the [graphQL.js](https://github.com/graphql/graphql-js) implementation is needed to provide context for this library.

This repository contains the following scalars:

- `Date`: A date string, such as 2007-12-03.
- `Time`: A time string at UTC, such as 10:15:30.000Z
- `DateTime`: A date-time string at UTC, such as 2007-12-03T10:15:30.000Z.

## Getting started

Install `graphql-iso-date` using yarn

```sh
yarn add graphql-iso-date
```

Or using npm

```sh
npm install --save graphql-iso-date
```

GraphQL-ISO-Date exposes 3 different date/time scalars that can be used in combination with [GraphQL.js](https://github.com/graphql/graphql-js). Let's build a simple schema using the scalars included in this library and execute a query:

```js
import {
  graphql,
  GraphQLObjectType,
  GraphQLSchema,
} from 'graphql';

import {
  GraphQLDate,
  GraphQLTime,
  GraphQLDateTime
} from 'graphql-iso-date';

const schema = new GraphQLSchema({
  query: new GraphQLObjectType({
    name: 'Query',
    fields: {
      birthdate: {
        type: GraphQLDate,
        //resolver can take a Date or date string.
        resolve: () => new Date(1991, 11, 24);
      },
      openingNYSE: {
        type: GraphQLTime,
        //resolver can take a Date or time string.
        resolve: () => new Date(Date.UTC(2017, 0, 10, 14, 30);
      },
      instant: {
        type: GraphQLDateTime,
        // resolver can take Date, date-time string or Unix timestamp (number).
        resolve: () => new Date(Date.UTC(2017, 0, 10, 21, 33, 15, 233));
      }
    }
  })
});

const query = `
  {
    birthdate
    openingNYSE
    instant
  }
`;

graphql(schema, query).then(result => {

    // Prints
    // {
    //   data: {
    //     birthdate: '1991-12-24',
    //     openingNYSE: '14:30:00.000Z',
    //     instant: '2017-01-10T21:33:15.233Z'
    //   }
    // }
    console.log(result);
});
```

## Examples

This project includes several examples in the folder `/examples` explaining how to use the various scalars.

Run the examples by downloading this project and running the following commands:

Install dependencies using yarn

```sh
yarn
```

Or npm

```sh
npm install
```

Run the examples

```
npm run examples
```

## Scalars

This section provides a detailed description of each of the scalars.

 > A reference is made to `coercion` in the description below. For further clarification on the meaning of this term, please refer to the GraphQL [spec](http://facebook.github.io/graphql/#sec-Scalars).

### Date

A date string, such as 2007-12-03, compliant with the *"full-date"* format outlined in section 5.6 of the RFC 3339 profile of the ISO 8601 standard for representation of dates and times using the Gregorian calendar.

This scalar is a description of the date, as used for birthdays for example. It cannot represent an instant on the time-line.

The value "2nd of January 2015" is represented as date string "2015-01-02".

**Result Coercion**

Javascript Date instances are coerced to an RFC 3339 compliant date string date string. Invalid Date instances raise a field error.

**Input Coercion**

When expected as an input type, only valid RFC 3339 compliant date strings are accepted. All other input values raise a query error indicating an incorrect type.

### Time

A time string at UTC, such as 10:15:30.000Z, compliant with the `full-time` format outlined in section 5.6 of the RFC 3339 profile of the ISO 8601 standard for representation of dates and times using the Gregorian calendar.

This scalar is a description of a time instant such as the opening bell of the New York Stock Exchange for example. It cannot represent an exact instant on the time-line.

This scalar ignores leap seconds (thereby assuming that a minute constitutes of 59 seconds), in this respect it diverges from the RFC 3339 profile.

Fractional seconds are represented to millisecond precision. When RFC compliant time string does not have a fractional second, it is implied to be zero milliseconds. For example, the time string "14:10:20Z" is implied to be "14:10:20.000Z".

Where an RFC 3339 compliant time string has a time-zone other than UTC, it is shifted to UTC. For example, the time string "14:10:20.987+01:00" is shifted to "13:10:20.987Z".

**Result Coercion**

Javascript Date instances are coerced to an RFC 3339 compliant time string by extracting the UTC time part. Invalid Date instances raise a field error.

**Input Coercion**

When expected as an input type, only valid RFC 3339 compliant time strings are accepted. All other input values raise a query error indicating an incorrect type.

### DateTime

A date-time string at UTC, such as 2007-12-03T10:15:30.000Z, compliant with the `date-time` format outlined in section 5.6 of the RFC 3339 profile of the ISO 8601 standard for representation of dates and times using the Gregorian calendar.

This scalar is a description of an exact instant on the time-line such as the instant that a user account was created for example.

DateTime is a concatenation of the `Date` and `Time` scalars in the form `<Date>T<Time>`, this means that the same conventions are used as described in the sections associated with these scalars.

**Result Coercion**

Javascript Date instances and Unix timestamps (represented as 32-bit signed integers) are coerced to RFC 3339 compliant date-time strings. Invalid Date instances raise a field error.

**Input Coercion**

When expected as an input type, only valid RFC 3339 compliant date-time strings are accepted. All other input values raise a query error indicating an incorrect type.
