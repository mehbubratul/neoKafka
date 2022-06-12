# System Architecture

We will create a simple system that consists of three microservices. 
The order-service sends orders to the Kafka topic called orders. 
Both other microservices stock-service and payment-service listen for the incoming events. 
After receiving them they verify if it is possible to execute the order. 
For example, if there are no sufficient funds on the customer account the order is rejected. 
Otherwise, the payment-service accepts the order and sends a response to the payment-orders topic. 
The same with stock-service except that it verifies a number of products in stock and sends a response to the stock-orders topic.

Then, the order-service joins two streams from the stock-orders and payment-orders topics by the order’s id. 
If both orders were accepted it confirms a distributed transaction. 
On the other hand, if one order has been accepted and the second rejected it performs rollback. 
In that case, it just generates o new order event and sends it to the orders topic. 
We may treat the orders topic as a stream of the order’s status changes or just like a table with the last status. 
Here’s the picture that visualizes our scenario.
