## Intro to Gen Ai 

### Where Generative AI Fits in the AI and ML Landscape
Don't know what that is?
00:11
Then you're in the perfect place.
00:13
I'm Roger Martinez, and I am a developer relations engineer at Google Cloud.
00:17
And it's my job to help developers learn to use Google Cloud.
00:21
In this course, I'll teach you four things-- how to define generative AI, explain how generative AI works, describe generative AI model types, describe generative AI applications.
00:34
But let's not get swept away with all of that yet.
00:37
Let's start by defining what generative AI is first.
00:41
Generative AI has become a buzzword, but what is it?
00:45
Generative AI is a type of artificial intelligence technology that can produce various types of content, including text, imagery, audio, and synthetic data.
00:57
But what is artificial intelligence?
01:01
Since we are going to explore generative artificial intelligence, let's provide a bit of context.
01:07
Two very common questions asked are, what is artificial intelligence?
01:12
And what is the difference between AI and machine learning?
01:16
Let's get into it.
01:18
So one way to think about it is that AI is a discipline, like how physics is a discipline of science.
01:24
AI is a branch of computer science that deals with the creation of intelligent agents and our systems that can reason, learn, and act autonomously.
01:34
Are you with me so far?
01:35
Essentially, AI has to do with the theory and methods to build machines that think and act like humans.
01:42
Pretty simple, right?
01:44
Now, let's talk about machine learning.
01:47
Machine learning is a subfield of AI.
01:50
It is a program or system that trains a model from input data.
01:55
The trained model can make useful predictions from new, never-before-seen data drawn from the same one used to train the model.
02:03
This means that machine learning gives the computer the ability to learn without explicit programming.
02:08
[MUSIC PLAYING]

### Supervised and Unsupervised Machine Learning

Two of the most common classes of machine learning models are unsupervised and supervised ML models.
00:16
The key difference between the two is that, with supervised models, we have labels.
00:21
Labeled data is data that comes with a tag, like a name, a type, or a number.
00:28
Unlabeled data is data that comes with no tag.
00:32
So what can you do with supervised and unsupervised models?
00:37
This graph is an example of the problem a supervised model might try to solve.
00:42
For example, let's say you're the owner of a restaurant.
00:45
What type of food do they serve?
00:47
Let's say pizza-- or dumplings.
00:52
No, let's say pizza.
00:53
I like pizza.
00:54
Anyway, you have historical data of the bill amount and how much different people tipped based on the order type, pickup or delivery.
01:04
In supervised learning, the model learns from past examples to predict future values.
01:09
Here, the model uses a total bill amount data to predict the future tip amount based on whether an order was picked up or delivered.
01:17
Also, people, tip your delivery drivers.
01:20
They work really hard.
01:22
This is an example of a sort of problem that an unsupervised model might try to solve.
01:27
Here, you want to look at tenure and income and then group or cluster employees to see whether someone is on the fast track.
01:34
Nice work, blue shirt.
01:36
Unsupervised problems are all about discovery, about looking at the raw data and seeing if it naturally falls into groups.
01:43
This is a good start, but let's go a little deeper to show this difference graphically, because understanding these concepts is the foundation for your understanding of generative AI.
01:53
In supervised learning, testing data values, x, are input into the model.
01:59
The model outputs a prediction and compares it to the training data used to train the model.
02:04
If the predicted test data values and actual training data values are far apart, that is called error.
02:12
The model tries to reduce this error until the predicted and actual values are closer together.
02:17
This is a classic optimization problem.
02:21
So let's check in.
02:22
So far, we've explored differences between artificial intelligence and machine learning and supervised and unsupervised learning.
02:30
That's a good start.
02:31
But what's next?
02:33
Let's briefly explore where deep learning fits as a subset of machine learning methods.
02:38
And then I promise we'll start talking about GenAI.
02:41
[MUSIC PLAYING]

### Gen AI a subset of Deep Learning

