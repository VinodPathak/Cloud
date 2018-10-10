# Source with default settings
connectionString="Endpoint=sb://<>.servicebus.windows.net/;SharedAccessKeyName=<>;SharedAccessKey=<>;EntityPath=<>"
# = "amqps://pdmuser.servicebus.windows.net/myevent"
ehConf = {
  'eventhubs.connectionString' : connectionString
}

df = spark \
  .readStream \
  .format("eventhubs") \
  .options(**ehConf) \
  .load()
df = df.withColumn("body", df["body"].cast("string"))

df.writeStream \
  .format("delta") \
  .outputMode("append") \
  .option("checkpointLocation", "/delta/events/checkpoints/tweets") \
  .start("/delta/tweets")
