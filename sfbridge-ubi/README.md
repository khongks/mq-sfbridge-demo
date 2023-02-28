

1. To build the image

docker build -t mqsfbridge \
--build-arg runmqsfb_QueueManager="QUEUEMGR" \
--build-arg runmqsfb_MQBaseTopic="/sf" \
--build-arg runmqsfb_MQChannel="" \
.