[MUSIC PLAYING] SPEAKER: While machine learning is a broad field that encompasses many different techniques, deep learning is a
00:10
type of machine learning that uses artificial neural networks, allowing them to process more complex patterns than machine learning.
00:19
Artificial neural networks are inspired by the human brain.
00:23
Pretty cool, huh?
00:24
Like your brain, they are made up of many interconnected nodes, or neurons, that can learn to perform tasks by processing data and making predictions.
00:34
Deep learning models typically have many layers of neurons, which allows them to learn more complex patterns than traditional machine learning models.
00:43
Neural networks can use both labeled and unlabeled data.
00:47
This is called semi-supervised learning.
00:50
In semi-supervised learning, a neural network is trained on a small amount of labeled data and a large amount of unlabeled data.
00:58
The labeled data helps the neural network to learn the basic concepts of the tasks, while the unlabeled data helps the neural network to generalize to new examples.
01:08
Now we finally get to where generative AI fits into this AI discipline.
01:13
GenAI is a subset of deep learning, which means it uses artificial neural networks, can process both labeled and unlabeled data, using supervised, unsupervised, and semisupervised methods.
01:26
Large language models are also a subset of deep learning.
01:30
See, I told you I'd bring it all back to GenAI.
01:32
Good job, me.
01:34
Deep learning models or machine learning models in general can be divided into two types, generative and discriminative.
01:43
A discriminative model is a type of model that is used to classify or predict labels for data points.
01:48
Discriminative models are typically trained on a data set of labeled data points, and they learn the relationship between the features of the data points and the labels.
01:58
Once a discriminative model is trained, it can be used to predict the label for new data points.
02:05
A generative model generates new data instances based on a learned probability distribution of existing data.
02:12
Generative models generate new content.
02:15
Take this example.
02:17
Here, the discriminative model learns the conditional probability distribution, or the probability of y, our output, given x, our input, that
02:25
this is a dog and classifies it as a dog and not a cat, which is great because I'm allergic to cats.
02:33
The generative model learns the joint probability distribution, or the probability of x and y, p of xy,
02:42
and predicts the conditional probability that this is a dog and can then generate a picture of a dog.
02:48
Good boy.
02:49
I'm going to name him Fred.
02:52
To summarize, generative models can generate new data instances, and discriminative models discriminate between different kinds of data instances.
03:02
One more quick example.
03:05
The top image shows a traditional machine learning model, which attempts to learn the relationship between the data and the label, or what you want to predict.
03:13
The bottom image shows a generative AI model which attempts to learn patterns on content so that it can generate new content.
03:20
[MUSIC PLAYING]

### Is it gen AI or not

[MUSIC PLAYING] ROGER MARTINEZ: So what if someone challenges you to a game of, is it Gen AI or not?
00:12
I've got your back.
00:14
This illustration shows a good way to distinguish between what is Gen AI and what is not.
00:21
It is not Gen AI when the output, or y, or label, is a number or a class.
00:28
For example, spam or not spam, or a probability.
00:33
It is Gen AI when the output is natural language, like speech or text, audio, or an image like Fred from before, for example.
00:43
Let's get a little mathy to really show the difference.
00:47
Visualizing this mathematically would look like this.
00:50
If you haven't seen this for a while, the y equals f of x equation calculates the dependent output of a process given different inputs.
00:58
The y stands for the model output.
01:01
The f embodies the function used in the calculation or model, and the x represents the input or inputs used for the formula.
01:10
As a reminder, inputs are the data, like comma-separated value files, text files, audio files, or image files, like Fred.
01:20
So the model output is a function of all the inputs.
01:24
If the y is a number, like predicted sales, it is not generative AI.
01:30
If y is a sentence, like define sales, it is generative, as the question would elicit a text response.
01:39
The response will be based on all the massive large data the model was already trained on.
01:45
So the traditional ML supervised learning process takes training code and labeled data to build a model.
01:52
Depending on the use case or problem, the model can give you a prediction, classify something, or cluster something.
01:59
Now, let's check out how much more robust the generative AI process is in comparison.
02:06
The generative AI process can take training code, labeled data, and unlabeled data of all data types and build a foundation model.
02:15
The foundation model can then generate new content.
02:18
It can generate text, code, images, audio, video, and more.
02:25
We've come a long way from traditional programming to neural networks to generative models.
02:31
In traditional programming, we used to have to hard code the rules for distinguishing a cat-- type, animal; legs, four; ears, two; fur, yes; likes, yarn, catnip; dislikes, Fred.
02:49
In the wave of neural networks, we could give the networks pictures of cats and dogs and ask, is this a cat?
02:56
And it would predict a cat or not a cat.
02:59
What's really cool is that in the generative wave, we, as users, can generate our own content, whether it be text, images, audio, video, or more.
03:09
For example, models like Gemini, Google's multimodal AI model, or LaMDA, language model for dialogue applications, ingest very, very large data from multiple sources across the
03:19
internet and build foundation language models we can use simply by asking a question, whether typing it into a prompt, or verbally talking into the prompt itself.
03:30
So when you ask it, what's a cat, it can give you everything it's learned about a cat.
03:36
[MUSIC PLAYING]

