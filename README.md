# ## Network protocols

### ___IP internet protocol___ 
Data sent in IP packets. Its a fundemantel unit. 2 main section:
- Header contains ingormation abaout the packet: Source and destination IP adress. Total size. Version: IPv4, IPv6
- Data: may 2 power 16 bytes. 

### ___Port___
You also have to know which port you want to talk to. There are also pre defined ports, but you can overwrite them. 

### ___TCP___ 
Transmission control protocol.
Built in top of IP. Help keeps tracking the packeages. As packages can be lost during transfer. It also checks the order. The IP packet also contains a TCP header. 
Handshake: Initialize connection. (Ack). 
Connection can be timed out. 

### ___HTTP___
Built on top of TCP.
Request-Response paradigm.
Request: 
- Host (destination)
- Port (destination)
- Method
- Path
- Headers
- Body

Response: 
 - Statuscode
 - Headers
 - Body

### OSI model
7 layer. Layer 7 is the appliacation layer(fe. HTTP), layer 4 is the Transport layer (fe. TCP). IF HTTPS than everything is encrypted in the message (Layer6 converts). Only the Destination IP and the soure IP is visible

##### ___Curl Command___
Best way to test requests. :)
##### ___DIG Command___
Make DNS queries.

## Storage

Disk or Memory. Disk persistent but slow, memory not persistent but fast!

## Latency and Throughput

Latency: The time it takes for a certain operation to complete in system. FOr example read 1 MB from storage, memory, Transfer data and so on.
Throughput: The number of operations that a system can handle properly per time unit. For example request / second.

## Availability
A system availability is measured in 9s. For example a 5 nine system is available in one year 99.999% (what is good). Availability comes with trade offs (redundancy, throughput). You must have to decide which part of your system should be HA and which not.

Redundancy: Active: All nodes are working. Passive: One node is working if it fails than another node takes over (leader election).

## Caching

Reduce latency. 

With immutable data it works like charm.
Caches easy can became stale. Solution -> one cache for all. 

Do caching when:
- immutable data
- only one node write and reads
- no consistency needed (for example view count)

Write through cache: it overwrites the cache then overwrites the db. 
Write back cache: only update the cache, then a different async process updates the DB.

How to get rid of caches?
RLU Policy. least recently used.
LFU. Least frequently used.

## Proxy

Forward: Hides the client.

Reverse: Hides the server. Load balancers are reverse proxies.

