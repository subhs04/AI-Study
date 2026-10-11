## ML Ops

### Intro 

SPEAKER: Welcome to the Machine Learning Operations, MLOps, for Generative AI course.
00:06
In this course, you'll enter the complex landscape of MLOps strategies that are designed specifically for generative AI.
00:13
While the realm of generative AI is brimming with excitement and exploration, there are unique challenges in transitioning from captivating demos to real-world production.
00:25
This course will equip you with the knowledge and tools to overcome these challenges and harness the transformative power of generative AI.
00:33
By completing this course, you'll be able to recognize the evolution of MLOps and its role in shaping the
00:41
future of AI development, explore the emerging framework of MLOps specifically tailored for generative AI applications, explain the unique
00:51
challenges faced by MLOps teams when deploying and managing generative AI models, and explore how Gemini Enterprise Agent Platform,
01:01
commonly referred to as Agent Platform, empowers AI teams to streamline MLOps processes and achieve success in generative AI projects.

### Evolution of ML Ops

SPEAKER: Let's start by revisiting the essence of MLOps and delve into the reasons for its ongoing evolution.
00:08
If you've worked with ML models before, you know that training and deploying them can be a time consuming and iterative process.
00:18
To solve this problem, organizations need to build the necessary ML engineering culture and capability.
00:25
MLOps, with its standardized processes and technology infrastructure, provides the answer, enabling you to rapidly and reliably build, deploy, and operationalize ML systems.
00:38
There's no universally accepted definition for the distinction between non-generative AI and generative AI, but we're not going to enter that discussion in this course.
00:48
Instead, we'll focus on the core concepts about each kind of AI, before we talk about MLOps.
00:54
In order to highlight the difference between non-generative AI and generative AI, we'll use two terms.
01:03
We'll call non-generative AI predictive AI.
01:06
And we'll also talk about generative AI.
01:11
Predictive AI refers to a type of artificial intelligence that focuses on performing specific tasks or solving specific problems.
01:21
It doesn't have the ability to create new content or generate original ideas.
01:27
Instead, it relies on pre-existing data and algorithms to make predictions, classifications, or decisions.
01:34
For example, regression classification, object detection, or forecasting tasks can be listed as predictive AI tasks.
01:44
Within the AI research community, the term "generative" is primarily used for sophisticated models capable of producing high-quality human-like outputs.
01:55
Therefore, the term generative AI can be used for a type of artificial intelligence that focuses on
02:01
creating new and original content such as text, images, or music by learning from patterns in existing data.
02:12
Building upon the existing MLOps infrastructure, our objective is to seamlessly extend its capabilities to support the rapidly growing field of generative AI.
02:23
While predictive AI and generative AI share some fundamental principles, the latter introduces a unique set of challenges.
02:31
Our goal is to adapt and refine established MLOps processes to effectively address these challenges, and fully harness the potential of generative AI.
02:42
Let's revisit the two key phases of the traditional ML workflow-- training and serving. The training phase commences with an experimental stage,
02:53
where labeled data, ML algorithms, and trained models are employed to operationalize the training step. The serving or inference phase involves deploying the
03:03
model to an endpoint and obtaining predictions through an inference pipeline, utilizing new data, the served model and prediction requests. If we
03:15
focus on the training and serving stages, the traditional ML workflow can be simplified into four distinct phases-- experimentation, training, deployment, and prediction.
03:31
However, over the past few years, we've recognized the importance of evaluating and monitoring these models
03:37
consistently across all these phases, considering the ever changing nature of data and real world applications.
03:46
As a result, we've incorporated evaluation and monitoring phases into the overall workflow, encompassing both the training and serving stages.
03:56
In parallel, at the industry level, we've collectively fine tuned our practices in logging diverse artifacts such as
04:02
models in model registries, features in feature stores, and even pipeline directed acyclic graph in a template gallery.
04:12
Simultaneously, we have maintained consistent data and artifact governance throughout the entire training and serving lifecycle.
04:22
With a solid understanding of the core phases of the ML workflow and the importance of evaluation and monitoring, you're now equipped to dive
04:30
deeper into the specific complexities of MLOps for generative AI models, and discover how platform empowers you to overcome these hurdles and achieve success.

