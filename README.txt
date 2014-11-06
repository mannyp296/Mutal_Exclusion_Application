Ricart-Argrawala/Lamport Distributed Systems Application
By: Manuel Perez, Alex King


This is a program that makes use of the Ricart-Argrawala algorithm
to simulate mutual exclusion access to resources and the Lamport
Timestamps algorithm to update its clock according to the clock of
the copies.

////////////////////////////
Overview
////////////////////////////

this program, uses multithreading. It creates a separate thread for
each of the other ports it is connected to. These threads are used
to listen for incoming traffic. We do not use a separate thread for
sending messages; rather, we just create a function to send
messages to the other instances of the program. Each of the
messages sent between the programs includes a timestamp, the type
of message, and the ID of the process sending it. The type of
message is either a request for the resource or an “OK” message,
saying it is ok to acquire the resource. The ID of each process is
found by identifying the position of its port number relative to
all the other port numbers. For instance, the smallest port number
would have ID 1. After starting all the servers, our program enters
a large while loop in which it waits a random amount of time before
sending a request for the shared resource. The loop also causes a
delay which is proportional to the process’s ID, causing the
processes to run at different speeds. These loop includes checks to
see if there are any awaiting messages to be processed. 

////////////////////////////
Lamport’s Algorithm
////////////////////////////

Whenever a message is sent from one process to another, the message
includes the time at which the message is sent from the sending
process. The receiving process extracts the time stamp from the
message, checks its own time, and if its own time is lower than
the timestamp it updates its own time to that of the timestamp plus
one.

////////////////////////////
Ricart-Agrawala Algorithm
////////////////////////////

-Broadcasting:
Whenever a process wants to acquire a resource it has to broadcast
request to all other processes. It does so by using the send()
function and sending it to every single process using a for loop,
not a thread.

-Receiving Messages:
Each process creates a listening  thread for every other process it
has to communicate with. These listen treads are the receive points
for any messages sent using send(). Each thread represents a
separately accepted socket. The thread waits for incoming messages
form the process that the particular socket of the threa
drepresents. Each time a message is received it is decomposed into
its components: timestamp, process ID number, and type of  message.
After updating the process’ time using Lamport’s Algorithm if
needed, the process proceeds to queuing the message according the
queuing system. If the message is an acknowledgment, the thread
goes back to listening and lets the main function manage the
acknowledgment. If the message is a request, the thread looks into
the locking system  and the other process’ timestamp to determine
if it can send  a message to that process. If not, it queues the
message; otherwise it sends an acknowledgement message. The thread
returns to listening to incoming messages.

-Acknowledgments:
In order to let other processes know that a resource is not used,
a process responds with an acknowledgement.  These acknowledgments
are sent in similar fashion to broadcasting using send(). The
content of the two types of messages has a parameter that
distinguishes the two.

-Shared Resource Protection:
We use mutexes to protect the shared resources in our system. Each
time a process wants to gain control of the shared resources, it
has to first receive an OK messages from each of the other
processes. Then, it acquires the lock and keeps the resource for 5
clock cycles. Any processes that asks for the lock in that time
has to wait for it to release the lock before an OK message is
returned. In the odd case where one process is waiting to get
acknowledgement from the others and another process asks for
acknowledgement as well, the process with the later timestamp is
put into a queue so the earlier process gets the resource. Upon
release of the resource, the process sends an acknowledgement to
the other waiting process.

////////////////////////////
Queueing System
////////////////////////////

For incoming messages, we use a queueing system to process them.
We use arrays to implement the queues, traversing them in a
circular fashion. The listener thread adds all messages to the
queue when they come in, and then they would are processed in
the main thread. We use a secondary queue to distinguish request
messages from acknowledgement messages. The messages are
processed one at a time, allowing messages regarding them to be
printed one per line.
