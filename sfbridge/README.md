Before you build the image, replace these files in the folder ~/mq-sfbridge-demo/sfbridge

1) AMQCLCHL.TAB
2) keystore.jks

To build the image

docker build -t mqsfbridge \
--build-arg runmqsfb_QueueManager="QUEUEMGR" \
--build-arg runmqsfb_MQBaseTopic="/sf" \
--build-arg runmqsfb_MQChannel="" \
.

  Note the list of ARGs you are configured when you build the image:
  
  - runmqsfb_QueueManager
  - runmqsfb_MQBaseTopic
  - runmqsfb_MQChannel
  - runmqsfb_MQConname
  - runmqsfb_MQDLQname
  - runmqsfb_MQCCDTURL
  - runmqsfb_JndiClass
  - runmqsfb_JndiProvider
  - runmqsfb_MQUserName
  - runmqsfb_MQPassword
  - runmqsfb_SFUserName
  - runmqsfb_SFPassword
  - runmqsfb_SFToken
  - runmqsfb_SFEndpoint
  - runmqsfb_SFConsumerKey
  - runmqsfb_SFConsumerSecret
  - runmqsfb_KeyStoreFileName
  - runmqsfb_KeyStorePassword
  - runmqsfb_TrustedStoreFileName
  - runmqsfb_TrustedStorePassword
  - runmqsfb_UseTLSforMQ
  - runmqsfb_PushTopics
  - runmqsfb_PlatformEvents
  - runmqsfb_MonitorInterval
  - runmqsfb_QoS
  - runmqsfb_MQSubscribe
  - runmqsfb_AppDataFormat
  - runmqsfb_StartupWait
  - runmqsfb_RuntimeLogFile