- layer 7: decisions made based on the HTTP request. Smart balancing. <- Good for Micro services. Caching. Cons: Expensive (not for real machines (maybe for a Rpi)). TLS termination. Two TCP connections.
- layer 4: decisions made based on load or just round robin. Pros: Uses NAT table. one TCP connection. fast. secure(don't encrypt data). Cons: No smart load balancing. <- Not good for Micro services. No caching.

How does a prox know where to forward a request to? 
>There are a number of types of proxies, and each uses a different approach to communicate to the proxy server what it wants to do.

>HTTP proxies understand HTTP only, and do not try to proxy packets, but instead HTTP commands like connect, get, post, etc. They create an entirely new packet addressed at the lower layers from themselves to the destination server. When a client initiates a connection, the first packet of the http flow contains the CONNECT verb. the proxy receives it, does a dns lookup on it if needed, and builds a packet to send to the remote server using the http commands and data flow from the packets it receives from the client.

>SOCKS proxies perform tunneling above the session layer, so the client configures a layer 5 header which tells the proxy where you want to connect, transport protocol info, and passes any authentication the proxy requires. The client places the Layer 6/7 datagrams into the layer 5 segment's data region, and sends it to the proxy. the proxy receives it, creates a new packet (without a SOCKS header) addressed to the remote server, places the layer 6/7 data units from the client packet into the new packet, and sends it to the destination server. SOCKS proxies don't work for all upper layer protocols, but they will proxy most lower layer protocols including tcp and udp.
[source](https://superuser.com/questions/622201/how-does-a-proxy-know-where-to-forward-the-requests-to)

## Load balancer

Scale horizontally. It reduces latency, and also throughput as you have more servers in charge.
Its a reverse Proxy. Maybe layer 7 or layer 4. Most likely layer 7 to achieve smart load balancing.

Servers register or deregister in the load balancer. 
Balancing load: 
- Round Robin: goes through all of the servers 1-2-3-1-2-3 
- Weighted Round Robin: More weighted servers get more load
- IP based: always hit the same server. Increase cache hit. 
- Redirected based on path. (Layer 7)

You can have multiple load balancers in one system.

Load balancers also do health checks. (/health)

To increase cash hits you should balance your load in a smart way. One way is to use caching. You hash a value(ip address, request...) and then map the hashe to a server. THis way the same client will always hit the same server. 
Naiv hashing is a 1-1 hash. Its bad as if a server dies, or you add more servers than all clients will have a new server.

Consistent hashing: It hashes the servers and then positions them in a circle. The hashed clients also will be positioned in a circle, and the clients requests the next server (fE clockwise).

Rendezvous hashing: It calculates a server ranking for a client.

SHA: Secure hash algorithms. collection of cryptographic hash functions used in the industry. SHA-3 is currently the best.


## Relational databases 

SQL
ACID
Database Index: slower write, lot faster read.
It has strong consistency. (Eventual consistency is when the databse can return stale data)

## Key value store (NoSQL)

Its basically a hashmap.
Super fast.
Maybe in memory, maybe saved on disk.

## Specialized storage paradigms

Blob Storage: 
- videos, images, sourcecode...
- only allow retrieve data based on the name of the blob
- Gooogle cloud store or S3

Time Sereis Databases:
- specialzed for time indexed data.
- fE IOT where you have tons of records in minute.
- useful if you have to do queries based on time
- InfluxDB and Graphite

Graph databases:
- data is strored following the graph model
- useful if you have to query deeply connected data fast
- fE social networking stuff
- Neo4j

Spatial database:
- best for stroing spatial data like locations
- Quadtree can be a spatial index

## Sharding and Replica

Replica is a backoff for your database. So if the master goes down the replica can be the new master. But it has a tradoff. All data will be duplicated and also have to think abaout the sync or async processes. Sync means high consistency but slow. Async is fast but data can be stale.

Sharding. Break up the database. For example you can break a users table into a-d e-g shards. Load balancing can be very helpful. YOu can choose a hash based load balancing strategy to avoid hot shards. 

## Leader election
Main problem is that the machines have to share state in the cluster, and agree on something.
It can be achieved with Consensus Algorithm. Like Paxos and Raft. 
Leader election can be outsourcd to a thord party service. Like ZooKeeper or ETCD.

## Peer to peer networks
Slice data into small chunnks (chunks have an identifier). Than peers can talk to each other. 
Tracker. A node who controlls the peers. 
Gossip. Peers figure out how to talk to each other.
Distributed Hash table. 
Uber kraken.

## Polling and streaming
Polling is asking the server in a pre defined intervall. For example every 30 sec. 
Streaming: for example a socket connection. It can push the data to the client. 

## Configuration
Static: YAML or JSON.
Dynamic: A DB where you save the config. Most of the time the Access is controlled. And also a deploy system is built araound.

## Rate limiting
Do not allow the user to hit our endpoints so much.  
Rate limits can beased on headers, Ip or anythign what you want.
Use Redis for distributed systems.
Rate limiting tiers is a good practice but complicated.

## Logging and Monitoring
Logging is the same as logging while debug.
The log messages shoud be meaningful and containt enough information (to be able to debug or create metrics)
Monitoring: metrics, and alerting. 

## Publish/Subscribe pattern
At least one delivery: There is situations where the subscriber can not ACK the message, fE it looses connection. Then the message will be sent again.
Idempotency: Do not matter how many times you call the operation the output will be the same. fE Update status to complete. Idempotent. Increase counter: not idempotent.
FIFO queues: First in first out.
There are solutions which you can replay messages.
Kafka, redis pubsub...

## MapReduce
3 steps: 
Map -> map the data into key/value pairs or something different.
Shuffle -> send the mapped data to the reducer
Reduce -> works on the mapped data. Has only one output.

The Reduce and the map functions HAS to be Idempotent.
One central point what is controlling the whole flow.
The whole system is fault tolerant. (thanks to idempotency)
