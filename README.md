# solrsan

http://github.com/tc/solrsan

This gem is a lightweight wrapper for the Apache Solr API.

Before you start, read the documentation for solr at http://wiki.apache.org/solr

It'll be invaluable for knowing parameters and error messages. 
I made a few test cases for further examples at http://github.com/tc/solrsan/tree/master/test/unit

## HOWTO
Install Jetty/solr

```
rake solr:setup
```
See lib/tasks/solr.rake

Add solrsan to your Ruby application's Gemfile:

```
  gem "solrsan"
```

Create solr configuration files using:

```
  rails generate solrsan:config
```

The generator will copy the following files into your application.

```
  config/solr.yml
  config/solr
  config/initializers/solrsan.rb
  lib/tasks/solr.rake
```

Edit the config/solr.yml for your directory paths.

The rake file will add these rake tasks:

```
  rake solr:start
  rake solr:stop
  rake solr:clear_index
  rake solr:index
```

you will need to alter clear_index/index to match your models

Deploy tasks via capistrano:
add to your deploy.rb

```
  require 'solrsan/capistrano'
```

This will add the following methods which will just call the
corresponding rake tasks:

```
  cap solr:start
  cap solr:stop
  cap solr:reindex
```

## Indexing documents:
Edit config/solr/conf/schema.xml to state the types of fields you want
to index. You can use dynamic fields as well.

These fields are required for each solr document and are automatically
generated:

```
  id, db_id, type
```

In your model, define as_solr_document and return a hash with specific fields.

```
  class Document < ActiveRecord::Base
    include Solrsan::Search
    after_save :solr_index
    before_destroy :destroy_index_document

    def as_solr_document
      {:content => "hi"}
    end
  end
```

In each model, you can include a Solrsan::Search module which will include a few interface helper methods:

```
  solr_index
  destroy_index_document
  search(params)
```

## Search:
A simple search query:

```
  Document.search(:q => "hello world")
```

More searching examples can be seen in test/unit/search_test.rb

## Changelog
0.6.0
Corrected gemspec format. Thanks dinshaw.

0.5.9
Reverted ability to custom modify solr 'type' attribute field.

0.5.8
Allow any version of activesupport > 3.1 for gem dependency.

0.5.7
Allow solr attribute field 'type' to be overridden.

0.5.6
Tests use sleep since test index are using commitWithin. Fixed rsolr
reference.

0.5.5
Fixed usage of commitWithin.

0.5.4
Added a opts param with a default commitWithin of 10ms. Upgraded jetty
version.

0.5.2
Renamed index to solr_index to avoid naming conflict with mongoid.

0.5.1
Added solr:setup rake/cap task

0.5.0
Updated gems to rsolr 1.0.3/rails 3.1.1

0.0.33
Reverted Hashwithindifferentaccess for request parsing, buggy with will_paginate.

0.0.32
Using Hashwithindifferentaccess for request parsing.

0.0.31
Usable version!

0.0.1
First release.

## Copyright

Copyright (c) 2011 Tommy Chheng. See LICENSE for details.

