# ETL

## [DBT](https://medium.com/the-telegraph-engineering/dbt-a-new-way-to-handle-data-transformation-at-the-telegraph-868ce3964eb4#:~:text=DBT%20(Data%20Building%20Tool)%20is,for%20Extraction%20and%20Load%20operations.)

[**DBT**](https://www.getdbt.com/) (**Data Building Tool**) is a command-line tool that enables data analysts and engineers to **transform data in their warehouses simply by writing select statements**.

**DBT performs the T (Transform) of ETL** but it doesn’t offer support for Extraction and Load operations. DBT’s only function is to take code, compile it to SQL, and then run against your database.

Multiple databases are supported, including:

- Postgres
- Redshift
- BigQuery
- Snowflake
- Presto

DBT can be easily installed using pip (the Python package installers) and it comes with both CLI and a UI.

- The **CLI** offers a set of functionalities to execute your data pipelines: **run** **tests**, **compile**, **generate documentation,** etc.

- The **UI** doesn’t offer the possibility to change your data pipeline and it is used mostly for **documentation** purposes.

One of the most important concepts in DBT is the concept of **model**. Every model is a select statement that has to be orchestrated with the other models to transform the data in the desired way. Every model is written using the query language of your favorite data warehouse (DW).

The output of each model can be stored in different ways, depending on the desired behavior:

- **Materialize** a table — full refresh
- **Append** to a table — incrementally build your output
- **Ephemeral** — the output will not be stored in your DW but it can be used as a data source from other models.

Every DBT model can be complemented with a **schema definition**. This means that the documentation lives in the same repository as your codebase, making it easier to understand every step of what has been developed.

DBT helps to serve high-quality data, allowing you to write different typologies of **tests to check your data**. Simple tests can be defined using YAML syntax, placing the test file in the same folder as your models. **More advanced testing** can be implemented using SQL syntax.

**Pros:**

- It is **Opensource** and open to customization.
- It is easy to apply **version control**
- The **documentation** lives with your DBT project and it is automatically generated from your codebase.
- It doesn’t require any specific skills on the jobs market. If your engineers are familiar with SQL and have a basic knowledge of Python, that’s enough to approach DBT.
- The **template** of each project is automatically generated running DBT init. This enforces a standard for all of our data pipelines.
- All of the computational work is pushed towards your DW. This allows you to attain **high performance** when using a technology similar to BigQuery or Snowflake.
- Because of the point above, orchestrating a DBT pipeline requires minimal resources.
- It allows you to **test your data** (schema tests, referential integrity tests, custom tests) and ensures data quality.
- It makes it **easier to debug complex chains of queries**. They can be split into multiple models and macros that can be tested separately.
- It’s **well documented** and the learning curve is not very steep.

**Cons:**

- **SQL based**; it might offer less readability compared with tools that have an interactive UI.
- **Lack of debugging** functionalities is a problem, especially when you write complex **macros**.
- Sometimes you will find yourself overriding DBT standard behaviour, rewriting macros that are used behind the scenes. This requires an understanding of the source code.
- The **UI is for documentation-only purposes**. It helps you to visualise the transformation process, but it’s up to your data engineers to keep the DBT project tidy and understandable. Having an interactive UI that allows you to visually see the flow of the pipeline and amend the queries can be helpful, especially when it comes to complex data pipelines.
- Documentation generation for BigQuery is time-consuming due to a poor implementation that scans all of the shards inside a dataset.
- **It covers only the T of ETL**, so you will need other tools to perform Extraction and Load.