## Gen AI : A formal approach
[MUSIC PLAYING] ROGER MARTINEZ: Now, let's make things a little more formal with an official definition.
00:10
What is generative AI?
00:13
Gen AI is a type of artificial intelligence that creates new content based on what it has learned from existing content.
00:21
The process of learning from existing content is called training and results in the creation of a statistical model.
00:28
When given a prompt, GenAI uses a statistical model to predict what an expected response might be.
00:34
And this generates new content.
00:37
It learns the underlying structure of the data and can then generate new samples that are similar to the data it was trained on.
00:44
Like I mentioned earlier, a generative language model can take what AI has learned from the examples it's been shown and create something entirely new based on that information.
00:54
That's why we use the word generative.
00:57
But large language models, which generate novel combinations of text in the form of natural sounding language, are only one type of generative AI.
01:07
A generative image model takes an image as input and can output text, another image, or video.
01:13
For example, under the output text, you can get visual question and answering.
01:18
While under output image, image completion is generated.
01:21
And under output video, animation is generated.
01:25
A generative language model takes text as input and can output more text, an image, audio, or decisions.
01:32
For example, under the output text, question and answering is generated, and under output image, a video is generated.
01:39
I mentioned that generative language models learn about patterns in language through training data.
01:44
Check out this example.
01:46
Based on things learned from its training data, it offers predictions of how to complete this sentence.
01:52
I'm making a sandwich with peanut butter and-- Jelly.
01:57
Pretty simple, right?
01:59
So given some text, it can predict what comes next.
02:03
Thus, generative language models are pattern-matching systems.
02:07
They learn about patterns based on the data that you provide.
02:10
Here is the same example using Gemini app, which is trained on a massive amount of text data
02:15
and is able to communicate and generate human-like texts in response to a wide range of prompts and questions.
02:21
See how detailed the response can be?
02:24
Here is another example that's just a little more complicated than peanut butter and jelly sandwiches.
02:29
The meaning of life is-- and even with a more ambiguous question, Gemini app gives you a contextual answer, and then shows the highest probability response.
02:40
[MUSIC PLAYING]

### Transformers , Hallucinations and Prompts
Transformers produce the 2018 revolution in Natural Language Processing.
00:16
At a high level, a transformer model consists of an encoder and a decoder.
00:22
The encoder encodes the input sequence and passes it to the decoder, which learns how to decode the representations for a relevant task.
00:30
Sometimes transformers run into issues, though.
00:33
Hallucinations are words or phrases that are generated by the model that are often nonsensical or grammatically incorrect.
00:41
See?
00:41
Not great.
00:44
Hallucinations can be caused by a number of factors, like when the model is not trained on enough
00:48
data, is trained on noisy or dirty data, is not given enough context, or is not given enough constraints.
00:58
Hallucinations can be a problem for transformers because they can make the output text difficult to understand.
01:04
They can also make the model more likely to generate incorrect or misleading information.
01:09
So put simply, hallucinations are bad.
01:13
Let's pivot slightly and talk about prompts.
01:16
A prompt is a short piece of text that is given to a Large Language Model, or LLM, as input.
01:23
And it can be used to control the output of the model in a variety of ways.
01:27
Prompt design is the process of creating a prompt that will generate the desired output from an LLM.
01:34
Like I mentioned earlier, generative AI depends a lot on the training data that you have fed into it.
01:40
It analyzes the patterns and structures of the input data, and thus learns.
01:45
But with access to a browser-based prompt, you, the user, can generate your own content.
01:50
[MUSIC PLAYING]

