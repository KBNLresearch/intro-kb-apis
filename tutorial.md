# Introduction to the KB APIs

The KB offers access to a large amount of digital content: from books to parliamentary papers and from historical newspapers to medieval illuminated manuscripts. Often users will first encounter these collections through one of several KB websites, such as [Delpher](http://www.delpher.nl/), [Memory of the Netherlands](http://www.geheugenvannederland.nl/) or the [Dutch Digital Parliamentary Papers](http://www.statengeneraaldigitaal.nl/). These website provide a graphical user interface (GUI) with functionality to browse, search, view and download digital objects and their metadata.

Others may want to (re)use some of this content: heritage institutions or commercial parties to develop an alternative user interface that offers new functionality or combines data from different sources (e.g. [Europeana](http://www.europeana.eu/)); Digital Humanities scholars in order to explore their research questions, either by using existing tools or developing their own software. To enable such user groups to programmatically retrieve data in bulk the KB has created a number of application programming interfaces or APIs.

### What data is available?

For each digital object in the KB collections, the following metadata and (sub)objects will typically be available:

- Structural metadata in [MPEG21 DIDL](http://mpeg.chiariglione.org/standards/mpeg-21) (Digital Item Declaration Language) format, describing the way in which a composite digital object is built up. This metadata contains information about all associated files, such as descriptive metadata, images and full-text objects.

- Descriptive metadata in the [Dublin Core](http://dublincore.org/documents/dces/) (DC) vocabulary, with some KB-specific extensions (DCX). Here you will find information about title, author, date, language etc. of an item.

- One or more [ALTO](https://www.loc.gov/standards/alto/) files with content and layout information. This is most often used for applications wanting to highlight specific elements on a page.

- One or more OCR (Optical Character Recognition) files containing the textual content of an object and its basic structure, such as what the title is and where paragraphs begin and end.

- One or more images of the object, usually in [JPEG](https://jpeg.org/jpeg/) format.

Apart form the images, all data is provided in [XML](https://www.w3.org/TR/xml/) (Extensible Markup Language) format, a simple markup language that adds machine-readable structure by enclosing distinct bits of information between tags. One of the benefits of this data format is that it can be easily read by humans as well.

### And which APIs?

The KB infrastructure to handle access to these different types of data consists of three main components:

- The digital objects from the various collections (images, ALTO, OCR) are gathered in a central web storage (KB-WO).

- The metadata associated with these objects (descriptive, structural) is stored seperately in central metadata storage (KB-MDO).

- In order to be able to efficiently search these large data sources the KB builds up a search index from metadata and full-text objects.

Each of these three components can be accessed over the web through its own API: a harvest API based on [OAI-PMH](http://www.openarchives.org/pmh/) to request metadata from the central metadata storage, a search API based on [SRU](http://www.loc.gov/standards/sru/) to query the index, and a resolver to retrieve files from the web storage.

## The resolver

In this tutorial we will explore the relatively simple data set of [Radio bulletins](https://www.kb.nl/en/resources-research-guides/data-services-apis/open-data-set-radio-bulletins-anp-press-agency) from the ANP press agency, consisting of 1.5 million digitized typoscripts from radio news broadcast between 1937 and 1984. It is available through the Delpher website and the KB offers the data under (semi) open licenses: for the metadata a [CC0-license](https://creativecommons.org/publicdomain/zero/1.0/) applies, whereas images and full-text objects carry [CC-BY-NC-ND-licenses](https://creativecommons.org/licenses/by-nc-nd/3.0/deed.en).

Like all objects from the KB collections each typoscript is issued with a persistent identifier, e.g. `http://resolver.kb.nl/resolve?urn=anp:1973:10:18:44:mpeg21`. This identifier takes the form of a resolver link and can be constructed from the persisent link of the typoscript that is shown on its Delpher page by adding the `:mpeg21` suffix. Adding additional suffixes will retrieve the various files associated with the typoscript from the web storage:

- An image can be retrieved by adding the `:image` suffix: <br><http://resolver.kb.nl/resolve?urn=anp:1973:10:18:44:mpeg21:image>

- The OCR of the typoscript can be retrieved by adding the `:ocr` suffix: <br><http://resolver.kb.nl/resolve?urn=anp:1973:10:18:44:mpeg21:ocr>

- And the ALTO file for the typoscript can be retrieved using the `:alto` suffix: <br><http://resolver.kb.nl/resolve?urn=anp:1973:10:18:44:mpeg21:alto>

### Exercise 1: Getting files through the resolver

- Visit the [Delpher](http://www.delpher.nl/) website and search the collection of radio bulletins. Find the persistent link for a typoscript of your choice and construct its unique identifier by adding the `:mpeg21` suffix.

-  Open a new tab or window in your browser and retrieve the image, OCR and ALTO files associated with this typoscript using suffixes.

## The OAI-PMH harvest API

The metadata harvest API is aimed at retrieving a copy of a data set as a whole, as well as the administrative information needed to keep that copy up to date with the original set, in case of any changes occurring to the original after the initial harvest. It is based on the [OAI-PMH](http://www.openarchives.org/pmh/) (Open Archives Initiative Protocol for Metadata Harvesting) standard protocol created by the Open Archives Initiative. 

A simple request to the harvest API to retrieve the DIDL structural metadata record for a typoscript from the central metadata storage is:

<http://services.kb.nl/mdo/oai?verb=GetRecord&identifier=anp:anp:1981:10:14:20:mpeg21&metadataPrefix=didl>

All requests start with `http://services.kb.nl/mdo/oai`, the base URL of the harvest API, followed by a question mark. After that a so-called query string appears, containing a number of URL parameters with the details of the request. The parameters take the form of field-value pairs, separated by ampersand signs. 

First, the operation that is to be performed, called `verb` according to the OAI protocol, needs to be specified, which in this case is `GetRecord` to retrieve a
single metadata record. Next, the unique identifier `anp:anp:1981:10:14:20:mpeg21` of the requested object needs to be supplied, which is inserted with the `identifier` parameter. The identifier used here consists of the final part of the persistent resolver identifier (after `urn=`) for the typoscript, to which the prefix `anp:` is then added to indicate the specific metadata set we are harvesting from. Finally, the `didl` metadata format we would like to retrieve is requested with the parameter `metadataPrefix`.

The [response](responses/oai_get_record.xml) to this request will be an XML file starting with some general information about the request, followed by the actual DIDL metadata record. Note that the descriptive metadata for the typoscript is embedded in a block of the structural metadata, whereas other data types can be retrieved using resolver links.

### Exercise 2: Getting started with OAI-PMH

- Open a new window or tab in your browser and navigate to the harvest API base URL. Construct the parameters for retrieving the DIDL structural metadata record for the typoscript of your choice.

- Inspect the data returned. In particular, identify the different blocks or components that are included in the response and the way(s) in which to obtain the data they describe.

### OAI-PMH verbs

The OAI-PMH protocal includes five verbs in addition to the `GetRecord` verb that can be used to perform other operations:

- `Identify` will retrieve general information about a OAI repository, such as its name and the version of the OAI-PMH protocol supported: <br/><http://services.kb.nl/mdo/oai?verb=Identify>

- `ListSets` will retrieve a list of data sets present in the repository: <br><http://services.kb.nl/mdo/oai?verb=ListSets>

- `ListMetadataFormats` will retrieve the metadata formats available from a repository. An optional identifier argument restricts the request to the formats available for a specific object: <br><http://services.kb.nl/mdo/oai?verb=ListMetadataFormats&identifier=anp:anp:1981:10:14:20:mpeg21>

- `ListRecords` will retrieve all metadata records from the specified set in the specified format. The optional `from` parameter can be used to selectively harvest records that were added to the set after a particular date: <br><http://services.kb.nl/mdo/oai?verb=ListRecords&set=anp&metadataPrefix=didl&from=2005-01-01>

- `ListIdentifiers` will retrieve only the identifiers of all items available from the specified set in the specified format. The optional `from` parameter can be used here as well: <br><http://services.kb.nl/mdo/oai?verb=ListIdentifiers&set=anp&metadataPrefix=didl>

Note that the OAI-PMH syntax is case sensitive. The order in which the parameters are inserted, however, is not important: different orderings will yield the same results.

### Harvesting an entire set

Since sets in an OAI-PMH repository can be quite large, harvesting an entire set should be performed in a number of steps. First, all identifiers for the set are harvested. By default, only the first 400 identifiers are returned in the initial response of a `ListIdentifiers` request, such as:

<http://services.kb.nl/mdo/oai?verb=ListIdentifiers&set=anp&metadataPrefix=didl>

In the [response](responses/oai_list_identifiers.xml) a so-called resumption token is included near the end, e.g. `anp!2008-09-24T09:09:16.332Z!!didl!2317275`. This token can be added to a subsequent request with the `resumptionToken` parameter, without the need to specify the set name and metadata format again:

<http://services.kb.nl/mdo/oai?verb=ListIdentifiers&resumptionToken=anp!2008-09-24T09:09:16.332Z!!didl!2317275>

This will retrieve the next 400 identifiers, along with a new resumption token. This step can be repeated until the identifiers for the entire set have been harvested, in which case no new resumption token will be present in the response.

The resulting list of identifiers can now be used to retrieve the full metadata records by issuing `GetRecord` requests for each identifier. Finally, any files associated with the objects can be downloaded using the resolver links from the DIDL record.

### Exercise 3: Harvesting a set with OAI-PMH

- Go back to the browser tab or window containing the `GetRecord` request from the previous exercise. Adjust the query string to get some general information about the KB-MDO repository, using verbs such as `Identify`, `ListSets` and `ListMetadataFormats`.

- Enter the URL to harvest the first 400 identifiers from the ANP set. Find the resumption token and use it to request the next 400 identifiers and repeat this step a few times.

## The SRU search API

If you are interested in getting the results for a particular search query, rather than dowloading an entire set, you can use the search API based on [SRU](http://www.loc.gov/standards/sru/) (Search and Retrieval via URL), a standard protocol for performing search queries that is currently maintained by the Library of Congress. The base URL for the search API is `http://jsru.kb.nl/sru/sru` and a simple request to query the ANP collection with a single keyword would be:

<http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=nobelprijs>

As a first parameter the `operation` that is to be performed is specified, which in this case is `searchRetrieve` for a regular search query. The keyword we are interested in is `nobelprijs`, which is thus entered with the `query` field. Finally, the collection to be searched is indicated with the `x-collection` parameter and the collection name `ANP` (with the SRU syntax, like OAI-PMH, being case sensitive). Note that the name of collections in the index may differ from the name of the corresponding set in the metadata repository.

The [response](responses/sru_search_retrieve.xml) to this request will contain the search results in XML format, along with some general information about the request, such as the version of the SRU protocol being used, the time it took to process the request, and the total number of search results.

### SRU parameters

The SRU protocol includes a number of parameters that can be used to further tune the response received and the KB has implemented some extensions to the protocol to enable additional functionality. Parameters that are part of this KB extension can be recognized by the `x-` prefix.

- With the `operation` parameter set to `explain`, some general information about the service can be obtained:<br><http://jsru.kb.nl/sru/sru?operation=explain>

- By default, the first 20 search results are returned. This number can be adjusted with the `maximumRecords` parameter:<br><http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=nobelprijs&maximumRecords=100>

- If you want to start viewing the result set from a particular record onwards, you can use the `startRecord` parameter:<br><http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=nobelprijs&startRecord=100>

- If you want to add particular bits of information to the display, you can use the `x-fields` parameter. The OCR, for example, can be added by entering the value `content` in this field. Multiple values an be entered as well, separated by commas. To view all available information simply enter `*` here:<br>
<http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=nobelprijs&x-fields=content,volgnummer>

- By default, the Dublin Core descriptive metadata is displayed, but with the `recordSchema` parameter other types of metadata, such as the DIDL, may be retrieved, if available:<br>
<http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=nobelprijs&recordSchema=didl>

### Exercise 4: Querying the index with SRU

- Open a new window or tab in your browser and navigate to the search API base URL. Construct the parameters for querying the ANP collection with a single keyword of your choice. Find the total number of results for your query.

- Use the `maximumRecords`, `startRecord` and `x-fields` parameters to expand and navigate through the results.

### CQL query syntax
 
The SRU protocol uses [CQL](https://www.loc.gov/standards/sru/cql/) (Contextual Query Language), another standard, as its query language. With the CQL query syntax more elaborate search queries can be formed:

- To search a string consisting of multiple words, enter the string between double quotes as the query parameter value:<br>
[http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query="nobelprijs literatuur"] (http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=%22nobelprijs+literatuur%22)

- For an OR- or AND-query consisting of two terms, simply use the words `OR` or `AND` between the search terms, surrounded by spaces:<br>
[http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=nobelprijs AND literatuur] (http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=nobelprijs+AND+literatuur)

- Wildcards in the form of an asterisk `*` are also supported when appearing in the middle or at the end of a keyword:<br><http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=nobelpr*>

- To restrict the search to a particular field, add the field name before the search term. This option is only available for specific fields for the ANP collection, such as `date` and `volgnummer`:<br>
<http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=date=01-01-1960>

- To restrict the search to a specific period in time, use the `within` syntax in combination with the `date` field:<br>
[http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=date within "01-01-1960 01-01-1961"] (http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=date+within+%2201-01-1960+01-01-1961%22)

When constructing these queries you have to be careful to properly encode certain characters, such as spaces, that may not appear as part of an URL. A space should be replaced by `%20` or `+`, for example, and a double quotation mark with `%22`. Many browsers will automatically take care of this encoding for you, but if you run into problems you can get your query encoded at the [URL Encoding Reference](http://www.w3schools.com/tags/ref_urlencode.asp).

### Faceting with SRU

The search API has the option of faceting the search results by period. A query for faceting the search results by year, for example, is:

<http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=nobelprijs&maximumRecords=0&x-facetprefix=1&x-facetname=periode&x-facets=indexes:ANPfacets:periode>

The `maximumRecords` parameter has been set to 0 here, so that only the facetted results are present in the [response] (responses/sru_search_facets.xml). The `x-facetprefix` parameter can take values from 0 to 3, resulting in different temporal resolutions of the facet (where 0=decade, 1=year, 2=month, and 3=day). The `x-facetname` and `x-facets` parameters are needed to indicate the particular facet requested.

The period facet can be filtered as well. If you want facets with a resolution of a month, but are only interested in the decade 1950-1959, the query would be:

[http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=nobelprijs&maximumRecords=0&x-facetprefix=2&x-facetname=periode&x-facets=indexes:ANPfacets:periode&x-filter=periode exact "0/1950-1959/"] (http://jsru.kb.nl/sru/sru?operation=searchRetrieve&x-collection=ANP&query=nobelprijs&maximumRecords=0&x-facetprefix=2&x-facetname=periode&x-facets=indexes:ANPfacets:periode&x-filter=periode+exact+%220%2F1950-1959%2F%22)

### Exercise 5: Advanced SRU options

- Experiment with the CQL query language to form more elaborate queries. Search for a string consisting of multiple keywords, for example, and filter the results by selecting a particular date range.

- Note the URL encoding that your browser applies to the query (or, if your browser does not offer this functionality, the URL encoding that is returned by the [URL Encoding Reference](http://www.w3schools.com/tags/ref_urlencode.asp)).

- Create a faceted view of a result set of your choice by using the `x-facets`, `x-facetname` and `x-facetprefix` parameters.

## Other KB data sets

The KB offers access to many other collections besides the ANP radio bulletins through its APIs. Other collections may be somewhat more complex to work with if objects with a hierarchical structure, such as a newspaper issue comprising multiple pages and articles, are involved. However, for several such collections detailed technical instructions are available from the [Data Services](<https://www.kb.nl/en/resources-research-guides/data-services-apis>) page of the KB website to help you get started.

Note that not all collections are available as open data sets, so if you want to use a copyright-protected collection, please contact us first to discuss the possibilities. It may be necessary to draw up a contract stating the allowed use of the dataset, or to obtain a so-called API key that can be used to gain access to restricted materials.

### Exercise 6: Moving on to other sets

-   Visit the [Data Services](<https://www.kb.nl/en/resources-research-guides/data-services-apis>) page of the KB website and take a look at the open datasets that are available.
-   Open the technical instructions for a dataset of your choice and try it out with the search and harvest APIs.

## Questions?

If you have any questions about a particular data set or run into problems while using the APIs, you can always contact us using the KB Digital Humanities mailbox at <dh@kb.nl>.
