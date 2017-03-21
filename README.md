# Introduction to the KB APIs

This [tutorial](tutorial.md) provides an easy introduction to working with the KB search and harvest APIs, aimed primarily at users with no programming experience. It covers everything you need to know to get started using the APIs, including:

- The main components of the KB data access infrastructure 
- The various types of files and metadata that are typically associated with digital objects from the KB collections, such as images and full-text objects
- The most important metadata standards used, such as [Dublin Core](http://dublincore.org/documents/dces/) for descriptive metadata and [MPEG21-DIDL](http://mpeg.chiariglione.org/standards/mpeg-21) for structural metadata
- Querying a collection with the search API at [http://jsru.kb.nl/sru/sru](http://jsru.kb.nl/sru/sru?operation=explain), based on the [SRU](http://www.loc.gov/standards/sru/) standard protocol
- Harvesting metadata with the harvest API at [http://services.kb.nl/mdo/oai](http://services.kb.nl/mdo/oai?verb=Identify), based on the [OAI-PMH](http://www.openarchives.org/pmh/) standard protocol
- Getting files through the resolver using persistent identifiers

In addition to the [tutorial](tutorial.md) itself, this repository contains some [example API responses](responses), a [glossary](glossary.pdf) of frequently used terms and the [slides](slides.pdf) used for presenting the tutorial. 
