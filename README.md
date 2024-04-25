This is a chatbot application that provides users with deeper, personalized responses.
This chatbot allows a user to have deeper conversations by usage of internal knowledge 
bases. For example, a user could have a conversation with a chatbot while browsing movies 
to watch, and a chatbot could suggest movies to a user that are relevant to their previous 
genres and high ratings. In the real world, chatbots are able to deliver industrial value by
incorporating information into response generation.

In daily conversations, the process of incorporating information into responses is also
called "providing context". Instead of creating a chatbot that would provide strict, 
unchanging responses, I decided to understand the internal processes that a chatbot would 
undergo in order to "provide context" just as we do daily.

# About the Microservices Model

The problem of managing the development and deployment of large applications is addressed by 
Amazon Web Services (AWS). AWS aims to streamline the development and deployment process by 
employing the Microservices model . The model involves breaking down the one large application 
into smaller, independent services that work together to fulfill the developer's intended function. 

The microservice model allows for these advantages:

* **Scalability**: Microservices can be independently scaled, allowing specific components to handle 
varying levels of load, optimizing resource usage.

* **Flexibility**: Developers can choose different programming languages, frameworks, and technologies 
for each microservice, enabling flexibility and innovation.

* **Resilience**: Failure in one microservice doesn't necessarily bring down the entire system, as 
other services can continue to function independently.

* **Easy Maintenance**: With smaller, focused services, it's easier to understand, test, and maintain 
codebases, leading to faster development cycles and easier updates.

* **Autonomy**: Teams can work on and deploy microservices independently, fostering autonomy and speeding 
up development and deployment processes.

# *Read Before Following Chatbot Setup Instructions*

* **Notice: Possibility for Arbitrary PDF Usage**

The *Text_PDFs* folder will contain files that the user will need to provide to the AWS S3
Bucket in order to train the chatbot. They are able to be replaced with any kind of PDF that
concerns information for the user to use, such as an encyclopedia, dictionary, or instructional
pamphlet.

* **Caveat: COST**

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
enough to passively pass the AWS Free Tier pricing range. AWS OpenSearch incurred about $21 for 
two days of chatbot runtime, which I was not happy about paying because of my initial lack of 
cost management awareness.

Now, for an overview and explanation of RAG architecture.

### RAG Architecture

Retrieval Augmented Generation (RAG) is an advanced approach in artificial intelligence that enhances 
the capabilities of natural language processing models by integrating two key components: 

retrieval and generation.

* **Retrieval**:
Retrieval involves sourcing information from a vast repository of data. In the 
context of RAG, this repository could be comprised of texts, articles, databases, or any other form of 
structured or unstructured data. In this repository, you will notice a selection of PDFs that concern
Pokemon - that is the topic of choice for this project, but the user is able to replace it with any kind
of content of their own choice. When prompted with a query or a topic, the system retrieves relevant 
information from this selection using search and retrieval algorithms.

* **Generation**:
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

* Amazon S3 - for storing data
* Amazon Bedrock - provides powerful searching and indexing for processing of data into word embeddings
* Amazon OpenSearch - usage of a vector store, which represents word embeddings as vectors in order to
  represent the semantic content of the data source
* AWS Lambda and AWS CloudFormation - used for API calls to Amazon Bedrock and resource provisioning

# *Setup Instructions*

Creating this project will not be possible as your AWS account's root user. You must be 
logged into an IAM role.

Clone the RAG_Chatbot repository into your desired directory.

### Dataset Setup
We will set up an S3 bucket to serve our knowledgebase.

1. Navigate to the Amazon S3 console. Choose **Buckets** under the *navigation* panel.

2. **Create bucket** and name it `knowledgebase-<aws-account-number>`, with the AWS
account number being your own account's account number.

3. Leave settings for this bucket as default, press **Create**.

4. Move to the `knowledgebase-<aws-account-number>` bucket, choose **Create folder** and
name it `dataset`. Leave all folder settings as default, press **Create**.

5. Upload the files in this repository's *Text_PDFs* folder to the bucket's `dataset`
folder. The files I have provided concern Pokemon, but you can provide any kind of
PDFs that you would like.

6. Move back to the bucket home, choose **Create folder** and name it `lambdalayer`. Leave
all folder settings as default, press **Create**.

7. Upload the `knowledgebase-lambdalayer.zip` file from this repository's *lambda_layer*
folder to the bucket's `lambdalayer` folder. Do not unzip the file!

### Knowledge base Setup
Using the previously created S3 bucket, we will create a knowledgebase.

1. Navigate to the Amazon Bedrock console. Choose **Orchestration** under the *navigation*
panel, and click on **Knowledge base**. Click on **Create Knowledge base**. You can 
provide it any name you would like. In the *IAM permissions* section, you will need to 
**Create and use a new service role** for which you will provide a name for. Here you
can also create tags for resource tracking. Click **Next**.

2. Leave *Data source name* as default.

3. Choose the S3 bucket `knowledgebase-<aws-account-number>` that you created earlier. 
Leave all other settings default, unless you would like to change any chunk loading 
strategies for higher amounts of data. Click **Next**

4. For the **Embeddings model**, click on **Titan Embedding G1 - Text**. For the 
**Vector database**, select **Quick create a new vector store**. Click **Next**, then 
scroll down and click **Create knowledge base**.

5. This process may take minutes to even hours. *HUGE* caution - using the Pokemon PDFs,
this process took a total of 5 hours. This will incur huge amounts of costs, so you may
want to provide your own PDFs. If you close the current tab, creation will fail. When
the knowledge base status is `ready`, write down the knowledge base ID.

![iddd](https://github.com/isaiahglenncruz/Contextual_ChatBot/assets/72627685/89ecd11d-29c5-44ac-9caa-2091f31dbd20)

6. In the *Data Source* section of the knowledge base's details page, click *Sync* to 
begin feeding the data from the S3 bucket into this knowledge base. The `Ready` status
will appear once ingestion is finished, so if you add any more files to the bucket,
then you will have to *Sync* again.

![readdd](https://github.com/isaiahglenncruz/Contextual_ChatBot/assets/72627685/c693a7e0-4f5d-498b-ace0-e5f699527314)

7. The above status should show up in the *Data Source* section of the knowledge base.

### Lambda Function Setup
Every time a user creates a query, this Lambda function is triggered.

1. Navigate to the AWS CloudFormation home page. Click **Create Stack**. Under **Prepare
Template**, select *Template is ready*. Select *Upload the template file* under **Template
Source**, and choose `DeployKnowledgeBase.yaml` from this repository. Click **Next**.

2. Provide any name to the Stack, the Knowledge base's ID, and your S3 bucket's name. Leave
all defaults the same, click **Next**, and **Submit**. If there are no errors, then the
application is ready to be tested.

### Testing!
All the microservices have been set up, so we can integrate them with our local environment
now.

1. In a new terminal window, run

`pip install boto3`

to install the AWS SDK for Python.

2. Run

`pip install streamlit` 

to set up an environment for the Streamlit application to run on.

3. Navigate to the GitHub repository's root, and run

`python -m streamlit run chatbot.py`

to open an application hosted by Streamlit in your Internet browser.

![typeshi](https://github.com/isaiahglenncruz/RAG_Chatbot/assets/72627685/7fd0d7fd-330e-4284-88f5-a9826c9b5e26)

When you are done using the application, make sure that you navigate through S3, 
OpenSearch, Lambda, and CloudFront - delete all resources and roles created, these will 
all incur costs as you leave them running, even if you are not using the application.

