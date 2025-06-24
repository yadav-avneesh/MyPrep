# System Design

## Table of Contents
1. [Core Principles & Fundamentals](#core-principles--fundamentals)
2. [Scalability Patterns](#scalability-patterns)
3. [Data Storage & Management](#data-storage--management)
4. [Distributed Systems Concepts](#distributed-systems-concepts)
5. [GenAI System Design](#genai-system-design)
6. [Microservices Architecture](#microservices-architecture)
7. [Performance & Monitoring](#performance--monitoring)
8. [Security & Compliance](#security--compliance)
9. [Interview Framework](#interview-framework)
10. [Real-World Examples](#real-world-examples)

---

## Core Principles & Fundamentals

### The 4 Pillars of System Design
1. **Scalability** - Handle increasing load gracefully
2. **Reliability** - System continues to work correctly during failures
3. **Availability** - System remains operational over time
4. **Consistency** - All nodes see the same data simultaneously

### Key Trade-offs (CAP Theorem)
- **Consistency vs Availability** - Can't have both during network partitions
- **Latency vs Throughput** - Optimizing one often hurts the other
- **Space vs Time** - Memory usage vs computational efficiency
- **Consistency vs Performance** - Strong consistency reduces performance

### Back-of-Envelope Calculations
- **QPS Estimation**: DAU × Actions per user / 86,400 seconds
- **Storage**: Records × Average record size × Replication factor
- **Bandwidth**: QPS × Average request/response size
- **Memory**: Cache hit ratio × Working set size

**Key Numbers to Remember:**
- 1 Million QPS = ~1000 servers (assuming 1K QPS per server)
- 1 TB = 1012 bytes ≈ 1 billion records (1KB each)
- Round trip within data center: 0.5ms
- Cross-continental round trip: 150ms

---

## Scalability Patterns

### Horizontal vs Vertical Scaling

**Vertical Scaling (Scale Up)**
- Add more power (CPU, RAM) to existing machines
- Simpler application design
- Limited by hardware constraints
- Single point of failure

**Horizontal Scaling (Scale Out)**
- Add more servers to resource pool
- Requires distributed system design
- Nearly unlimited scaling potential
- Better fault tolerance

### Load Balancing Strategies

**Layer 4 (Transport) Load Balancing**
- Routes based on IP and port
- Faster, less CPU intensive
- Cannot inspect application data
- Good for homogeneous backends

**Layer 7 (Application) Load Balancing**
- Routes based on application data (HTTP headers, URLs)
- More intelligent routing decisions
- Higher latency, more CPU intensive
- Enables advanced features (SSL termination, compression)

**Load Balancing Algorithms:**
- **Round Robin**: Simple rotation
- **Weighted Round Robin**: Based on server capacity
- **Least Connections**: Route to server with fewest active connections
- **IP Hash**: Route based on client IP hash
- **Geographic**: Route based on client location

### Caching Strategies

**Cache Levels:**
1. **Browser Cache** - Client-side caching
2. **CDN** - Geographic distribution
3. **Reverse Proxy** - Server-side cache
4. **Application Cache** - In-memory (Redis, Memcached)
5. **Database Cache** - Query result caching

**Cache Patterns:**
- **Cache-Aside (Lazy Loading)**: Application manages cache
- **Write-Through**: Write to cache and database simultaneously
- **Write-Behind**: Write to cache immediately, database asynchronously
- **Refresh-Ahead**: Proactively refresh cache before expiration

**Cache Eviction Policies:**
- **LRU (Least Recently Used)**: Remove least recently accessed
- **LFU (Least Frequently Used)**: Remove least frequently accessed
- **FIFO**: Remove oldest entries first
- **TTL**: Time-based expiration

---

## Data Storage & Management

### SQL vs NoSQL Decision Matrix

**Use SQL When:**
- ACID compliance required
- Complex queries and joins needed
- Data structure is well-defined and stable
- Strong consistency requirements
- Existing team expertise

**Use NoSQL When:**
- Massive scale (billions of records)
- Rapid development and frequent schema changes
- Simple queries (key-value lookups)
- Geographic distribution required
- Eventual consistency acceptable

### Database Scaling Patterns

**Read Replicas**
- Master handles writes, replicas handle reads
- Eventual consistency between master and replicas
- Good for read-heavy workloads
- Typical ratio: 1 master to 3-5 replicas

**Sharding (Horizontal Partitioning)**
- Split data across multiple databases
- Each shard handles subset of data
- Requires shard key selection
- Challenges: Cross-shard queries, rebalancing

**Sharding Strategies:**
- **Range-based**: Partition by value ranges
- **Hash-based**: Partition by hash of key
- **Directory-based**: Lookup service for shard location
- **Geographic**: Partition by user location

### Data Partitioning Best Practices

**Choosing Partition Keys:**
- High cardinality (many unique values)
- Even distribution of data
- Queries align with partition boundaries
- Avoid hotspots

**Common Partition Keys:**
- User ID (for user-centric applications)
- Timestamp (for time-series data)
- Geographic region
- Hash of natural key

---

## Distributed Systems Concepts

### Consistency Models

**Strong Consistency**
- All nodes see the same data simultaneously
- Achieved through consensus protocols (Raft, Paxos)
- Higher latency, lower availability during partitions
- Examples: Financial transactions, inventory management

**Eventual Consistency**
- System will become consistent over time
- Higher availability and performance
- Temporary inconsistencies acceptable
- Examples: Social media feeds, user profiles

**Session Consistency**
- Consistency within a user session
- User sees their own writes immediately
- Good balance between performance and user experience

### Consensus Algorithms

**Raft Algorithm**
- Leader-based consensus
- Simpler to understand than Paxos
- Used in etcd, Consul
- Strong consistency guarantees

**Two-Phase Commit (2PC)**
- Ensures atomicity across multiple resources
- Coordinator manages transaction phases
- Blocking protocol (not fault-tolerant)
- Used in distributed databases

### Circuit Breaker Pattern

**States:**
- **Closed**: Normal operation
- **Open**: Fast-fail mode when failures exceed threshold
- **Half-Open**: Test if downstream service recovered

**Benefits:**
- Prevents cascade failures
- Reduces resource exhaustion
- Faster failure detection
- Graceful degradation

---

## GenAI System Design

### GenAI Architecture Patterns

**Model Serving Infrastructure**

**GPU Resource Management:**
- **Model Sharding**: Split large models across multiple GPUs
- **Pipeline Parallelism**: Different layers on different GPUs
- **Tensor Parallelism**: Split tensors across GPUs
- **Data Parallelism**: Multiple model copies processing different batches

**Inference Optimization:**
- **Batching**: Process multiple requests together
- **Caching**: Cache embeddings and frequent responses
- **Model Quantization**: Reduce model precision (INT8, INT4)
- **KV-Cache**: Cache key-value pairs for transformer models

### GenAI-Specific Components

**Vector Databases**
- **Purpose**: Store and search high-dimensional embeddings
- **Technologies**: Pinecone, Weaviate, Chroma, pgvector
- **Key Features**: Similarity search, approximate nearest neighbor (ANN)
- **Scaling**: Sharding by embedding space, hierarchical clustering

**Embedding Services**
- **Text Embeddings**: OpenAI, Cohere, Sentence Transformers
- **Multimodal**: CLIP for text+image, DALL-E for image generation
- **Caching Strategy**: Hash input text → cached embedding
- **Batch Processing**: Group requests to improve GPU utilization

**RAG (Retrieval-Augmented Generation) Architecture**

```
User Query → Embedding → Vector Search → Context Retrieval → LLM → Response
     ↓            ↓           ↓              ↓           ↓        ↓
  Preprocessing  Cache    Vector DB     Ranking    Model API  Post-process
```

**Components:**
1. **Query Processing**: Intent detection, query expansion
2. **Retrieval**: Semantic search in vector database
3. **Ranking**: Re-rank results based on relevance
4. **Context Augmentation**: Combine query + retrieved context
5. **Generation**: LLM generates response with context
6. **Response Processing**: Fact-checking, safety filtering

### LLM Serving Patterns

**Synchronous Serving**
- Real-time inference for chat applications
- Low latency requirements (<2 seconds)
- Connection pooling and request queuing
- Auto-scaling based on queue depth

**Asynchronous Processing**
- Batch processing for non-real-time tasks
- Job queuing systems (RabbitMQ, Apache Kafka)
- Status tracking and result storage
- Better GPU utilization through batching

**Model Gateway Pattern**
- Route requests to different models based on requirements
- Load balancing across model instances
- Rate limiting and authentication
- A/B testing different models

### GenAI Scaling Challenges

**GPU Memory Management**
- **Model Size**: 7B parameter model ≈ 14GB memory (FP16)
- **Batch Size Optimization**: Balance latency vs throughput
- **Memory Fragmentation**: Use memory pools and pre-allocation
- **Multi-tenant GPU**: Careful resource isolation

**Prompt Engineering at Scale**
- **Template Management**: Version control for prompt templates
- **Context Length Optimization**: Truncation strategies
- **Few-shot Learning**: Dynamic example selection
- **Prompt Caching**: Cache responses for similar prompts

**Safety and Alignment**
- **Content Filtering**: Pre and post-processing filters
- **Hallucination Detection**: Fact-checking against knowledge base
- **Bias Monitoring**: Track model outputs for harmful biases
- **Human Feedback Loop**: RLHF integration

---

## Microservices Architecture

### Service Design Principles

**Single Responsibility**
- Each service has one business capability
- Clear domain boundaries
- Independent deployment and scaling
- Team ownership alignment

**API Design Best Practices**
- RESTful interfaces with clear resource models
- Versioning strategy (URL path, headers, or content negotiation)
- Idempotent operations where possible
- Comprehensive error handling and status codes

### Service Communication Patterns

**Synchronous Communication**
- **HTTP/REST**: Simple, widely supported
- **gRPC**: High performance, type-safe
- **GraphQL**: Flexible queries, single endpoint

**Asynchronous Communication**
- **Message Queues**: Point-to-point (RabbitMQ, Amazon SQS)
- **Event Streaming**: Publish-subscribe (Apache Kafka, Amazon Kinesis)
- **Event Sourcing**: Store events, not current state

### Service Discovery & Configuration

**Service Discovery Patterns**
- **Client-side**: Client queries registry (Eureka, Consul)
- **Server-side**: Load balancer queries registry
- **Service Mesh**: Infrastructure handles discovery (Istio, Linkerd)

**Configuration Management**
- **External Configuration**: Separate from code
- **Environment-specific**: Different configs per environment
- **Dynamic Configuration**: Update without restart
- **Secret Management**: Secure credential storage

---

## Performance & Monitoring

### Performance Metrics

**Golden Signals (SRE)**
1. **Latency**: Time to process requests
2. **Traffic**: Amount of demand on system
3. **Errors**: Rate of failing requests
4. **Saturation**: How "full" the service is

**Key Performance Indicators**
- **Response Time**: P50, P95, P99 percentiles
- **Throughput**: Requests per second (RPS)
- **Error Rate**: Percentage of failed requests
- **Availability**: Uptime percentage (99.9%, 99.99%)

### Monitoring Stack

**Metrics Collection**
- **Application Metrics**: Business and technical KPIs
- **Infrastructure Metrics**: CPU, memory, disk, network
- **Custom Metrics**: Domain-specific measurements
- **Tools**: Prometheus, DataDog, New Relic

**Logging Strategy**
- **Structured Logging**: JSON format for machine parsing
- **Log Levels**: DEBUG, INFO, WARN, ERROR, FATAL
- **Centralized Logging**: ELK Stack, Splunk, CloudWatch
- **Log Sampling**: Reduce volume while maintaining visibility

**Distributed Tracing**
- **Trace Requests**: Across multiple services
- **Span Correlation**: Parent-child relationships
- **Performance Analysis**: Identify bottlenecks
- **Tools**: Jaeger, Zipkin, AWS X-Ray

### Alerting Best Practices

**Alert Fatigue Prevention**
- Alert on symptoms, not causes
- Use appropriate thresholds (avoid noise)
- Implement alert escalation policies
- Regular alert review and tuning

**Runbook Development**
- Step-by-step troubleshooting procedures
- Common failure scenarios and solutions
- Contact information and escalation paths
- Post-incident review process

---

## Security & Compliance

### Authentication & Authorization

**Authentication Methods**
- **Multi-Factor Authentication (MFA)**: Something you know + have + are
- **OAuth 2.0**: Token-based authorization framework
- **JWT**: Stateless token format
- **SAML**: Enterprise single sign-on

**Authorization Patterns**
- **Role-Based Access Control (RBAC)**: Permissions based on roles
- **Attribute-Based Access Control (ABAC)**: Context-aware permissions
- **Zero Trust**: Never trust, always verify
- **Principle of Least Privilege**: Minimum necessary access

### Data Protection

**Encryption Standards**
- **At Rest**: AES-256 for stored data
- **In Transit**: TLS 1.3 for network communication
- **Key Management**: Hardware Security Modules (HSM)
- **Certificate Management**: Automated rotation and renewal

**Privacy Compliance**
- **GDPR**: Right to be forgotten, data portability
- **CCPA**: California Consumer Privacy Act
- **HIPAA**: Healthcare data protection
- **Data Classification**: Public, internal, confidential, restricted

### Security Architecture Patterns

**Defense in Depth**
- Multiple layers of security controls
- Perimeter security (firewalls, WAF)
- Network segmentation (VPCs, subnets)
- Application security (input validation, output encoding)
- Data security (encryption, access controls)

**Security Monitoring**
- **SIEM**: Security Information and Event Management
- **Threat Detection**: Behavioral analysis and anomaly detection
- **Incident Response**: Automated and manual response procedures
- **Vulnerability Management**: Regular scanning and patching

---

## Interview Framework

### System Design Interview Process

**1. Requirements Gathering (5-10 minutes)**
- Clarify functional requirements
- Discuss non-functional requirements (scale, performance, consistency)
- Identify constraints and assumptions
- Estimate scale (users, data, QPS)

**2. High-Level Design (10-15 minutes)**
- Draw major components and their interactions
- Show data flow through the system
- Identify key services and databases
- Discuss technology choices at high level

**3. Detailed Design (15-20 minutes)**
- Deep dive into specific components
- Database schema design
- API design and data models
- Address scalability and performance
- Discuss trade-offs and alternatives

**4. Scale the Design (10-15 minutes)**
- Identify bottlenecks
- Discuss scaling strategies
- Add caching, load balancing, sharding
- Consider monitoring and alerting

**5. Wrap Up (5 minutes)**
- Summarize key decisions
- Discuss additional considerations
- Address any remaining questions

### Common Interview Questions

**Design a Chat System (WhatsApp/Slack)**
- Real-time messaging with WebSocket/Server-Sent Events
- Message delivery guarantees and ordering
- User presence and typing indicators
- Message history and search
- Push notifications for offline users
- End-to-end encryption considerations

**Design a URL Shortener (bit.ly/TinyURL)**
- Base62 encoding for short URLs
- Database design for URL mappings
- Caching frequently accessed URLs
- Analytics and click tracking
- Custom aliases and expiration
- Rate limiting and spam prevention

**Design a Social Media Feed (Facebook/Twitter)**
- Push vs pull model for feed generation
- Fan-out strategies for posts
- Timeline generation and ranking
- Content recommendation algorithms
- Real-time updates and notifications
- Content moderation and safety

**Design a Video Streaming Service (YouTube/Netflix)**
- Video encoding and multiple quality levels
- Content Delivery Network (CDN) strategy
- Metadata storage and search
- Recommendation engine
- View counting and analytics
- Live streaming vs on-demand

**Design a Ride-Sharing Service (Uber/Lyft)**
- Real-time location tracking
- Driver-rider matching algorithms
- Route optimization and ETA calculation
- Pricing and surge pricing
- Payment processing
- Rating and review system

### GenAI-Specific Interview Questions

**Design a ChatGPT-like System**
- LLM serving infrastructure and GPU management
- Conversation context management
- Response streaming and real-time updates
- Safety filters and content moderation
- Usage tracking and rate limiting
- Model versioning and A/B testing

**Design a RAG-based Q&A System**
- Document ingestion and preprocessing
- Vector database for semantic search
- Embedding generation and caching
- Context ranking and retrieval
- LLM integration for response generation
- Fact-checking and source attribution

**Design an AI Code Assistant (GitHub Copilot)**
- Code context understanding
- Real-time code completion
- Model fine-tuning on codebases
- Language-specific adaptations
- Security and privacy considerations
- Integration with development environments

---

## Real-World Examples

### Netflix Architecture

**Core Components:**
- **API Gateway**: Zuul for request routing and filtering
- **Service Discovery**: Eureka for service registration
- **Configuration**: Archaius for dynamic configuration
- **Circuit Breaker**: Hystrix for fault tolerance
- **Monitoring**: Atlas for real-time monitoring

**Key Innovations:**
- Chaos Engineering with Chaos Monkey
- Microservices architecture at scale
- Regional failover capabilities
- Personalization algorithms
- Content delivery optimization

### Amazon Architecture Evolution

**Early Stage (Monolith)**
- Single application handling all functionality
- Vertical scaling approach
- Tight coupling between components

**Service-Oriented Architecture**
- Decomposition into services
- Service contracts and interfaces
- Horizontal scaling

**Modern Microservices**
- API-first design
- Event-driven architecture
- Serverless computing integration

### Google Search Architecture

**Crawling and Indexing:**
- Distributed web crawling
- Inverted index construction
- Real-time index updates
- Quality scoring algorithms

**Query Processing:**
- Query understanding and expansion
- Index lookup and ranking
- Result aggregation and presentation
- Personalization and localization

**Scalability Techniques:**
- Sharded index across multiple machines
- Caching at multiple levels
- Load balancing and geographic distribution
- Continuous deployment and monitoring

### GenAI in Production Examples

**OpenAI ChatGPT Architecture**
- Multi-model serving (GPT-3.5, GPT-4)
- Conversation state management
- Real-time response streaming
- Usage-based rate limiting
- Safety filtering pipeline

**Google Bard/Gemini**
- Multi-modal model serving
- Integration with Google services
- Real-time information retrieval
- Fact-checking and grounding

**Anthropic Claude**
- Constitutional AI approach
- Harmlessness and helpfulness optimization
- Human feedback integration
- Safety-first architecture

---

## Key Takeaways for Staff+ Engineers

### Technical Leadership Principles
1. **Think in Systems**: Understand how components interact and scale
2. **Embrace Trade-offs**: Every decision has costs and benefits
3. **Plan for Failure**: Design for resilience and graceful degradation
4. **Measure Everything**: Data-driven decision making
5. **Iterate and Improve**: Continuous improvement over perfection

### Communication Best Practices
- Start with business requirements and user impact
- Use diagrams and visual aids effectively
- Explain trade-offs and alternative approaches
- Be prepared to defend your design decisions
- Acknowledge limitations and areas for future improvement

### Staying Current with GenAI
- Follow major AI research developments
- Understand LLM capabilities and limitations
- Practice with embedding and vector databases
- Experiment with prompt engineering techniques
- Monitor AI safety and alignment research

---

*These comprehensive notes serve as both a study guide for system design interviews and a reference for day-to-day architectural decisions at FAANG-scale companies. Regular practice with real-world scenarios and staying updated with emerging technologies, especially in the GenAI space, is crucial for success at the staff+ level.*