# My Little Fork

This is a fork of David Shapiro's [ChromaDB Chatbot](https://github.com/daveshap/ChromaDB_Chatbot_Public) with some bugfixes and added features.

# Why Did I Make This?

## Identifying the Problem

These fixes would have been just a pull request, but David turned the original repo into a read-only archive for reasons I cannot argue with. While trying out his original ChromaDB Chatbot, I discovered a fatal flaw in the system: **the chatbot only ever creates one KB article.** I tested numerous times, trying to add wildly different subjects to my chats (Green apples, Mount Everest, Kielbasa, etc.), but alas, it always updated the singular entry in the vector database, either erasing the previous information entirely or synthesizing both subjects together. Puzzled, I decided to get to the bottom of this, so I rolled up my sleeves, put on my best sleuthing hat, and dove into the code. 

## Diving into the Code

I quickly discovered that the code was quite simple. One Python file with a few helper functions and a main loop interacted with the user, vector database, and GPT API in sequence. As I made my way through the file, grateful that Python is such a readable language, everything seemed to be in good order. David had written the code in a nicely self-documenting fashion and sprinkled in some sparse comments to serve as guideposts. It wasn't until I walked myself through the logic a couple of times that it struck me - the code was missing a crucial step! You see, there was a bit of code that created a new knowledge base article, a bit that edited the current article, and a bit that split articles in two when they got too long. All of this was done conditionally with boolean logic and worked just as one would expect - except for one detail: **We never assessed if the user input context necessitated updating the existing KB article or creating a new one.** Without this crucial piece of logic, the code would only ever create a new KB article if none existed or edit the existing one. 

## The Missing Step

With the scent of victory tantalizingly close, the thrill of the chase pulled me in, and I started coding. I would not be deterred. Although my previous experience with Python amounted to zero, I made quick progress with the help of some of my favorite LLM powered tools (mainly phind.com and ChatGPT). I devised a plan to create some new functionality. In order to create this crucial bit of logic, this divining of article and user input similarity, I needed to find a way to assess the sameness of meaning between two abstract bits of written language. No small task for an ordinary computer program, and certainly not something I can simply throw a `==` at and be done with. 

## The Power of GPT-4

Fortunately, we have the magic of GPT-4 at our disposal. Herein lies the thing that makes ChatGPT such a wondrous tool - the making fluid of that which is by nature solid. A computer gone squishy, if you will. I digress. With the power of GPT-4, all I needed to do was ask, and my seemingly Herculean request was granted. I simply prompted the GPT API to look at the two pieces of text and decide: Do we update the knowledge base article, make a new one, or do nothing at all? I wrote the function, made sure to check if I was calling the GPT API correctly, and gently rearranged the existing logic of the main loop to make room for my addition. After dutifully double-checking my work and concluding that I had completed my task to the best of my ability (and attention span), I quietly entered `python chat.py` into my terminal. 

## The Results

It worked like a charm! As I fed the chatbot varied subjects, both similar and dissimilar to each other, GPT-4 was unfailingly working away in the background, making sound judgments and directing my application's next steps. "Make a new KB article here", "Edit the existing one there", "No action needed for this one, boss!" As if there was a little mind in my computer, GPT-4 doing just this little bit of cognitive labor made simple what used to be so complicated. 

## Conclusion

What a fantastic thing! If you took the time to read this whole thing, thank you so much for indulging me. I don't consider myself much of a writer, but I love to tell a good story now and then. All credit for this cool piece of software goes to David Shapiro. I urge you to check out [his YouTube channel](https://www.youtube.com/@DavidShapiroAutomator) where he discusses all things AI.

**TLDR: Chatbot was broken. Fixed chatbot. Now chatbot can decide based on context to edit KB article, make new KB article or make no changes to KB. GPT-4 is magic. Thank you David Shapiro!**

# ChromaDB Chatbot

Public version of ~~my~~ David Shapiro's ChromaDB chatbot that keeps track of user profile and historical topics. Should be mostly ready to go right out of the box. 

## Setup

1. Install chromadb and openai (in `requirements.txt` file)
2. Update `user_profile.txt` file with your initial information
3. Update `key_openai.txt` with your OpenAI API key

## Usage

- Main chat client: `python chat.py`
- Take a look in your KB: `python chromadb_peek.py`

## Code Explanation

This Python script serves as the implementation of a chatbot that leverages the OpenAI's GPT-4 model. It additionally integrates the chatbot with a persistent knowledge base using the ChromaDB library. Here's an overview of how the different parts of the script function:

1. **Utility Functions**: The script starts with several utility functions to handle file operations and to interact with OpenAI's API. These include functions for saving and opening files, and a function to run the chatbot, managing retries in case of exceptions.
2. **Main Application**: The script's main operation is contained within a continuous loop (`while True:`), enabling continuous interaction with the user. This loop does the following:
   - **Instantiates the ChromaDB client** for persistent storage and knowledge base management.
   - **Initiates the chatbot** by loading OpenAI's API key and preparing a conversation list.
   - **Captures user input** and adds it to the conversation list. The input is also logged in a separate file for record-keeping.
   - **Searches the knowledge base** for relevant content based on the current conversation and updates the chatbot's default system message accordingly.
   - **Generates a response** from the chatbot based on the conversation so far, which includes the updated default system message and the user's input.
   - **Updates the user profile** based on the user's recent messages, using the chatbot's response as the updated profile.
   - **Updates the knowledge base** with the most recent conversation, either adding a new entry or updating an existing entry. If an existing entry becomes too long, it's split into two separate entries.

The script logs all interactions with the OpenAI API and updates to the knowledge base, providing a record of the chatbot's operations and aiding in debugging and optimization efforts. The use of the ChromaDB library allows for scalable storage and retrieval of the chatbot's knowledge base, accommodating a growing number of conversations and data points.

But seriously just look at the code, it's pretty straight forward. 

## Contributing

You're welcome to submit a pull request to make mild changes or fix bugs. Any substantial refactors will be rejected. If you want to take this work and modify it, please just work on your own fork. This repo will eventually be made a public readonly archive. 
