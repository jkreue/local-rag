# Running a local RAG build on n8n
This project is about setting up a local RAG on MacOS.
The setup requires an Apple Silicon Mac with 16G or more. It should be easy enough to be transfer to other platforms, given a comparable minimal setup. 

# What to expect
The reason of setting up a local RAG is primarily confidentiality and data privacy. It comes with limitations whne compared to cloud solutions, so be warned. This is slow and results may. The plus is that all your data stays on your machine and no conectivity is required.
This is work in progress. It provides the expected functionality, but on a MVP level.

## Tech Stack
* n8n
* Redis
* PostgreSQL + pgvector
* Ollama + qwen3:8B + nomic-embed-text
* Homebrew 
* nvm + nodeJS

## Install on MacOS
Open a Terminal.
If you do not have ***Homebrew*** installed, get it now:
    
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

and if you do have ***Homebrew*** installed, get it updated:

    brew update && brew upgrade

If you have nodeJS installed via Homebrew already, first unlink (in doubt, just do it):

    brew unlink node

Next we need ***nvm*** to manage nodeJS:

    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

Replace version with latest, check here: https://github.com/nvm-sh/nvm
n8n needs a specific older version of nodeJS, at the time of writing this is:

    nvm install 18.17.0

Now let's get Ollama. You can install via brew, but I prefer to download: https://ollama.com/download
Drop into you Application folder and run it. In the terminal pull the required models. This will take some time:

    ollama pull qwen3:8b
    ollama pull nomic-embed-text

Feel free to try other models. You will need one that is able to work with tools (see the tags in the Ollama library https://ollama.com/search
I found qwen3:8b to be working best for me. Other models might need tweaking of the prompts.

Now for the databases, ***Redis*** for the conversation memory and ***PostgreSQL*** as permanent vector store:

    brew install redis
    brew services start redis
    brew install postgres
    brew install pgvector
    brew services start postgresql

At last, we need ***n8n***:

    npm install n8n -g
    n8n

Your system is up and running now. Access at http://localhost:5678/  (You might want to bookmark this)
n8n is running in the terminal window, you will need to restart after closing it or reboot.

## Creating workflows in n8n
Visit http://localhost:5678/ and create an account. Then login with your new credentials.

### The chat
Click the plus button top left or the Create Workflow button top right to create a new workflow. Command+C the content of https://github.com/jkreue/local-rag/blob/main/RAG_chat.json, click into the workflow-canvas of your n8n instance and Command+V. This is your chat workflow, name it as you like, safe and switch the activation button on top to active.
Double-click on the 'When chat message received' node. On the top of the settings window, you will find your chat URL. You might want to bookmark this.

### The RAG embedding
Create a folder to store the documents you want to be digested by your RAG. A good path is something like /Users/YOURACCOUNT/Documents/RAG/
Create anoter workflow the same way as before. This time copy paste the content of https://github.com/jkreue/local-rag/blob/main/RAG_embedding.json
This is your embedding workflow. You will need to configure the folder to watch:
Double-click on the 'Local File Trigger' node and adjust the path in 'Folder to watch'. Get back to canvas, name the workflow as you like, safe and switch the activation button on top to active.

## Finally
You are all set. Drop the documents you want to use in your RAG into the folder, embedding will start automagically in the background. Depending on file size it just takes a few seconds for processing. The RAG can digest PDF, DOC, XLS, XLSX, ODS, EPUB, CSV, JSON, XML, HTML, RTF and TXT. 
Open the chat url: Happy chatting.

## What's next
As mentioned initially, this is an MVP.
A lot of functionality is missing for a full production ready tool:
- No success or error message on embedding,
- No document embedding via the chat,
- No document removal or db clean. What's in is in (see below how to wipe anyway)
- ...

## Manual db clean
In case you want to wipe the RAG db, open a terminal and type:

    psql -h localhost -U $(whoami) -d postgres
    TRUNCATE TABLE n8n_vectors RESTART IDENTITY;
    \q


    



    
    


