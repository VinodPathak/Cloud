
### 1. Unzip File in Dataflow Before Reading

Links: https://stackoverflow.com/questions/32964657/unzip-file-in-dataflow-before-reading
```
Dataflow / Apache Beam support ZIP-compressed files in TextIO automatically: TextIO.read().from(filepattern) 
will automatically decompress files matching the filepattern according to their extension, and .zip is one of
the supported formats - in that case it will implicitly concatenate all files inside the .zip into a single 
file, and parse lines of text from that.

You can also specify compression type explicitly using TextIO.read().from(filepattern).withCompressionType(...)
if the files don't have an extension.
```

### 2. Google Cloud Dataflow vs Apache Beam

Links: https://stackoverflow.com/questions/44591782/google-cloud-dataflow-vs-apache-beam
```
Yes, I've had this issue recently when testing outside of GCP. This link help to determine what you need when 
it comes to apache-beam. If you run the below you will have no GCP components.

$ pip install apache-beam

If you run this however you will have all the cloud components.

$ pip install apache-beam[gcp]

As an aside, I use the Anaconda distribution for almost all of my python coding and packages management.
As of 7/20/17 you cannot use the anaconda repos to install the necessary GCP components. Hoping to work 
with the Continuum folks to have this resolved not just for Apache Beam but also for Tensorflow.
```

### 3. Dataflow BigQuery to BigQuery

Links: https://stackoverflow.com/questions/49265480/dataflow-bigquery-to-bigquery
```
from __future__ import absolute_import

import argparse
import logging

import apache_beam as beam

PROJECT='experimental'
BUCKET='temp1/python2'


def run():
    argv = [
            '--project={0}'.format(PROJECT),
            '--job_name=test1',
            '--save_main_session',
            '--staging_location=gs://{0}/staging/'.format(BUCKET),
            '--temp_location=gs://{0}/staging/'.format(BUCKET),
            '--runner=DataflowRunner'
    ]

    with beam.Pipeline(argv=argv) as p:

        # Read the table rows into a PCollection.
        rows = p | 'read' >> beam.io.Read(beam.io.BigQuerySource(query =  'Select * from `table.orders` where paid = false limit 10', use_standard_sql=True))

        # Write the output using a "Write" transform that has side effects.
        rows  | 'Write' >> beam.io.WriteToBigQuery(
                table='orders_test',
                dataset='external',
                project='experimental',
                schema='field1:type1,field2:type2,field3:type3',
                create_disposition=beam.io.BigQueryDisposition.CREATE_IF_NEEDED,
                write_disposition=beam.io.BigQueryDisposition.WRITE_TRUNCATE)


if __name__ == '__main__':
    logging.getLogger().setLevel(logging.INFO)
    run()
```
### 4. how-to-write-dictionaries-to-bigquery-in-dataflow-using-python
Link: https://stackoverflow.com/questions/47379190/how-to-write-dictionaries-to-bigquery-in-dataflow-using-python
```
ads_schema='Advertiser_ID:INTEGER,Campaign_ID:INTEGER,Ad_ID:INTEGER,Ad_Name:STRING,Click_through_URL:STRING,Ad_Type:STRING'

class BuildAdsRecordFn(beam.DoFn):
    def __init__(self):
      super(BuildAdsRecordFn, self).__init__()

    def process(self, element):
      text_line = element.strip()
      ads_record = self.process_row(element)      
      return ads_record

    def process_row(self, row):
        dict_ = {}

        record = row.split(",")
        dict_['Advertiser_ID'] = int(record[0]) if record[0] else None
        dict_['Campaign_ID'] = int(record[1]) if record[1] else None
        dict_['Ad_ID'] = int(record[2]) if record[2] else None
        dict_['Ad_Name'] = record[3]
        dict_['Click_through_URL'] = record[4]
        dict_['Ad_Type'] = record[5]
        return [dict_]

with beam.Pipeline() as p:

    (p | ReadFromText("gs://bucket/file.csv")
       | beam.Filter(lambda x: x[0] != 'A')
       | (beam.ParDo(BuildAdsRecordFn()))
       | WriteToBigQuery('ads_table', dataset='dds',
           project='doubleclick-2', schema=ads_schema))
      #| WriteToText('test.csv'))
```

### 5. How to read BigQuery table using python pipeline code in GCP Dataflow
Links: https://stackoverflow.com/questions/48386148/how-to-read-bigquery-table-using-python-pipeline-code-in-gcp-dataflow
```
Reading From BigQuery
> rows = (p | 'ReadFromBQ' >> beam.io.Read(beam.io.BigQuerySource(query=QUERY, use_standard_sql=True))

Writing to BigQuery
> rows | 'writeToBQ' >> beam.io.Write(
beam.io.BigQuerySink('{}:{}.{}'.format(PROJECT, BQ_DATASET_ID, BQ_TEST), schema='CONVERSATION:STRING, LEAD_ID:INTEGER', create_disposition=beam.io.BigQueryDisposition.CREATE_IF_NEEDED,
    write_disposition=beam.io.BigQueryDisposition.WRITE_TRUNCATE))

```
