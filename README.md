# Description

This is a chatbot application that provides users with deeper, personalized responses 
based on the capabilities of large language models processed by Amazon Bedrock.
This chatbot allows a user to have deeper conversations by usage of internal knowledge 
bases. For example, a user could have a conversation with a chatbot while browsing movies 
to watch, and a chatbot could suggest movies to a user that are relevant to their previous 
genres and high ratings. In the real world, chatbots are able to deliver industrial value by
incorporating information into generation of responses. 

# Motive

The problem of managing the development and deployment of many applications is something 
that is tackled by Amazon Web Services (AWS). AWS aims to streamline the development and 
deployment process by employing the Microservices Model. This involves breaking down the one large 
application into smaller, independent services that work together to fulfill the developer's 
intended function. 

The microservice model allows for these advantages:

* Scalability: Microservices can be independently scaled, allowing specific components to handle varying levels of load, optimizing resource usage.
* Flexibility: Developers can choose different programming languages, frameworks, and technologies for each microservice, enabling flexibility and innovation.
* Resilience: Failure in one microservice doesn't necessarily bring down the entire system, as other services can continue to function independently.
* Easy Maintenance: With smaller, focused services, it's easier to understand, test, and maintain codebases, leading to faster development cycles and easier updates.
* Autonomy: Teams can work on and deploy microservices independently, fostering autonomy and speeding up development and deployment processes.

# Read Before Following Chatbot Setup Instructions

* Notice: PDF Usage

The *Text_PDFs* folder will contain files that the user will need to provide to the AWS S3
Bucket in order to train the chatbot. They are able to be replaced with any kind of PDF that
concerns information for the user to use, such as an encyclopedia, dictionary, or instructional
pamphlet.

* Caveat: Cost

This chatbot uses a high level of back-end processing power to utilize a knowledge base that
is relevant to the user. In this current project, I have used a folder called *Text_PDFs* to 
hold the data that will be needed for the user to have a conversation with the chatbot that 
concerns Pokemon. It is a Japanese media franchise consisting of video games, animated series
and films, a trading card game, and other related media. 

The main concern when developing and using this chatbot is the possible amount of cost incurred.
I used the Pokemon PDFs to serve relevant information to user queries, which is held in a 
Serverless Vector store in Amazon OpenSearch. The huge caveat with the solution for this chatbot,
run through AWS, is the amount of pay-for-use costs that are incurred. Initially, I was 
completely unaware of the cost to be incurred with the use of those Pokemon PDFs, so I allowed
the OpenSearch vector store to process those PDFs for user query for a total of 7 hours. This
training cost was not much, but the amount of resources created by Amazon OpenSearch is high
enough to passively pass the AWS Free Tier pricing range. AWS OpenSearch incurred about $9 for 
two days of chatbot runtime, which I was not happy about paying because of my initial lack of 
cost management awareness.

Now, for an overview and explanation of RAG architecture.

# RAG Architecture

Retrieval Augmented Generation (RAG) is an advanced approach in artificial intelligence that enhances 
the capabilities of natural language processing models by integrating two key components: 

retrieval and generation.

* Retrieval:
At its core, retrieval involves sourcing information from a vast repository of data. In the 
context of RAG, this repository could be comprised of texts, articles, databases, or any other form of 
structured or unstructured data. In this repository, you will notice a selection of PDFs that concern
Pokemon - that is the topic of choice for this project, but the user is able to replace it with any kind
of content of their own choice. When prompted with a query or a topic, the system retrieves relevant 
information from this selection using search and retrieval algorithms.

* Generation:
Once the relevant information is retrieved, the generation component of RAG comes into
play. Rather than simply presenting the retrieved information as-is, the system employs natural
language generation techniques to create new content based on the retrieved information. This
content could take various forms, such as summaries, essays, or answers to questions.

What sets RAG apart is its ability to not only retrieve information but also to synthesize and generate 
new content based on the retrieved information. This dynamic interplay between retrieval and generation 
enables RAG to provide comprehensive and contextually relevant responses to queries or prompts, making 
it a powerful tool for tasks ranging from information retrieval and summarization to creative 
writing and content generation.

![sumgpt](https://github.com/isaiahglenncruz/Contextual_ChatBot/assets/72627685/49db6ca1-3902-42ef-ba4a-f6d87b7200fb)

The above image is a detailed illustration of each individual component of the User's prompt-to-response 
use of ARG architecture. The complexity of using the RAG approach comes from managing the complexities 
that will come with development and deployment. The services provided by AWS allow a user to 
manage their created application using tools in conjunction with one another. In this project, the following
services are used to mirror the above workflows:

* Amazon S3 - Data Source
* Amazon OpenSearch - Vector Store
* Amazon Bedrock + AWS Lambda - Embeddings Model

# Setup Instructions