###  MLOps framework for generative AI

SPEAKER: Now that you're familiar with the traditional or predictive ML workflow, let's focus on how MLOps can be specifically tailored for generative AI applications.
00:12
MLOps for Gen AI can be defined as a set of practices, techniques, and technologies that extend MLOps principles to the operationalization of generative AI applications.
00:26
While the foundation of MLOps remains essential, generative AI introduces unique considerations that require additional layers and refinements to the traditional workflow.
00:39
Let's start with the experimentation phase.
00:42
This phase is distinct in two ways-- pre-trained model discovery, where the emphasis shifts from building models from scratch to discovering and leveraging pre-trained
00:52
models, and the direct prediction shortcut, where a direct path from the discovery and experimentation phase enables immediate prediction execution using natural language prompts.
01:05
After the discovery and experimentation phase, we proceed with training and serving our models as we would in any ML workflow.
01:13
However, with generative AI, we introduce two new phases-- customization and tuning, and curated data.
01:21
Customization and tuning.
01:22
Instead of training models from scratch, generative AI focuses on customizing and fine tuning pre-trained models for specific tasks.
01:31
This approach allows us to leverage the power of pre-trained models while tailoring them to our specific needs.
01:37
Curated data.
01:39
Generative AI relies on curated data sets rather than massive amounts of labeled data.
01:45
This necessitates a reevaluation of data lake and data warehouse strategies to ensure that we have the right data available for training and serving our models.
01:55
The other necessary adaptation of MLOps for GenAI is in the governing artifacts phase.
02:02
Generative AI introduces additional artifacts such as tuning jobs, adaptive layers, and embeddings that require governance.
02:11
The next adaptation is in the evaluating and monitoring phases.
02:15
Traditional metrics like accuracy and precision might not suffice for evaluating generative AI models.
02:22
New metrics that consider fluency, factuality, and brand reputation are often required.
02:28
Check the documentation for further details of new evaluation metrics and services Agent Platform offers.
02:35
In the final phase, curate data or incorporating enterprise data phase, it appears that foundation models are somewhat static, confined to the knowledge they acquired during pre-training.
02:47
The true potential lies in accessing data beyond their original training set, allowing them to perform more sophisticated tasks.
02:56
However, integrating additional data introduces new evaluation and monitoring challenges, increasing the overall complexity.
03:05
With these insights in hand, let's move on to explore the practical tools and strategies Agent Platform offers to streamline your generative AI MLOps journey.

### Navigating generative AI: embracing nuances and adapting MLOps

