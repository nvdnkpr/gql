[![Build Status](https://travis-ci.org/genomejs/gql.png?branch=master)](https://travis-ci.org/genomejs/gql)

[![NPM version](https://badge.fury.io/js/gql.png)](http://badge.fury.io/js/gql)

## Information

<table>
<tr> 
<td>Package</td><td>gql</td>
</tr>
<tr>
<td>Description</td>
<td>Query language for interpreting genome SNPs</td>
</tr>
<tr>
<td>Node Version</td>
<td>>= 0.4</td>
</tr>
</table>

## Compatibility

This query language is for use with the genomejs JSON format. See the [dna2json](https://github.com/genomejs/dna2json) repository for more information.

## Usage

This example will create a query that determines if the gender is male.

```javascript
var gql = require('gql');

var query = gql.query();
query.exact("rs2032651", "D");
query.has("rs2032651", "A");
query.has("rs9341296", "C");
query.has("rs9341296", "T");
query.has("rs13304168", "C");
query.has("rs13304168", "T");
query.has("rs1118473", "A");
query.has("rs1118473", "G");
query.has("rs150173", "A");
query.has("rs150173", "C");
query.has("rs16980426", "G");
query.has("rs16980426", "T");
query.or(query.exact("rs1558843", "C"), query.exact("rs1558843", "A"));
query.or(query.exact("rs17222419", "C"), query.exact("rs17222419", "T"));
```

### Processing SNPs

#### Simple async

```javascript
var async = require('async');
var snps = require('./genome.json');
async.forEach(snps, query.process.bind(query), function(){
  console.log(query.matches().length, "matches for genoset 144");
  console.log(query.percentage(), "chance that gene matches");
});
```

#### Streaming

```javascript
var async = require('async');
var JSONStream = require('JSONStream');
var fs = require('fs');

var jsonStream = fs.createReadStream('./genome.json');

var queryStream = query.stream();

jsonStream
  .pipe(JSONStream.parse('*'))
  .pipe(queryStream);

queryStream.on('end', function(){
  console.log(query.matches().length, "matches for genoset 144");
  console.log(query.percentage(), "chance that gene matches");
});
```

## API

### query()

Creates a new GQL chainable query

#### exact(id, genotype)

Evaluates to true if this was the only allele observed

```javascript
q.exact("rs2032651", "D"); // will only match genotype D
q.exact("rs2032651", "AT"); // will only match genotype AT
```

#### has(id, genotype)

Evaluates to true if the allele was observed at all

```javascript
q.has("rs2032651", "A"); // will match GA, GAT, AB, etc.
```

#### exists(id)

Evaluates to true if any allele has been observed

```javascript
q.exists("rs2032651");
```

#### doesntExist(id)

Evaluates to true if no allele has been observed

```javascript
q.doesntExist("rs2032651");
```

#### add(fn)

This will let you add a custom condition. Callback signature is `(error, isMatch)`

```javascript
q.add(function(snp, cb){
  cb(null, (snp.id === "rswhatever" && snp.genotype === "A"))
});
```

#### process(snp[, cb])

Runs the SNP through all of the conditions. If a condition is evaluated as true it will not be evaluated again and a match for it will be registered.

#### stream()

Turns the query into a through stream. Sugar for `es.map(q.process);`

#### percentage()

Returns expecting/matches

#### matches()

Returns condition matches so far

#### unmatched()

Returns all conditions yet to be matched

#### completed()

Returns true or false if all conditions have been matches
## LICENSE

(MIT License)

Copyright (c) 2013 Fractal <contact@wearefractal.com>

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
