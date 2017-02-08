# Wrangler
[![Build Status](https://travis-ci.org/hydrator/wrangler-transform.svg?branch=develop)](https://travis-ci.org/hydrator/wrangler-transform) 
<a href="https://scan.coverity.com/projects/hydrator-wrangler-transform">
  <img alt="Coverity Scan Build Status"
       src="https://scan.coverity.com/projects/11434/badge.svg"/>
</a>
[![codecov](https://codecov.io/gh/hydrator/wrangler-transform/branch/develop/graph/badge.svg)](https://codecov.io/gh/hydrator/wrangler-transform)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

Collection of libraries, pipeline plugin and CDAP service for performing data cleansing, transformation and filtering using a set of instructions. Instructions to manipulate data are either generated using an interative visual tool or could be manually entered.

## Concepts

This implementation of wrangler defines the following concepts. Please familiarize yourself with these concepts. 

### Record

A Record is a collection of field names and field values. 

### Column

A Column is a data value of any supported java type, one for each Record.

### Directive

A Directive is a single data manipulation instruction specified to either transform, filter or pivot a single record into zero or more records. A directive can produce one or more Steps to be executed by the Pipeline. 

### Step

A Step is a implementation of a data transformation function operating on a Record or set of records. A step can generate zero or more Records from the application of a function. 

### Pipeline

A Pipeline is a collection of Steps to be applied on a Record. Record(s) outputed from each Step is passed to the next Step in the pipeline. 

## Notations

### Directives

A directive is represented as simple text in the format as specified below
```
  <command> <argument-1> <argument-2> ... <argument-n>
```

### Record

A record in this documentation will be representation as json object with object key representing the column names and the value representing the plain representation of the the data without any mention of types. 

E.g.
```
{
  "id" : 1,
  "fname" : "root",
  "lname" : "joltie",
  "address" : {
    "housenumber" : "678",
    "street" : "Mars Street",
    "city" : "Marcity",
    "state" : "Maregon",
    "country" : "Mari"
  }
  "gender", "M"
}
```

## Available Directives

Following are different directives currently available.

* [Parsers]()
  * [CSV Parser](docs/directives/csv-parser.md)
  * [Json Parser](docs/directives/parse-as-json.md)
  * [Json Path](docs/directives/json-path.md)
  * [XML Parser](docs/directives/parse-as-xml.md)
  * [XML Path Parser](docs/directives/xml-path.md)
  * [Fixed Length Parser](docs/directives/fixed-length-parser.md)
  * [HTTPD and NGNIX Log Parser](docs/directives/parse-as-log.md)
  * [Date Parser](docs/directives/parse-as-date.md)
  * [HL7 Parser](docs/directives/parse-as-hl7.md)
* [Output Formatters]()
  * [JSON Map Formatter](docs/directives/write-to-json-map.md)
* [Text Transformations](docs/directives/text-transformation.md)
  * [Change Text case](docs/directives/change-case.md)
  * [Index Split](docs/directives/index-split.md)
  * [Split by Seperator](docs/directives/split-by-seperator.md)
  * [Sed](docs/directives/sed.md)
  * [Cut](docs/directives/cut.md)
  * [Expressions](docs/directives/expression.md)
  * [URL Encode](docs/directives/url-encode.md)
  * [URL Decode](docs/directives/url-decode.md)
  * [Split Email Address](docs/directives/split-email.md)
  * [Fuzzy String Match - Distance](docs/directives/text-distance.md)
  * [Fuzzy String Match - Metric](docs/directives/text-metric.md)
* [Quantization](docs/directives/quantize.md)
* [Unique ID]()
  * [UUID Generation](docs/directives/generate-uuid.md)
* [Date Transformations](docs/directives/date-time.md)
  * [Format Date](docs/directives/format-date.md)
  * [Format Unix Timestamp](docs/directives/format-timestamp.md)
* [Static Catalog Lookup]()
  * [ICD-9 Code](docs/directives/lookup.md)
  * [ICD-10 Code - 2016, 2017](docs/directives/lookup.md)
* [Hashing & Masking](docs/directives/masking.md)
  * [Substitution Masking](docs/directives/mask-substitution.md)
  * [Number Masking](docs/directives/mask-number.md)
  * [Message Digest or Hash](docs/directives/hash.md)
* [Row Operations]()
  * [Flatten](docs/directives/flatten.md)
  * [Split To Rows](docs/directives/split-to-rows.md)
  * [Filter Rows On](docs/directives/filter-rows-on.md)
  * [Filter Row using Regex](docs/directives/filter-row-if-matched.md) (_Deprecated_)
  * [Filter Row on Condition](docs/directives/filter-row-if-true.md) (_Deprecated_)
* [Column Operations]()
  * [Drop Column](docs/directives/drop.md)
  * [Rename Column](docs/directives/rename.md)
  * [Copy Column](docs/directives/copy.md)
  * [Merge Columns](docs/directives/merge.md)
  * [Keep Columns](docs/directives/keep.md)
  * [Swap Column](docs/directives/swap.md)
  * [Split To Columns](docs/directives/split-to-columns.md)
  * [Fill Null or Empty Columns](docs/directives/fill-null-or-empty.md)  
  
## Performance

Initial performance tests shows that with medium set of directives for transforming data, wrangler is able to process at ~ 60K records/second. The rates below are specified as '**records/second**'. More details and test results will be available [here](docs/performance.md)

| Directive Complexity | Column Count| Records | Size | Mean Rate | 1 Minute Rate | 5 Minute Rate | 15 Minute Rate |
|----------------------|-------------|---------|------|-----------|---------------|---------------|----------------|
| Medium | 18 | 13,499,973 | 4,499,534,313 | 64,998.50 | 64,921.29 | 46,866.70 | 36,149.86 | 
| Medium | 18 | 80,999,838 | 26,997,205,878 | 62,465.93 | 62,706.39 | 60,755.41 | 56,673.32 | 

## Wrangler Service

Wrangler is integrated as a CDAP Service to support REST based interactive ways to wrangle data. The main objective of having this service is to make it easy for interactively generating directives required for parsing data. This service does not support full scale big data processing.

The base endpoint is defined below :

```
  http://<hostname>:11015/v3/namespaces/<namespace>/apps/wrangler/services/service/methods
```

Following are different services provided:

* [Administration and Management](docs/service/admin.md)
* [Directive Execution](docs/service/execution.md)
* [Column Type Detection and Statistics](docs/service/statistics.md)
* [Column Name Validation](docs/service/validation.md)

## Build new directives

Directives are executed as a step, so it's a simple three step process to actually implement the Step and
provide the specification for directive.

### Step 1/3
In order to add a new step, implement the interface 'Step'.
```
/**
 * A interface defining the wrangle step in the wrangling pipeline.
 */
public interface Step {
  /**
   * Executes a wrangle step on single {@link Row} and return an array of wrangled {@link Row}.
   *
   * @param records List of input {@link Record} to be wrangled by this step.
   * @return Wrangled list of {@link Record}.
   * @throws StepException In case of any issue this exception is thrown.
   */
  List<Record> execute(List<Record> records) throws StepException;
}
```

### Step 2/3
Add comprehensive test case for testing the directive that has been added. 

### Step 3/3

Modify the specification to parse the directive specification and create the implementation of
Step you have created above.

## Build
To build your plugins:

    mvn clean package -DskipTests

The build will create a .jar and .json file under the ``target`` directory.
These files can be used to deploy your plugins.

## UI Integration

The Cask Hydrator UI displays each plugin property as a simple textbox. To customize how the plugin properties
are displayed in the UI, you can place a configuration file in the ``widgets`` directory.
The file must be named following a convention of ``[plugin-name]-[plugin-type].json``.

See [Plugin Widget Configuration](http://docs.cdap.io/cdap/current/en/hydrator-manual/developing-plugins/packaging-plugins.html#plugin-widget-json)
for details on the configuration file.

The UI will also display a reference doc for your plugin if you place a file in the ``docs`` directory
that follows the convention of ``[plugin-name]-[plugin-type].md``.

When the build runs, it will scan the ``widgets`` and ``docs`` directories in order to build an appropriately
formatted .json file under the ``target`` directory. This file is deployed along with your .jar file to add your
plugins to CDAP.

## Deployment
You can deploy your plugins using the CDAP CLI:

    > load artifact <target/plugin.jar> config-file <target/plugin.json>

For example, if your artifact is named 'my-plugins-1.0.0':

    > load artifact target/my-plugins-1.0.0.jar config-file target/my-plugins-1.0.0.json

## Mailing Lists

CDAP User Group and Development Discussions:

- `cdap-user@googlegroups.com <https://groups.google.com/d/forum/cdap-user>`__

The *cdap-user* mailing list is primarily for users using the product to develop
applications or building plugins for appplications. You can expect questions from 
users, release announcements, and any other discussions that we think will be helpful 
to the users.

## IRC Channel

CDAP IRC Channel: #cdap on irc.freenode.net


## License and Trademarks

Copyright © 2016-2017 Cask Data, Inc.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except
in compliance with the License. You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the 
License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, 
either express or implied. See the License for the specific language governing permissions 
and limitations under the License.

Cask is a trademark of Cask Data, Inc. All rights reserved.

Apache, Apache HBase, and HBase are trademarks of The Apache Software Foundation. Used with
permission. No endorsement by The Apache Software Foundation is implied by the use of these marks.