SPEAKER: In this section, we'll explore the unique challenges faced by MLOps teams when deploying and managing generative AI models.
00:08
We'll also learn how platform empowers AI teams to streamline MLOps processes and achieve success in generative AI projects.
00:18
The main idea here is that your existing MLOps investments remain relevant and valuable.
00:24
You can leverage the same platform for generative AI tasks like code and text generation and summarization.
00:31
However, effectively integrating generative AI requires a thorough grasp of the unique challenges it presents.
00:39
While generative AI holds immense promise, its integration into MLOps framework presents unique challenges that require careful consideration and tailored solutions.
00:50
Let's examine these challenges and explore the effective mitigation strategies to seamlessly integrate generative AI within the MLOps ecosystem.
01:01
The first challenge is increased AI infrastructure needs for pre-trained multitask models.
01:07
Generative AI models, especially large language models, demand substantial computational resources due to their complex architecture and extensive pre-training requirements.
01:18
This necessitates a robust AI infrastructure equipped with GPUs and TPUs to support training, experimentation, and deployment.
01:28
One effective mitigation strategy might be leveraging platform for efficient generative AI development.
01:34
Agent Platform provides a comprehensive platform for generative AI development, offering pre-trained models, a user-friendly interface for model customization, and optimized infrastructure for training and experimentation.
01:47
Agent Platform streamlines the exploration and experimentation phase for AI applications in a couple of ways.
01:55
The first is the Gemini Enterprise Agent Platform Model Garden.
01:59
Model Garden serves as your discovery gateway, providing access to a diverse array of models, including Google models, open-source models, and third party models.
02:09
It can help you understand which models best suit your use case without extensive data collection or labeling millions of examples.
02:16
Imagine effortlessly integrating functionalities like classification, summarization, and entity extraction into your applications with just simple text instructions, instead of dedicated ML pipelines and data sets for each.
02:31
Model Garden users can get started immediately with code examples to learn the best practice for training models on GPUs and TPUs.
02:39
Many of the models in the Model Garden can also be trained in a distributed fashion.
02:44
The second is Agent Studio on Gemini Enterprise Agent Platform.
02:49
This simplifies model customization by enabling you to fine-tune models with your own data.
02:55
It provides a user friendly interface for testing sample prompts, designing custom prompts, and adapting foundation models to align with your specific application requirements.
03:05
Agent Studio is a fully managed environment, which means that Google takes care of the underlying infrastructure.
03:12
This frees up developers and data scientists to focus on building and deploying applications.
03:18
You can also leverage Gemini Enterprise Agent Platform Managed Training and Gemini Enterprise Agent Platform Inference for fully managed compute infrastructure.
03:28
You just upload your data and Agent Platform Managed Training takes care of everything to run and scale with high availability.
03:36
This allows you to scale your ML training efficiently and focus on what matters most-- building and deploying powerful AI models.
03:44
The second challenge is customizing and tuning generative AI models.
03:49
Generative AI models often require fine tuning to align with specific tasks and domains.
03:55
This involves supervised tuning, reinforcement learning with human feedback, and extensive data curation.
04:02
Three services offered by agent platform can be leveraged to mitigate the challenges associated with customizing and tuning generative AI models.
04:11
Supervised tuning lets you leverage existing supervised learning techniques for tasks with well-defined outputs-- reinforcement learning with human feedback,
04:21
RLHF, which can be employed for tasks where defining the expected output is challenging, such as summarizing chat applications.
04:30
RLHF is offered for both Gemini models and open-source models like Llama 2.
04:36
And data curation, which means augmenting generic pre-training data with domain specific data to enhance model performance.
04:45
Let's move on to the next challenge.
04:47
Generative AI introduces additional artifacts such as prompts, embeddings, and adapter layers that require efficient management.
04:56
Managing those additional artifacts is another challenge for generative AI models.
05:01
At this point, existing tools and best practices on agent platform are still very useful.
05:07
The first is prompt management and analysis.
05:10
Prompt engineering is the art of crafting the perfect instructions to guide language models towards generating the desired output.
05:17
While it can be a meticulous process, there are valuable tools and frameworks available to assist in-depth prompt analysis and debugging.
05:25
For instance, tools like LangChain and Weights & Biases empower users to design prompts for a diverse range of
05:30
tasks, from straightforward text completion to more intricate natural language processing applications such as text summarization and code generation.
05:40
These tools enable users to provide specific instructions, context input data, and output indicators, ensuring that the language model receives the necessary guidance to produce the desired outcome.
05:51
The second is embedding management.
05:54
Embeddings transform unstructured data like text, images, and videos into dense vector representations, enabling powerful applications such as search, recommendations, and similarity matching.
06:05
You can leverage Agent Platform Feature Store to streamline the storage, management, and serving of these embeddings.
06:13
Next is adapter layer management.
06:15
Adapter tuning means updating only a small set of weights within those models.
06:20
Adapter layers are actually only tens of megabytes in file, and then get passed to our foundation model alongside your prompts to generate an inference.
06:29
You can employ model registry to manage your predictive AI models and generative AI models, including adapter layers.
06:37
Finally, is tuning job management.
06:40
Tuning jobs involve optimizing your models for improved performance.
06:45
Tuning generative AI models using pipelines blinds necessitates robust reproducibility, lineage tracking, and metadata management.
06:53
Agent Platform Pipelines provides a comprehensive platform for orchestrating and managing generative AI tuning jobs, enabling seamless
07:01
lineage tracking from data set to model, regardless of whether you're utilizing Spark, XGBoost, TensorFlow, or PyTorch.
07:11
Evaluating and monitoring generative AI models also poses unique challenges due to the unstructured nature of their outputs.
07:19
Generative AI models necessitate evaluating and monitoring not a single output, but rather extensive chunks of generated output, such as images and chat logs.
07:30
To effectively evaluate and monitor generative AI models, consider implementing strategies that address the unique challenges posed by their unstructured outputs.
07:39
With evaluation, you can develop metrics that consider factors like fluency, factuality, and brand reputation for mitigating evaluation challenges.
07:49
Gemini Enterprise Agent Platform Evals, commonly referred to as Gen AI evals, offer a range of evaluation targets, methods, and access to numerous metrics.
07:59
To evaluate the performance of a model, you first create an evaluation data set that contains prompts and ground-truth pairs.
08:06
For each pair, the prompt is the prompt that you want to evaluate, and the ground truth is the ideal response for that prompt.
08:14
During evaluation, the prompt of each pair in the evaluation data set is passed to the model to produce an output.
08:21
The output generated by the model and the ground truth from the evaluation data set are used to compute the evaluation metrics.
08:28
The metrics used for evaluation depend on the task that you're evaluating.
08:33
Check the official documentation for the supported tasks and the metrics used to evaluate each task.
08:39
With monitoring, you can utilize safety scores and recitation checking tools to ensure content quality and authenticity during the monitoring phase.
08:48
When inputs and outputs involve external or internal users, delegating input provision becomes essential.
08:55
It's crucial to understand the nature of the inputted or generated content.
09:00
Agent Platform addresses this challenge by generating an automatic payload with safety scores across more than 10 categories for each input and support request.
09:10
This valuable information facilitates monitoring and enables the setting of appropriate thresholds for various use cases.
09:17
Recitation checking plays a pivotal role in ensuring content authenticity.
09:22
To address concerns about the use of unoriginal content, built-in recitation checkers scan the model's output against existing code repositories and web articles.
09:32
Upon identifying significant matches, the system can either block substantial matches or provide a recitation within the API call output for matches exceeding a specific text character threshold.
09:44
These unique aspects of model monitoring significantly differ from traditional elements like training and serving
09:49
skew or tracking feature and prediction drift, offering diverse approaches to enhancing your model monitoring strategy.
09:57
Integrating generative AI models with enterprise data requires efficient data access and processing mechanisms.
10:05
To seamlessly integrate enterprise data into generative AI workflows, you can employ Agent Platform Services.
10:12
The first is Embeddings and Vector Search.
10:15
You can utilize embeddings to process and store enterprise data for efficient querying and analysis.
10:22
Agent Platform offers three core services-- a suite of Agent Platform embeddings APIs, a suite of embeddings across text
10:29
and image, allowing projection of both to the same vector space for semantic knowledge comparison, fully managed vector database.
10:38
Storing and retrieving these embeddings is facilitated by Agent Platform Vector Search, an Agent Platform Feature Store-- supporting embeddings
10:45
alongside existing ML features, and integration of Agent Platform models with third party tools and frameworks such as LangChain.
10:55
The second is grounding capabilities.
10:58
You can employ agent platform grounding capabilities to generate responses based on enterprise data, reducing the risk of hallucinations.
11:08
And the third is Agent Platform extensions.
11:11
You can use platform extensions to author and manage extensions that connect to real-time data and real-world actions.
11:19
These three capabilities extend the reach of generative AI into enterprise data.
11:25
Overall, by addressing these challenges and implementing the tailored adaptations discussed, you can effectively integrate generative AI into MLOps processes, unlocking the transformative potential of this technology.



