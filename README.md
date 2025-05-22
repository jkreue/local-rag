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


    



    
    


