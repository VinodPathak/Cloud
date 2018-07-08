
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
