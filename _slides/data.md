---
---

## Storing the Data History

Any text document (scripts, notes, reports) will be handled very well by a version control system like `git`. Can we keep information about data itself in the same project.

Option 1
: Small-ish size data can be stored directly in the repository as text. Examples include tabular data in CSV format or spatial data in GeoJSON format.

Option 2
: The data itself resides outside the project, but data management is done from scripts that are within the project repository. Management can include cleaning, importing and updating data or the design of tables.
{:.fragment}

<!--split-->

~~~
## Setup

library("RPostgreSQL")
setwd("%sandbox%")

## Table Creation

drv <- dbDriver("PostgreSQL")
con <- dbConnect(drv,
                 dbname="portal",
                 host="pgstudio.research.sesync.org",
                 user="student",
                 password="%password%")

dbGetQuery(con, "BEGIN TRANSACTION")

dbGetQuery(con, "
DROP TABLE IF EXISTS plots CASCADE;
")

dbGetQuery(con, "
CREATE TABLE plots(
  plot_id INTEGER PRIMARY KEY,
  plot_type text);
")

raw_data <- read.csv("data/plots.csv")
dbWriteTable(con, "plots", raw_data, row.names=FALSE, append=TRUE)

dbCommit(con)
dbDisconnect(con)
~~~
{:.text-document title="data-management.R"}

<!--split-->

Exercise
: Recall the notice that came up when the plots table was dropped. It said: `NOTICE:  drop cascades to constraint surveys_plot_id_fkey on table surveys`. We need to add the foreign key back after our data is ingested. Modify the script to make that happen. When testing it, source the whole script rather than running individual lines.

<!--split-->

Maintaining your chain of commits to a database is really clean when you use "Transactions".

~~~
dbGetQuery(con, "BEGIN TRANSACTION")

... do something ...

dbCommit(con)
~~~
{:.input}

<!--split-->

A transaction is a block of changes that don't really take effect until being commited. If you get an error during a transaction, safely back out of the changes with `dbRollback`.

~~~ 
Warning message:
In postgresqlQuickSQL(conn, statement, ...) : Could not create execute
...
> dbRollback(con)
~~~
{:.input}

By the way, this is a great example of using the editor for "clean" scripts, but being ready to use the interactive console for one-off commands.

<!--split-->

In the `git` repository, we saw that the history of a project -- how it got where it is -- is a chain of commits. Each commit applies changes to the project in the state resulting from the previous commit.

The best practice is to let your dataset can evolve in the same way.

~~~
dbGetQuery(con, "BEGIN TRANSACTION")

... make 263rd change to the data ...

dbCommit(con)

dbGetQuery(con, "BEGIN TRANSACTION")

... make 264th change to the data ...

dbCommit(con)

~~~
{:.input}

<!--split-->

## Add commits: don't re-write history!

You do not want to change an existing transaction, unless you have time to ensure that all subsequent transactions will do what they were originally intended to do.

You do not have to run the whole script if you introduce a new transaction, although in theory you *should be able to.* Instead, add the new transaction after you have made it work.

<!--split-->

An example of adding the record of a new commit into `data-management.R`

~~~
dbGetQuery(con, "BEGIN TRANSACTION")

surveyors <- dbReadTables("surveyors")
dbGetQuery(con, "
UPDATE surveyor
SET (first_name, last_name) = ('Joe', 'Maher')
WHERE person_id = 3;
")

dbRollback(con)
~~~

<!--split-->

Exercise
: Let's clean up the surveyor table! Take responsibility for your own row(s) or insert a new row. In addition to the 'UPDATE ... SET' phrase, here are two other helpful SQL snippets.

~~~
DELETE FROM surveyor WHERE person_id = %an id%
~~~

~~~
INSERT INTO surveyor (a, b, c, ...) VALUES (x, y, z ...)
~~~
