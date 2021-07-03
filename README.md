# rabbitmq-im
使用websocket连接rabbitmqtt搭建IM聊天

技术点：
Rabbitmq

首先：rabbitmq要先启用mqtt
命令如下：
rabbitmq-plugins enable rabbitmq_mqtt

后台c#代码：发送消息到mqtt
 /// <summary>
        /// 发送到队列
        /// </summary>
        /// <param name="message"></param>
        /// <param name="queue"></param>
        public void PublishMqtt(string message, string queue)
        {
            using (var connection = mc.CreateConnection())
            {
                using (var channel = connection.CreateModel())
                {
                    bool durable = true;
                    Dictionary<String, Object> args = new Dictionary<String, Object>();
                    args.Add("x-max-length-bytes ", 1048576);
                    channel.QueueDeclare(queue, durable, false, false, args);
                    var properties = channel.CreateBasicProperties();
                    properties.SetPersistent(true);
                    var body = Encoding.UTF8.GetBytes(message);
                    //交换器、队列、绑定
                    channel.BasicPublish("amq.topic", queue, properties, body);
                    // 这样就可以做到点对点通信了，amq.topic是默认exchange
               //     channel.BasicPublish("amq.topic", sessionId, null, messageBodyBytes);
                    System.Console.WriteLine(" set {0}", message);
                }
            }
        }
        
        