## Gen AI Model Types
[MUSIC PLAYING] SPEAKER: So let's talk a little bit about the model types available to us when text is our input
00:11
and how they can be helpful in solving problems, like never being able to understand my friends when they talk about soccer.
00:18
The first is text to text.
00:21
Text-to-text models take a natural language input and produce text output.
00:26
These models are trained to learn the mapping between a pair of texts, for example, translating from one language to others.
00:33
Next, we have text to image.
00:36
Text-to-image models are trained on a large set of images, each captioned with a short text description.
00:44
Diffusion is one method used to achieve this.
00:47
There's also text to video and text to 3D.
00:51
Text-to-video models aim to generate a video representation from text input.
00:56
The input text can be anything from a single sentence to a full script.
01:00
And the output is a video that corresponds to the input text.
01:04
Similarly, text-to-3D models generate three-dimensional objects that correspond to a user's text description for use in games or other 3D worlds.
01:14
And finally, there's text to task.
01:16
Text-to-task models are trained to perform a defined task or action based on text input.
01:22
This task can be a wide range of actions, such as answering a question, performing a search, making a prediction, or taking some sort of action.
01:30
For example, a text-to-text model could be trained to navigate a web user interface or make changes to a dock through a graphical user interface.
01:39
See, with these models, I can actually understand what my friends are talking about when the game is on.
01:45
Another model that's larger than those I mentioned is a foundation model, which is a large AI model pre-trained on a vast quantity of
01:51
data designed to be adapted or fine tuned to a wide range of downstream tasks, such as sentiment analysis, image captioning, and object recognition.
02:03
Foundation models have the potential to revolutionize many industries, including health care, finance, and customer service.
02:10
They can even be used to detect fraud and provide personalized customer support.
02:15
If you're looking for foundation models, Vertex AI offers a Model Garden that includes foundation models.
02:21
The language foundation models include chat, text, and code.
02:26
The Vision foundation models include Stable Diffusion, which have been shown to be effective at generating high quality images from text descriptions.
02:34
Let's say you have a use case where you need to gather sentiments about how your customers feel about your product or service.
02:41
You can use the classification task sentiment analysis task model.
02:45
Same for vision tasks.
02:47
If you need to perform occupancy analytics, there is a task-specific model for your use case.
02:53
[MUSIC PLAYING]

### Gen AI for Code Generation

[MUSIC PLAYING] SPEAKER: So those are some examples of foundation models we can use.
00:09
But can gen AI help with code for your apps?
00:12
Absolutely!
00:14
Shown here are generative AI applications.
00:17
You can see there's quite a lot.
00:19
Let's look at an example of code generation shown in the second block under the code at the top.
00:25
In this example, I've input a code file conversion problem, converting from Python to JSON.
00:32
I use Gemini and insert into the prompt box, I have a Pandas DataFrame with
00:35
two columns-- one with a filename and one with the hour in which it is generated.
00:42
I am trying to convert it into a JSON file in the format shown on screen.
00:46
Gemini returns the steps I need to do this.
00:49
And here, my output is in a JSON format.
00:52
Pretty cool, huh?
00:54
Well, get ready.
00:55
It gets even better.
00:56
I happen to be using Google's free browser-based Jupyter Notebook, and can simply export the Python code to Google's Colab.
01:03
So to summarize, Gemini code generation can help you debug your lines of source code, explain your code to you line by
01:10
line, craft SQL queries for your database, translate code from one language to another, and generate documentation and tutorials for source code.
01:21
[MUSIC PLAYING]

### Leveraging Google Cloud for Enhanced Generative AI
[MUSIC PLAYING] SPEAKER: I'm going to tell you about three other ways Google Cloud can help you get more out of generative AI.
00:11
The first is Vertex AI Studio.
00:15
Vertex AI Studio lets you quickly explore and customize generative AI models that you can leverage in your applications on Google Cloud.
00:24
Vertex AI Studio helps developers create and deploy generative AI models by providing a variety of tools and resources that make it easy to get started.
00:34
For example, there is a library of pre-trained models, tool for fine-tuning models, tool for deploying models to production, and community forum for developers to share ideas and collaborate.
00:47
Next, we have Vertex AI, which is particularly helpful for all of you who don't have much coding experience.
00:54
You can build generative AI search and conversations for customers and employees with Vertex AI Agent Builder.
01:01
Formerly, Vertex AI Search and Conversation.
01:04
Build with little or no coding and no prior machine learning experience.
01:09
Vertex AI can help you create your own chatbots, digital assistants, custom search engines, knowledge bases, training applications, and more.
01:21
Lastly, there is Gemini, a multimodal AI model.
01:25
Unlike traditional language models, it's not limited to understanding text alone.
01:30
It can analyze images, understand the nuances of audio, and even interpret programming code.
01:37
This allows Gemini to perform complex tasks that were previously impossible for AI.
01:43
Due to its advanced architecture, Gemini is incredibly adaptable and scalable, making it suitable for diverse applications.
01:51
Model Garden is continuously updated to include new models.
01:55
And now you know absolutely everything about generative AI.
02:00
Maybe you don't know everything, but you definitely know the basics.
02:03
Thank you for watching our course.
02:05
And make sure to check out our other videos if you want to learn more about how you can use AI.
02:10
[MUSIC PLAYING]


