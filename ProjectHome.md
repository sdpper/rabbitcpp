rabbitcpp is a C++ library for Message Queue Server RabbitMQ (http://www.rabbitmq.com/) and support the AMQP (Advanced Message Queuing Protocol http://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol ). The C++ library is using the C-rabbitmq client library (http://hg.rabbitmq.com/rabbitmq-c/).

## Example Publish ##
```
#include "amqpcpp.h"
#include <iostream>
#include <memory>
int main (int argc, char** argv) {
	try {
		AMQP amqp;

		AMQPExchange * ex = amqp.createExchange("e");
		ex->Declare("e", "direct");

		AMQPQueue * qu2 = amqp.createQueue("q2");
		qu2->Declare();
		qu2->Bind( "e", "msg");		

		string ss = "message 1 ";
		/* test very long message
		ss = ss+ss+ss+ss+ss+ss+ss;
		ss += ss+ss+ss+ss+ss+ss+ss;
		ss += ss+ss+ss+ss+ss+ss+ss;
		ss += ss+ss+ss+ss+ss+ss+ss;
		ss += ss+ss+ss+ss+ss+ss+ss;
*/

		ex->setHeader("Delivery-mode", 2);
		ex->setHeader("Content-type", "text/text");
		ex->setHeader("Content-encoding", "UTF-8");

		ex->Publish(  ss , "msg"); // publish very long message
		
		ex->Publish(  "message 2 " , "msg");
		ex->Publish(  "message 3 " , "msg");
						
	} catch (AMQPException e) {
		std::cout << e.getMessage() << std::endl;
	}
	return 0;					
}
```

## Example Consume ##
```
int i=0;

void onCancel(AMQPMessage * message ) {
	cout << "cancel tag="<< message->getDeliveryTag() << endl;
}

int  onMessage( AMQPMessage * message  ) {

	char * data = message->getMessage();
	if (data)
		  cout << data << endl;
	
	i++;	
	cout << "#" << i << " tag="<< message->getDeliveryTag() << " content-type:"<< message->getHeader("Content-type") ;
	cout << " encoding:"<< message->getHeader("Content-encoding")<< " mode="<<message->getHeader("Delivery-mode")<<endl;
	if (i > 10) {	
		message->getQueue->Cancel( message->getConsumerTag() );
	}	
	return 0;
};

int main () {
	try {
		AMQP amqp;		
		AMQPQueue * qu2 = amqp.createQueue("q2");
		
		qu2->Declare();
		qu2->Bind( "e", "msg");		

		qu2->setConsumerTag("tag_123");
		qu2->addEvent(AMQP_MESSAGE, (void*) onMessage );
		qu2->addEvent(AMQP_CANCEL, (void*) onCancel );	

		qu2->Consume(AMQP_NOACK);//
										
	} catch (AMQPException e) {
		std::cout << e.getMessage() << std::endl;
	}
	return 0;					
}

```

## Example Get ##
```
#include "amqpcpp.h"
#include <iostream>
#include <memory>

int main () {
	try {
		AMQP amqp;		

		AMQPQueue * qu2 = amqp.createQueue("q2");
		qu2->Declare();		
				
		while (  1 ) {
			qu2->Get(AMQP_NOACK);

			AMQPMessage * m=qu2->getMessage();
			
			cout << "count: "<<  m->getMessageCount() << endl;											 
			if (m->getMessageCount() > -1) {
			
			cout << "message\n"<< m->getMessage() << "\nmessage key: "<<  m->getRoutingKey() << endl;
			cout << "exchange: "<<  m->getExchange() << endl;											
			cout << "Content-type: "<< m->getHeader("Content-type") << endl;	
			cout << "Content-encoding: "<< m->getHeader("Content-encoding") << endl;	
			} else 
				break;				
						
		}	
	} catch (AMQPException e) {
		std::cout << e.getMessage() << std::endl;
	}

	return 0;					
```

### CHANGELOG ###
```
19.04.2010 the first commit
20.04.2010 add the ack method
28.04.2010 add properties header 
```