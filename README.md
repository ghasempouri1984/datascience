# 

The goal of the project is to develop a software that enables one to process data stored in different formats and to upload them into two distinct databases
to query these databases simultaneously according to predefined operations. The software must be accompanied by a document (i.e. a Jupyter notebook) describing the data to process (their main characteristics and possible issues) and how the software has been organised (name of the files, where have been defined the various Python classes, etc.).

## Data

Exemplar data for testing the project have been made available. In particular:

* for creating the relational database, there are two files, a [CSV file](data/relational_publications.csv) containing data about publications and a [JSON file](data/relational_other_data.json) containing additional information including the authors of each publication, the identifiers of the venue of each publication, and the identifier and name of each publisher publishing the venues;



## UML 

https://miro.com/app/board/uXjVOlm6vgg=/?share_link_id=155215955139


### Class `RelationalProcessor`

#### Attributes
`dbPath`: the variable containing the path of the database, initially set as an empty string, that will be updated with the method `setDbPath`.

#### Methods
`getDbPath`: it returns the path of the database.

`setDbPath`: it enables to set a new path for the database to handle.

### Classes `RelationalDataProcessor`

#### Methods
`uploadData`: it enables to upload the collection of data specified in the input file path (either in CSV or JSON, according to the [formats specified above](#source-data)) into the database.

### Classes `RelationalQueryProcessor`

#### Methods
`getPublicationsPublishedInYear`: It returns a data frame with all the publications (i.e. the rows) that have been published in the input year (e.g. `2020`).

`getPublicationsByAuthorId`: It returns a data frame with all the publications (i.e. the rows) that have been authored by the person having the identifier specified as input (e.g. `"0000-0001-9857-1511"`).

`getPublicationInVenue`: It returns a data frame with all the publications (i.e. the rows) that have been included in the venue having the identifier specified as input (e.g. `"issn:0944-1344"`).

`getJournalArticlesInIssue`: It returns a data frame with all the journal articles (i.e. the rows) that have been included in the input issue (e.g. `"9"`) of the input volume (e.g. `"17"`) of the journal having the identifier specified as input (e.g. `"issn:2164-5515"`).


### Class `GenericQueryProcessor`

#### Attributes
`queryProcessor`: the variable containing the list of `QueryProcessor` objects to involve when one of the *get* methods below is executed. In practice, every time a *get* method is executed, the method will call the related method on all the `QueryProcessor` objects included in the variable `queryProcessor`, before combining the results and returning the requested object.

#### Methods
`cleanQueryProcessors`: It clean the list `queryProcessor` from all the `QueryProcessor` objects it includes.

`addQueryProcessor`: It append the input `QueryProcessor` object to the list `queryProcessor`.

`getPublicationsPublishedInYear`: It returns a list of `Publication` objects referring to all the publications that have been published in the input year (e.g. `2020`).

`getPublicationInVenue`: It returns a list of Publication objects referring to all the publications that have been included in the venue having the identifier specified as input (e.g. "issn:0944-1344").

`getPublicationsByAuthorId`: It returns a list of `Publication` objects referring to all the publications that have been authored by the person having the identifier specified as input (e.g. `"0000-0001-9857-1511"`).

`getJournalArticlesInIssue`: It returns a list of `JournalArticle` objects referring to all the journal articles that have been included in the input issue (e.g. `"9"`) of the input volume (e.g. `"17"`) of the journal having the identifier specified as input (e.g. `"issn:2164-5515"`).


## Uses of the classes
### i do not use in the right way... for example my code does not return list[Publicatoin] of class Publication objects...

```
# Supposing that all the classes developed for the project
# are contained in the file 'impl.py', then:

# 1) Importing all the classes for handling the relational database
from impl import RelationalDataProcessor, RelationalQueryProcessor

# 2) Importing the class for dealing with generic queries
from impl import GenericQueryProcessor

# Once all the classes are imported, first create the relational
# database using the related source data
rel_path = "relational.db"
rel_dp = RelationalDataProcessor()
rel_dp.setDbPath(rel_path)
rel_dp.uploadData("data/relational_publications.csv")
rel_dp.uploadData("data/relational_other_data.json")

# In the next passage, create the query processors for both
# the databases, using the related classes
rel_qp = RelationalQueryProcessor()
rel_qp.setDbPath(rel_path)

# Finally, create a generic query processor for asking
# about data
generic = GenericQueryProcessor()
generic.addQueryProcessor(rel_qp)

result_q1 = generic.getPublicationsPublishedInYear(2020)
result_q2 = generic.getPublicationsByAuthorId("0000-0001-9857-1511")
# etc...
```