### Intro to LLM
How's it going?
00:07
I'm Megha.
00:08
Today I'm going to be talking about Large Language Models.
00:11
Don't know what those are?
00:11
Me either...
00:12
Just kidding!
00:13
I actually know what I'm talking about.
00:15
I'm a Customer Engineer here at Google Cloud and today I'm going to teach you everything you need to know about LLMs - that's short for Large Language Models.
00:23
In this course, you're going to learn to define Large Language Models, describe LLM use cases, explain prompt tuning, and describe Google's generative AI development tools.
00:34
Let's get into it...
00:35
Large Language Models, or LLMs, are a subset of deep learning.
00:39
To find out more about deep learning, check out our Introduction to Generative AI course video.
00:44
LLMs and generative AI intersect, and they are both a part of deep learning.
00:49
Another area of AI you may be hearing a lot about is generative AI.
00:53
This is a type of artificial intelligence that can produce new content - including text, images, audio, and synthetic data.
01:00
Alright, back to LLMs...
01:02
So, what are Large Language Models?
01:05
Large Language Models refer to large, general-purpose language models that can be pre-trained and then fine-tuned for specific purposes.
01:12
What do pre-trained and fine-tuned mean?
01:15
Great questions.
01:16
Let's dive in.
01:17
Imagine training a dog.
01:18
Often you train your dog basic commands such as sit, come, down, and stay.
01:24
These commands are normally sufficient for everyday life and help your dog become a good canine citizen.
01:29
Good boy!
01:30
But, if you need a special-service dog, such as a police dog, a guide dog, or a hunting dog, you add special trainings, right?
01:37
This similar idea applies to Large Language Models.
01:40
These models are trained for general purposes to solve common language problems such as text classification, question answering, document summarization, and text generation across industries.
01:52
The models can then be tailored to solve specific problems in different fields such as retail, finance, and entertainment, using a relatively small size of field datasets.
02:02
So, now that you've got that down, let's further break down the concept into three major features of Large Language Models.
02:08
We'll start with the word ‘large’.
02:11
Large indicates two meanings.
02:13
First, is the enormous size of the training dataset, sometimes at the petabyte scale.
02:18
Second, it refers to the parameter count.
02:20
In machine learning, parameters are often called hyperparameters.
02:23
Parameters are basically the memories and the knowledge that the machine learned from the model training.
02:28
Parameters define the skill of a model in solving a problem, such as predicting text.
02:33
So that's why we use the word ‘large’.
02:35
What about general purpose?
02:37
General purpose is when the models are sufficient to solve common problems.
02:41
Two reasons led to this idea.
02:43
First is the commonality of human language regardless of the specific tasks, and second is the resource restriction.
02:50
Only certain organizations have the capability to train such Large Language Models with huge datasets and a tremendous number of parameters.
02:58
How about letting them create fundamental language models for others to use?
03:02
So this leaves us with our last terms: pre-trained and fine-tuned, which mean to pre-train a Large Language Model
03:08
for a general purpose with a large dataset, and then fine-tune it for specific aims with a much smaller dataset.
03:14
So now that we've nailed down the definition of what Large Language Models (LLMs) are, we can move on to describing LLM use cases.
03:23
The benefits of using Large Language Models are straightforward.
03:26
First, a single model can be used for different tasks.
03:30
This is a dream come true.
03:32
These Large Language Models, that are trained with petabytes of data and generate billions of parameters, are
03:36
smart enough to solve different tasks, including language translation, sentence completion, text classification, question answering, and more.
03:46
Second, Large Language Models require minimal field training data when you tailor them to solve your specific problem.
03:53
Large Language Models obtain decent performance even with little domain training data.
03:58
In other words, they can be used for few-shot or even zero-shot scenarios.
04:02
In machine learning, ‘few-shots’ refers to training a model with minimal data and ‘zero shot’ implies
04:06
that a model can recognize things that have not explicitly been taught in the training before.
04:13
Third, the performance of Large Language Models is continuously growing when you add more data and parameters.
04:18
Large Language Models are almost exclusively based on transformer models.
04:23
Let me explain what that means.
04:25
A transformer model consists of an encoder and a decoder.
04:29
The encoder encodes the input sequence and passes it to the decoder, which learns how to decode the representations for a relevant task.
04:36
We've come a long way from traditional programing, to neural networks, to generative models.
04:41
In traditional programing, we used to have to hard code the rules for distinguishing a cat.
04:47
Type: animal.
04:48
Legs: four.
04:49
Ears: two.
04:51
Fur: yes.
04:51
Likes: yarn and catnip.
04:54
In the wave of neural networks, we could give the network pictures of cats and dogs and ask, ‘Is this a cat?’ And it would predict a cat.
05:01
What's really cool is that , in the generative wave, we, as users, can generate our own content - whether it be text, images, audio, video, or more.
05:10
For example, models like Gemini, Google's multimodal AI model, or LaMDA, Language Model for Dialog Applications, ingest very, very large data from multiple sources across the
05:21
internet and build foundation language models we can use simply by asking a question, whether typing it into a prompt or verbally talking into the prompt itself.
05:32
So, when you ask it ‘what's a cat?’ it can give you everything it has learned about a cat.
05:37
Let's compare LLM development using pre-trained models with traditional ML development.
05:43
First, with LLM development, you don't need to be an expert.
05:46
You don't need training examples, and there is no need to train a model.
05:50
All you need to do is think about prompt design, which is a process of creating a prompt that is clear, concise, and informative.
05:58
It is an important part of Natural Language Processing, or NLP for short.
06:02
In traditional machine learning, you need expertise, training examples, compute time, and hardware.
06:08
That's a LOT more requirements than LLM development.
06:11
Let's take a look at an example of a text generation use case to really drive the point home.
06:17
Question answering, or QA, is a subfield of Natural Language Processing that deals with the task of automatically answering questions posed in natural language.
06:26
QA systems are typically trained on a large amount of text and code, and they are able to answer a wide range of questions, including factual, definitional, and opinion-based questions.
06:37
The key here is that you needed domain knowledge to develop these Question Answering models.
06:42
Let's make this clear with a real-world example.
06:44
Domain knowledge is required to develop a question answering model for customer IT support, or healthcare or supply chain.
06:51
But, using generative QA, the model generates free text directly based on the context.
06:56
There is no need for domain knowledge!
06:59
Let me show you a few more examples of how cool this is.
07:02
Let's look at three questions given to Gemini, a Large Language Model chat bot developed by Google AI.
07:08
Question 1.
07:08
This year’s sales are $100,000.
07:12
Expenses are $60,000.
07:15
How much is net profit?
07:17
Gemini first shares how net profit is calculated, then performs the calculation.
07:21
Then, Gemini provides a definition of net profit.
07:25
Here's another question.
07:26
Inventory on hand is 6,000 units.
07:28
A new order requires 8,000 units.
07:30
How many units do I need to fill to complete the order?
07:34
Again, Gemini answers the question by performing the calculation.
07:38
And our last example, we have 1,000 sensors in ten geographic regions.
07:44
How many sensors do we have on average in each region?
07:47
Gemini answers the question with an example on how to solve the problem, and some additional context.
07:53
So how is that in each of our questions, a desired response was obtained?
07:58
This is due to prompt design.
08:00
Fancy!
08:01
Prompt design and prompt engineering are two closely related concepts in Natural Language Processing.
08:06
Both involve the process of creating a prompt that is clear, concise, and informative.
08:11
But, there are some key differences between the two.
08:14
Prompt design is the process of creating a prompt that is tailored to the specific task that the system is being asked to perform.
08:20
For example, if the system is being asked to translate a text from English to French,
08:25
the prompt should be written in English and should specify that the translation should be in French.
08:30
Prompt engineering is the process of creating a prompt that is designed to improve performance.
08:34
This may involve using domain-specific knowledge, providing examples of desired output, or using keywords that are known to be effective for the specific system.
08:44
In general, prompt design is a more general concept, while prompt engineering is a more specialized concept.
08:50
Prompt design is essential, while prompt engineering is only necessary for systems that require a high degree of accuracy or performance.
08:58
There are three kinds of Large Language Models: generic language models, instruction-tuned, and dialog-tuned.
09:04
Each needs prompting in a different way.
09:07
Let's start with generic language models.
09:10
Generic language models predict the next word based on the language in the training data.
09:14
Here is a generic language model.
09:16
In this example, the cat sat on...
09:19
The next word should be ‘the’ and you can see that ‘the’ is most likely the next word.
09:25
Think of this model type as an ‘auto-complete’ in search.
09:29
Next, we have instruction-tuned models.
09:32
This type of model is trained to predict a response to the instructions given in the input.
09:37
For example, summarize the text of x, generate a poem in the style of x, give me a list of keywords based on semantic similarity for x.
09:48
In this example, classify text into neutral, negative or positive.
09:54
And finally, we have dialog-tuned models.
09:57
This model is trained to have a dialog by the next response.
10:01
Dialog-tuned models are a special case of instruction tuned where requests are typically framed as questions to a chat bot.
10:08
Dialog-tuning is expected to be in the context of a longer back-and-forth conversation, and typically works better with natural, question-like phrasings.
10:17
Chain-of-thought reasoning is the observation that models are better at getting the right answer when they first output text that explains the reason for the answer.
10:25
Let's look at the question.
10:27
Roger has five tennis balls.
10:29
He buys two more cans of tennis balls.
10:31
Each can has three tennis balls.
10:33
How many tennis balls does he have now?
10:36
This question is posed initially with no response.
10:39
The model is less likely to get the correct answer directly.
10:43
However, by the time the second question is asked, the output is more likely to end with the correct answer.
10:50
But there is a catch...
10:52
There's always a catch.
10:53
A model that can do everything has practical limitations.
10:57
But, task-specific tuning can make LLMs more reliable.
11:03
Vertex AI provides task-specific foundation models.
11:07
Let's get into how you can tune with some real-world examples.
11:10
Let's say you have a use case where you need to gather how your customers are feeling about your product or service.
11:15
You can use a sentiment analysis task model.
11:19
Same for vision tasks.
11:19
If you need to perform occupancy analytics, there is a task-specific model for your use case.
11:26
Tuning a model enables you to customize the model response based on examples of the task that you want the model to perform.
11:34
It is essentially the process of adapting a model to a new domain or a set of custom use cases, by training the model on new data.
11:42
For example, we may collect training data and ‘tune’ the model specifically for the legal or medical domains.
11:48
You can also further tune the model by ‘fine-tuning’, where you bring your own dataset and retrain the model by tuning every weight in the LMM.
11:57
This requires a big training job and hosting your own fine-tuned model.
12:01
Here is an example of a medical foundation model trained on healthcare data.
12:06
The tasks include question answering, image analysis, finding similar patients, etc.
12:11
Fine-tuning is expensive and not realistic in many cases, so are there more efficient methods of tuning?
12:17
Yes.
12:19
Parameter-efficient tuning methods (PETM) are methods for tuning a Large Language Model on your own custom data without duplicating the model.
12:27
The base model itself is not altered.
12:29
Instead, a small number of add-on layers are tuned, which can be swapped in and out at inference time.
12:36
I'm going to tell you about three other ways Google Cloud can help you get more out of your LLMs.
12:42
The first is Vertex AI Studio.
12:45
Vertex AI Studio lets you quickly explore and customize generative AI models that you can leverage in your applications on Google Cloud.
12:53
Vertex AI Studio helps developers create and deploy generative AI models by providing a variety of tools and resources that make it easy to get started.
13:03
For example, there is a library of pre-trained models, a tool for fine-tuning models, a tool
13:09
for deploying models to production, and a community forum for developers to share ideas and collaborate.
13:16
Next, we have Vertex AI, which is particularly helpful for those of you who don't have much coding experience.
13:22
You can build generative AI search and conversations for customers and employees with Vertex AI Agent Builder, formerly Vertex AI Search and Conversation.
13:32
Build with little or no coding and no prior machine learning experience.
13:37
Vertex AI can help you create your own: chat bots, digital assistants, custom search engines, knowledge bases, training applications, and more.
13:48
Gemini is a multimodal AI model.
13:51
Unlike traditional language models, it’s not limited to understanding text alone.
13:55
It can analyze images, understand the nuances of audio, and even interpret programming code.
14:00
This allows Gemini to perform complex tasks that were previously impossible for AI.
14:05
Due to its advanced architecture, Gemini is incredibly adaptable and scalable, making it suitable for diverse applications.
14:13
Model Garden is continuously updated to include new models.
14:18
See?
14:18
I told you way back in the beginning of this video that I knew what
14:20
I was talking about when it came to Large Language Models, and now you do too.
14:26
Thank you for watching our course, and make sure to check out our other videos if you want to learn more about how you can use AI.