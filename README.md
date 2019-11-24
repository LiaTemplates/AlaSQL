<!--
author:   André Dietrich

email:    andre.dietrich@ovgu.de

version:  0.2.2

language: en

narrator: US English Female

logo:     https://jquery-plugins.net/image/plugin/alasql-javascript-sql-database-library.png

comment:  Macros for executing SQL code snippets with AlaSQL in LiaScript.

script:   https://cdnjs.cloudflare.com/ajax/libs/alasql/0.4.11/alasql-worker.min.js
          https://cdnjs.cloudflare.com/ajax/libs/alasql/0.4.11/alasql.min.js

attribute: [AlaSQL](https://alasql.org)
           by [Andrey Gershun](agershun@gmail.com)
           & [Mathias Rangel Wulff](m@rawu.dk)
           is licensed under [MIT](https://opensource.org/licenses/MIT)


script:  https://cdnjs.cloudflare.com/ajax/libs/PapaParse/4.6.1/papaparse.min.js

attribute: [PapaParse](https://www.papaparse.com)
           by [Matthew Holt](https://twitter.com/mholt6)
           is licensed under [MIT](https://opensource.org/licenses/MIT)

@AlaSQL.eval
<script>
try {
  JSON.stringify(alasql(`@input`), null, 3);
} catch(e) {
  let error = new LiaError(e.message, 1);
  try {
    let log = e.message.match(/.*line (\d):.*\n.*\n.*\n(.*)/);
    error.add_detail(0, e.name+": "+log[2], "error", log[1] -1 , 0);
  } catch(e) {}
  throw error ;
}
</script>
@end


@AlaSQL.eval_with_csv
<script>
let data = Papa.parse(`@input(1)`, {header: true});

let error = "";
if(data.errors.length != 0) {
    error = JSON.stringify(data.errors, null, 3)+"\n";
}

try {
  error += JSON.stringify(alasql(`@input`, [data.data]), null, 3);
} catch(e) {
  let error = new LiaError(e.message, 1);
  try {
    let log = e.message.match(/.*line (\d):.*\n.*\n.*\n(.*)/);
    error.add_detail(0, e.name+": "+log[2], "error", log[1] -1 , 0);
  } catch(e) {}
  throw error ;
}
</script>
@end

-->

# AlaSQL - Template

This is a template for developing interactive SQL courses with LiaScript and
[AlaSQL](http://alasql.org).

__Try it on LiaScript:__

https://liascript.github.io/course/?https://raw.githubusercontent.com/liaTemplates/AlaSQL/master/README.md

__See the project on Github:__

https://github.com/liaTemplates/AlaSQL


                         --{{1}}--
There are three ways to use this template. The easiest way is to use the
`import` statement and the url of the raw text-file of the master branch or any
other branch or version. But you can also copy the required functionionality
directly into the header of your Markdown document, see therefor the
[last slide](#4). And of course, you could also clone this project and change
it, as you wish.

                           {{1}}
1. Load the macros via

   `import: https://raw.githubusercontent.com/liaTemplates/AlaSQL/master/README.md`

2. Copy the definitions into your Project

3. Clone this repository on GitHub


## `@AlaSQL.eval`

                         --{{0}}--
Add the macro `@AlaSQL.eval` to the end of every SQL code-block that you want to
make executable and editable in LiaScript. The given code gets evaluate by the
AlaSQL interpreter and the result is shown in a console below.


``` sql
CREATE TABLE test (language INT, hello STRING);

-- insert dummy values
INSERT INTO test VALUES (1,'Hello!');
INSERT INTO test VALUES (2,'Aloha!');
INSERT INTO test VALUES (3,'Bonjour!');

SELECT * FROM test WHERE language > 1;
```
@AlaSQL.eval


                         --{{1}}--
Since AlaSQL has its own "runtime-environment", you can use as many scripts and
only have to take care of the names.


                           {{1}}
``` sql
/*
select some stuff
*/
SELECT * FROM test WHERE language > 1;
```
@AlaSQL.eval


## `@AlaSQL.eval_with_csv`

                         --{{0}}--
Use `@AlaSQL.eval_with_csv` with two subsequent code-blocks, whereby the first
block is used to define a table and the second the CSV data with a header in the
first line. [PapaParse](https://www.papaparse.com) is applied for CSV-parsing,
see the next section for the implementation.

``` sql
CREATE TABLE one;

-- ? gets replaced by the values in data.csv
INSERT INTO one SELECT * from ?;
```
``` text -data.csv
Region,Country,Item Type,Sales Channel,Order Priority,Order Date,Order ID,Ship Date,Units Sold,Unit Price,Unit Cost,Total Revenue,Total Cost,Total Profit
Middle East and North Africa,Libya,Cosmetics,Offline,M,10/18/2014,686800706,10/31/2014,8446,437.20,263.33,3692591.20,2224085.18,1468506.02
North America,Canada,Vegetables,Online,M,11/7/2011,185941302,12/8/2011,3018,154.06,90.93,464953.08,274426.74,190526.34
Middle East and North Africa,Libya,Baby Food,Offline,C,10/31/2016,246222341,12/9/2016,1517,255.28,159.42,387259.76,241840.14,145419.62
Asia,Japan,Cereal,Offline,C,4/10/2010,161442649,5/12/2010,3322,205.70,117.11,683335.40,389039.42,294295.98
Sub-Saharan Africa,Chad,Fruits,Offline,H,8/16/2011,645713555,8/31/2011,9845,9.33,6.92,91853.85,68127.40,23726.45
Europe,Armenia,Cereal,Online,H,11/24/2014,683458888,12/28/2014,9528,205.70,117.11,1959909.60,1115824.08,844085.52
Sub-Saharan Africa,Eritrea,Cereal,Online,H,3/4/2015,679414975,4/17/2015,2844,205.70,117.11,585010.80,333060.84,251949.96
Europe,Montenegro,Clothes,Offline,M,5/17/2012,208630645,6/28/2012,7299,109.28,35.84,797634.72,261596.16,536038.56
Central America and the Caribbean,Jamaica,Vegetables,Online,H,1/29/2015,266467225,3/7/2015,2428,154.06,90.93,374057.68,220778.04,153279.64
Australia and Oceania,Fiji,Vegetables,Offline,H,12/24/2013,118598544,1/19/2014,4800,154.06,90.93,739488.00,436464.00,303024.00
Sub-Saharan Africa,Togo,Clothes,Online,M,12/29/2015,451010930,1/19/2016,3012,109.28,35.84,329151.36,107950.08,221201.28
Europe,Montenegro,Snacks,Offline,M,2/27/2010,220003211,3/18/2010,2694,152.58,97.44,411050.52,262503.36,148547.16
Europe,Greece,Household,Online,C,11/17/2016,702186715,12/22/2016,1508,668.27,502.54,1007751.16,757830.32,249920.84
Sub-Saharan Africa,Sudan,Cosmetics,Online,C,12/20/2015,544485270,1/5/2016,4146,437.20,263.33,1812631.20,1091766.18,720865.02
Asia,Maldives,Fruits,Offline,L,1/8/2011,714135205,2/6/2011,7332,9.33,6.92,68407.56,50737.44,17670.12
Europe,Montenegro,Clothes,Offline,H,6/28/2010,448685348,7/22/2010,4820,109.28,35.84,526729.60,172748.80,353980.80
Europe,Estonia,Office Supplies,Online,H,4/25/2016,405997025,5/12/2016,2397,651.21,524.96,1560950.37,1258329.12,302621.25
North America,Greenland,Beverages,Online,M,7/27/2012,414244067,8/7/2012,2880,47.45,31.79,136656.00,91555.20,45100.80
Sub-Saharan Africa,Cape Verde,Clothes,Online,C,9/8/2014,821912801,10/3/2014,1117,109.28,35.84,122065.76,40033.28,82032.48
Sub-Saharan Africa,Senegal,Household,Offline,L,8/27/2012,247802054,9/8/2012,8989,668.27,502.54,6007079.03,4517332.06,1489746.97
```
@AlaSQL.eval_with_csv


                         --{{1}}--
Afterwards you can again use `@AlaSQL.eval` to define code for analyzing the
newly integrated table.

                           {{1}}
``` sql
/*
select some stuff
*/
SELECT Region FROM one;
```
@AlaSQL.eval


                           {{1}}
``` sql
SELECT * FROM one Where Region == "North America";
```
@AlaSQL.eval


## Implementation

                         --{{0}}--
The code shows the implementation of the two AlaSQL macros. The scripts load the
JavaScript libraries for AlaSQL and PapaParse. PapaParse is used to parse a
given string of CSV.

``` js
script:  https://cdnjs.cloudflare.com/ajax/libs/alasql/0.4.11/alasql-worker.min.js
         https://cdnjs.cloudflare.com/ajax/libs/alasql/0.4.11/alasql.min.js

script:  https://cdnjs.cloudflare.com/ajax/libs/PapaParse/4.6.1/papaparse.min.js

@AlaSQL.eval
<script>
try {
  JSON.stringify(alasql(`@input`), null, 3);
} catch(e) {
  let error = new LiaError(e.message, 1);
  try {
    let log = e.message.match(/.*line (\d):.*\n.*\n.*\n(.*)/);
    error.add_detail(0, e.name+": "+log[2], "error", log[1] -1 , 0);
  } catch(e) {}
  throw error ;
}
</script>
@end

@AlaSQL.eval_with_csv
<script>
let data = Papa.parse(`@input(1)`, {header: true});

let error = "";
if(data.errors.length != 0) {
    error = JSON.stringify(data.errors, null, 3)+"\n";
}

try {
  error += JSON.stringify(alasql(`@input`, [data.data]), null, 3);
} catch(e) {
  let error = new LiaError(e.message, 1);
  try {
    let log = e.message.match(/.*line (\d):.*\n.*\n.*\n(.*)/);
    error.add_detail(0, e.name+": "+log[2], "error", log[1] -1 , 0);
  } catch(e) {}
  throw error ;
}
</script>
@end

```

                         --{{1}}--
If you want to minimize loading effort in your LiaScript project, you can also
copy this code and paste it into your main comment header, see the code in the
raw file of this document.

{{1}} https://raw.githubusercontent.com/liaTemplates/AlaSQL/master/README.md


## More Information

To find out more about AlaSQL, visit the project site: http://alasql.org

or the Wiki at: https://github.com/agershun/alasql/wiki
