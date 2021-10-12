# Big Data Learning
When I study for AWS Data Analyst, I am looking for something hand-on that I can do. :) I found a interesting dataset on internet regarding to Mobile Base station on Shanghai, thanks for it !!!
###### http://www.sguangwang.com/TelecomDataset.html
##### [1] Yuanzhe Li, Ao Zhou, Xiao Ma, Shangguang Wang, Profit-aware Edge Server Placement, IEEE Internet of Things Journal, 2021, https://ieeexplore.ieee.org/document/9442389 [PDF] [Sourcecode]
##### [2] Y. Guo, S. Wang, A. Zhou, J. Xu, J. Yuan, C. Hsu. User Allocation‐aware Edge Cloud Placement in Mobile Edge Computing, Software: Practice and Experience, vol. 50, no. 5, pp. 489-502, 2020.[PDF] [Sourcecode]
##### [3] S. Wang, Y. Guo, N. Zhang, P. Yang, A. Zhou, X. Shen. Delay-aware Microservice Coordination in Mobile Edge Computing: A Reinforcement Learning Approach, IEEE Transactions on Mobile Computing, vol. 20, no.3, pp.939-953, 2021. [PDF]

### About Telecom Dataset
The dataset, provided by Shanghai Telecom, contains more than 7.2 million records of accessing the Interent through 3,233 base stations from 9,481 mobile phones for six months. For example, the following figure shows the distribution of base stations. Each node denotes a base station in Shanghai, China. This dataset could help researchers to evaluate their solution in mobile edge computing topic such as edge server placement, service migration, service recommendation, etc.

ID | Parameter Name	| Description
---|----------------|------------
1	 | Month	| The month when one record happens
2	| Date	| The date when one record happens
3	| Start Time	|The time when a record starts
4	|End Time|	The time when a record ends
5	|Base Station| Location	|The longitude and latitude of the base station where the mobile phones access the Interent
6	|User ID	| Mobile phone

### Description of Telecom Dataset
As shown in the following table, the Telecom dataset shows 6 parameters such as Month, Data, Start Time, End Time, Base Station Location, Mobile Phone ID. The trajectory of users can be found by the dataset.

### What we can do with this database ?
The most interesting of Big Data is not the coding. But it is what we can do with it to provide the useful information. Then and apply needed SQL.
I think:
1. Can we get top-ten Base Station Location, in that, the user access Internet mostly. Base on this, we can help mobile service provider to evaluate their solution, such as: evaluate the replacement/ migration service to minimize the DownTime, or increase the network bandwidht in specific BTS etc
2. Can we draw a "internet mobile access map" for specific user, in that, we can know where he/she access internet and the shop location nearby, so we may send suggested proper advertisement.

### Create AWS EMR with m4.large, Spark, Zepplin and JupyterGateway package
### Put the top-ten.py and data.csv to S3
### Add Job and run the query

```
import argparse
from pyspark.sql import SparkSession

def query_bts_location(data_source, output_uri):
    """
    Query top 10 BTS with high internet load and return long-lat attritutes
    """
    with SparkSession.builder.appName("Check BTS location").getOrCreate() as spark:
        # Load the restaurant violation CSV data
        if data_source is not None:
            bts_df = spark.read.option("header", "true").csv(data_source)

        # Create an in-memory DataFrame to query
        bts_df.createOrReplaceTempView("bts_data")

        # Create a DataFrame of the top 10 BTS with their location and with highest traffic
        top_ten_base_station_locations = spark.sql("""SELECT latitude, longitude, count(*) AS top_ten_base_station  
          FROM bts_data 
          GROUP BY latitude, longitude
          ORDER BY top_ten_base_station DESC LIMIT 10""")
        # Write the results to the specified output URI
        top_ten_base_station_locations.write.option("header", "true").mode("overwrite").csv(output_uri)

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument(
        '--data_source', help="The URI for you CSV data, like an S3 bucket location.")
    parser.add_argument(
        '--output_uri', help="The URI where output is saved, like an S3 bucket location.")
    args = parser.parse_args()

    query_bts_location(args.data_source, args.output_uri)
 ```
   
### Check the result in S3 bucket to see top-ten BTS output csv
### Query the long-latitude where subsciber access the internet (cont.)


