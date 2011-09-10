# Solr with Lithuanian language support

[Solr](http://lucene.apache.org/solr/) is the popular, blazing fast open source enterprise search platform from the Apache [Lucene](http://lucene.apache.org/) project.

This is a pre-packaged Solr version with Lithuanian language support from [zmedelis/ltlangpack](https://github.com/zmedelis/ltlangpack).

## Usage

- Download.
- Edit `solr/conf/schema.xml` (see "Configuration" below).
- Run: `java -jar start.jar`
- Open <http://localhost:8983/solr/admin> in your browser

### Requirements

- Java

### Configuration

#### Fields, types, schema.xml

You should first of all configure the *fields* for the documents in the database. Fields are defined in file `solr/conf/schema.xml`. Look for `<field .../>` tags and change them according to your needs.

You will probably have to remove a lot of fields from the default `schema.xml` included with Solr. To ease this process I have added a `schema-minimal.xml` file which holds just a few field definitions useful for indexing web pages. To use it backup `schema.xml` and copy `schema-minimal.xml` on top of it.

The default field type for indexing Lithuanian text is `text_lt`. You can also define your own field types in the same `schema.xml` file.

#### Stopwords, synonyms, protected words

After playing with the server for some time you will notice that sometimes searches return too many results with words that are too dissimilar to words in your query. This happens because the Lithuanian stemmer cuts off too many characters from the end of the word. To help cope with this you can use `solr/conf/protwords_lt.txt` and `solr/conf/synonyms_lt.txt` to write the exact cases for problematic words. See the examples in these files for more info.

If you add your own words to these files, please send a patch, so that we all can benefit from this.

`solr/conf/stopwords_lt.txt` contains a good list of short words (*like "ir","šis","tačiau"*) that are useless when searching. They are ommitted from index and ignored in query text.

#### Default port

The default port for Solr server is 8983 to change it edit this line in `etc/jetty.xml` (check for duplicates):

```xml
<Set name="port"><SystemProperty name="jetty.port" default="8983"/></Set>
```

### Using Solr server from your applications

*Note: these exapmles use JSON format for data. You can also use XML and other formats that Solr supports.*

Posting data to server (assuming fields *id,url,title,text* are defined in schema.xml):

```sh
curl 'http://localhost:8983/solr/update/json?commit=true' \
    -H 'Content-type:application/json' \
    -d '{"id":1, "url":"http://example.org/", "title":"Pavyzdys", "text":"Pavyzdinis dokumentas."}'
```

Getting search results:

```sh
curl 'http://localhost:8983/solr/search' \
    -d wt=json \
    -d hl=true -d hl.fl=text \
    -d q=pavyzdys
```

You can use your browser to see what results you will get using various HTTP parameters. The query above can be used like this:

<http://localhost:8983/solr/search?wt=json&hl=true&hl.fl=text&q=pavyzdys>
or to get XML:
<http://localhost:8983/solr/search?hl=true&hl.fl=text&q=pavyzdys>

#### Tip

From my experience it is easier to strip HTML tags and send plain text to Solr for indexing than try to convert HTML to an XML that Solr would accept.

### Tuning

You can limit the amount of memory the server uses by passing java executable some parameters, e.g.:

```sh
java -Xmx256m -jar start.jar
```

this should keep the server from using more than 300 MB of memory.

## Read more

- <http://lucene.apache.org/solr/tutorial.html>
- <http://khaidoan.wikidot.com/solr>

## About

### License

Apache Solr is licensed under [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0).

### Credits

- Apache Foundation and its contributors.
- Žygimantas Medelis, M. Petkevičius, Tomas Krilavičius for Snowball version of Porter stemmer for Lithuanian language.

### Packager contact

Emilis Dambauskas <emilis.d@gmail.com>, <http://emilis.github.com>.

