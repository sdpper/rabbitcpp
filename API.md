# API #

```
class AMQPMessage {

	public:	

		AMQPMessage(AMQPQueue * queue);		
		~AMQPMessage();
		
		void setMessage(char * data);
		char * getMessage();
		
		void setConsumerTag( amqp_bytes_t consumer_tag);
		void setConsumerTag( string consumer_tag);
		string getConsumerTag();		
				
		void setMessageCount( int count);
		int getMessageCount();

		void setExchange(amqp_bytes_t exchange);
		void setExchange(char * exchange);
		string getExchange();

		void setRoutingKey(amqp_bytes_t routing_key);
		void setRoutingKey(char * routing_key);
		string getRoutingKey();
		
		uint32_t getDeliveryTag();
		void setDeliveryTag(uint32_t delivery_tag);
		
		AMQPQueue * getQueue();
	
};


class AMQPBase {

	public:	
		~AMQPBase();		
		int getChannelNum();
		void setParam(short param);
		string getName();
};

class AMQPQueue : AMQPBase  {

	public:
		AMQPQueue(amqp_connection_state_t * cnn, int channelNum);
		AMQPQueue(amqp_connection_state_t * cnn, int channelNum, string name);

		void Declare();
		void Declare(string name);
		void Declare(const char * name);
		void Declare(string name, short parms);
		void Declare(const char * name, short parms);

		void Delete();
		void Delete(string name);
		void Delete(const char * name);

		void Purge();
		void Purge(string name);
		void Purge(const char * name);

		void Bind(string exchangeName, const char * key);
		void Bind(const char * exchangeName , string key);
		void Bind(string exchangeName, string key);
		void Bind(const char * queueName , const char * key);

		void unBind(string exchangeName, const char * key);
		void unBind(const char * exchangeName , string key);
		void unBind(string exchangeName, string key);
		void unBind(const char * queueName , const char * key);

		void Get();
		void Get(short param);

		void Consume();
		void Consume(short param);

		void Cancel(amqp_bytes_t consumer_tag);
		void Cancel(const char * consumer_tag);
		void Cancel(string consumer_tag);
			
		void Ack();
		void Ack(uint32_t delivery_tag);
					
		string getName();
		void setParam(short parms);

		AMQPMessage * getMessage();

		void setConsumerTag(string consumer_tag);
		void setConsumerTag(const char * consumer_tag);
		amqp_bytes_t getConsumerTag();
		
		void addEvent( AMQPEvents_e eventType, void * event );
				
		~AMQPQueue();
};


class AMQPExchange : AMQPBase {	

	public :
		AMQPExchange(amqp_connection_state_t * cnn, int channelNum);
		AMQPExchange(amqp_connection_state_t * cnn, int channelNum, string name);
		 
		void Declare();
		void Declare(string name);
		void Declare(const char * name);
		void Declare(string name, const char * type);		
		void Declare(const char * name, const char * type);		
		void Declare(string name, const char * type, short parms);		
		void Declare(const char * name, const char * type, short parms);		

		void Delete();
		void Delete(string name);
		void Delete(const char * name);

		void Bind(string queueName);
		void Bind(const char * queueName) ;
		void Bind(string queueName, const char * key);
		void Bind(const char * queueName , string key ) ;
		void Bind(string queueName, string key);
		void Bind(const char * queueName , const char * key ) ;

		void Publish(const char * message, const char * key);
		void Publish(string message, const char * key);
		void Publish(string message, string key);
		void Publish(const char * message, string key);
				
		string getName();
		void setParam(short param );
};

class AMQP {

	public:
		AMQP();
		AMQP( string cnnStr);
		AMQP(const char* cnnStr);
		~AMQP();
						
		AMQPExchange * createExchange();
		AMQPExchange * createExchange(string name);
		AMQPExchange * createExchange(const char * name);

		AMQPQueue * createQueue();
		AMQPQueue * createQueue(string name);
		AMQPQueue * createQueue(const char * name);

		void printConnect();
		
};
```