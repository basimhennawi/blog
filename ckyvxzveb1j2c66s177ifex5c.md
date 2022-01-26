## How To Query Data in AWS S3 using SQL

As a developer working on AWS cloud, you may encounter having an S3 object that contains JSON or CSV content that you want to run a SQL query against it. You may have chosen to retrieve the entire object in your application and then filter out only the required data for further analysis. But maybe there is a way you still can filter your data first in the cloud.

In this blog, we are going to demo how to do that:
- using S3 Select against one file (S3 object) or
- using Athena and Glue against multiple files to be aggregated.
 
### 1st approach: S3 Select

- Copy the following JSON sample of 5 best football players in the world in 2022 according to [Radiotimes magazine](https://www.radiotimes.com/tv/sport/football/best-football-players-world/):
```
{ "id": "1", "name": "Lionel Messi", "club": "PSG" }
{ "id": "2", "name": "Robert Lewandowski", "club": "Bayern Munich" }
{ "id": "3", "name": "Kevin De Bruyne", "club": "Man City" }
{ "id": "4", "name": "Mohamed Salah", "club": "Liverpool" }
{ "id": "5", "name": "Kylian Mbappe", "club": "PSG" }
```
then save it locally in a file called `sample.json`.

- Log into your AWS account via Console, navigate to S3 service, then inside a bucket of your choice (in our case `query-data-s3-sql`, remember it needs to be globally unique), upload `sample.json` file.

![UploadSampleToS3.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643211687397/Tduy5UnAb.png)

- Click on the S3 object you just upload then click on "Object actions" dropdown from top-right and chose "Query with S3 Select":

![QueryWithS3Select.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643211553455/UgWmFN9dk.png)

- Configure your input/output settings the way that meets your content's format whether CSV or JSON as well as content type (*Note: JSON is one entry per line without a comma at the end*) then you can query your data using SQL Select command in "SQL query" box. For example, let's select the name of the player with "id" equals "4" as follow:

```
SELECT s.name FROM s3object s WHERE s.id = '4'
```

so the output will be displayed below in the "Query results" box as follow:

![Result.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643212619416/tjWy25v7J.png)

*Note that you could also choose between some SQL templates as shown below.*

![SQLTemplates.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643214415987/TMqG9dMTA.png)

### Followup

That sounds perfect! Using S3 Select, which is just an API, we query JSON/CSV content in S3 object on the fly but what if we need to query against multiple S3 objects in the bucket at once or/and we need to do more operation on the data than just selection.

In this case, we need to use AWS Athena which is an interactive query service that makes it easy to analyze data in S3 using standard SQL and you pay only for the queries that you run. Also as per [docs](https://aws.amazon.com/athena), Athena is out-of-the-box integrated with AWS Glue Data Catalog, allowing you to create a unified metadata repository across various services, crawl data sources to discover schemas, populate your Catalog with new and modified table and partition definitions, and maintain schema versioning. Let's continue further our example using Athena and Glue.

### 2nd approach: Athena and Glue

- Let's upload another JSON file `sample2.json` with the rest of the top 10 players in the same bucket:

```
{ "id": "6", "name": "Erling Haaland", "club": "Dortmund" }
{ "id": "7", "name": "Harry Kane", "club": "Tottenham" }
{ "id": "8", "name": "Karim Benzema", "club": "Real Madrid" }
{ "id": "9", "name": "Trent Alexander-Arnold", "club": "Liverpool" }
{ "id": "10", "name": "Cristiano Ronaldo", "club": "Man Utd" }
```
- Navigate to "Athena" and first we need to set "Query result location" path in "Settings" to the same bucket in our case.

![Query result location.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643219665741/CgXgTragV.png)

- Go to "Data sources" from side menu, then "Connect data source", here you could choose where would you like to query your data from, in our case we're going to choose "S3 - AWS Glue Data Catalog"

![S3 - AWS Glue Data Catalog.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643219788507/upqGKI0m29.png)

- In order to create a table and retrieve the schema automatically we opt in "Create a crawler in AWS Glue" option, then hit "Create in AWS Glue".

![Create a crawler in AWS Glue.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643220024981/HUk2V8eUL.png)

- Being redirected to AWS Glue, now let's set up the crawler configuration, `AthenaDemo` as a name then specify "Crawler source type", in our case `Data stores` then we configure what exactly the repeat crawls of the data stores will crawl whether all folders or just new folders or changed folders, in our case we choose `Crawl all folders`.
- Configure "Data Store" step, by keeping the default selection so chosen data store as `S3` and leave "Connection" field empty, and keep  `Specified path in my account`, then you need fill in the path of the bucket, in our case `s3://query-data-s3-sql` then hit "Next" then no need to add another data sore, so another "Next".
- Create an IAM role by adding a suffix for the Role name, in our case `AthenaDemo`. If the data in the bucket are frequently changing then you need to set the crawler to run periodically, in our case we go for `Run on demand` then "Next".
- Create a database `athenademodb` and set the prefix added to tables to `tbl_` and hit "Next".
- Finally, review all steps and hit "Finish". Now you can select the newly created crawler and run it.

![ReviewGlueCrawler.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643220793195/u8ax0PF1c.png)

- After crawling is done, you will see that 1 table is added.

![TableCreated.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643221550076/4w4yr4Apb.png)

- Now you go back to Athena's query editor and choose our database `athenademodb` then you find the table added as `tbl_query_data_s3_sql`, the prefix plus the bucket's name, and finally, you can run SQL queries against your table as follow:

```
SELECT * FROM "athenademodb"."tbl_query_data_s3_sql" limit 10;
```

You get a total of 10 records aggregated from all files in the bucket as shown below:

![Output.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643221692468/q1ybUtZhP.png)

Also, you can now run other [DML queries](https://docs.aws.amazon.com/athena/latest/ug/functions-operators-reference-section.html) against this table not only select.

Of course, we used AWS Console for achieving that but you could use S3 Select or Athena using AWS SDK for your preferred language or AWS CLI.

### Conclusion

S3 Select can be used for simple queries based on a single S3 object. It is an S3 feature designed that works by retrieving a subset of an object’s data (using simple SQL expressions) instead of the entire object, which can be up to 5 terabytes in size. It can be used with Lambda to build serverless apps.

Athena can be used for complex queries on files, span multiple folders under S3 bucket. It can be used to process logs, perform ad-hoc analysis, and run interactive queries and joins. It can be used for business reporting as well as analytics tools.

That's it! I'd love for you to leave me feedback below in the comments! 


