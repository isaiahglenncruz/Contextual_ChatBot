# Description
This is a chatbot application that provides users with deeper, more personalized responses 
based on the capabilities of large language models of data processed by Amazon Bedrock.
This chatbot allows a user to have deeper conversations by usage of internal knowledge 
bases. For example, a user could have a conversation with a chatbot while browsing movies 
to watch, and a chatbot could suggest movies to a user that are relevant to their previous 
genres and high ratings. In the real world, chatbots are able to deliver industrial value by
incorporating information into generation of responses. 

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

The main concern when training the chatbot using AWS is the amount of cost incurred.
I used the Pokemon PDFs to serve relevant information to user queries, which is held in a 
Serverless Vector store in Amazon OpenSearch. The huge caveat with the solution for this chatbot,
run through AWS, is the amount of pay-for-use costs that are incurred. Initially, I was 
completely unaware of the cost to be incurred with the use of those Pokemon PDFs, so I allowed
the OpenSearch vector store to process those PDFs for user query for a total of 7 hours. This
training cost was not much, but the amount of resources created by Amazon OpenSearch is high
enough to passively pass the AWS Free Tier pricing range. AWS OpenSearch incurred about $9 for 
two days of chatbot runtime, which I was not happy about paying because of my initial lack of 
cost management and awareness.

