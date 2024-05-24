[![MasterHead](https://thecloudlegion.com/images/devops.gif)](https://rishavchanda.io)

---

<p>
<h1> Hey Again &nbsp;<img src="https://raw.githubusercontent.com/MartinHeinz/MartinHeinz/master/wave.gif" height="45" width="45"/>&nbsp;&nbsp;Welcome To My "ci_cd_github_action_aws" &nbsp;=)</h1>
</p>
<br/>

## 💻 Languages and Tools:

![Github](https://img.shields.io/badge/github-235835CC.svg?style=flat&logo=github&logoColor=white&label=.)



---

lets talk about this project:

this project is a github actions repo that work and connect to AWS

first guthub action how it work:
**".github/workflows/AwsInstanceRunningJob.yml"**

this action will deploy on you aws instance a website page with nginx.

steps:
- first if you have an instance copy the ip of this instance, if not, please create one and copy his ip.
- next in settings go to actions, then to runners, the click "new self hosted runner":
  
<img width="1282" alt="image" src="https://github.com/IftachZilcaPaz/ci_cd_github_action_aws/assets/151572520/79e0e497-8600-454b-8e3d-55b37e47583c">

- then choose the OS:
  
<img width="812" alt="image" src="https://github.com/IftachZilcaPaz/ci_cd_github_action_aws/assets/151572520/5a9775dd-ed99-4aca-b905-14c33d93ec81">

- then do those steps on youe instance:

## Download

```bash

# Create a folder
$ mkdir actions-runner && cd actions-runner
# Download the latest runner package
$ curl -o actions-runner-osx-x64-2.316.1.tar.gz -L https://github.com/actions/runner/releases/download/v2.316.1/actions-runner-osx-x64-2.316.1.tar.gz
# Optional: Validate the hash
$ echo "392b9d7b6d5b4d4f3814dbf874641b894f0c72447cdf05ce93158832d2d49b6b  actions-runner-osx-x64-2.316.1.tar.gz" | shasum -a 256 -c
# Extract the installer
$ tar xzf ./actions-runner-osx-x64-2.316.1.tar.gz
```

## Configure

```bash
# Create the runner and start the configuration experience
$ ./config.sh --url https://github.com/IftachZilcaPaz/ci_cd_github_action_aws --token BEENAKBJOTESLEFDCXXDBRTGKCFDC
# Last step, run it!
$ ./run.sh
```

## Using your self-hosted runner

```bash
# Use this YAML in your workflow file for each job
runs-on: self-hosted
```
<br/>
<br/>
<br/>

those lines will tell the script to run on the instance:

<img width="835" alt="image" src="https://github.com/IftachZilcaPaz/ci_cd_github_action_aws/assets/151572520/a7a5d19b-74a0-492f-8fb7-75a63ba8d41e">

<br/>
<br/>
<br/>
Diagram:

```mermaid
flowchart LR
    subgraph deploy-nginx
        direction TB
        A(run on self host) --> B(Check out repository code)
        B(Check out repository code) --> C(Install Nginx)
        C(Install Nginx) --> D(Start Nginx)
        D(Start Nginx) --> E(Enable Nginx to start on boot)
        E(Enable Nginx to start on boot) --> F(Print the status of Nginx)
        F(Print the status of Nginx) --> G(Stop Nginx)
    end
    %% ^ These subgraphs are identical, except for the links to them:

    %% Link *to* subgraph1: subgraph1 direction is maintained
    
    id1(((Github action start to run))) -.-> deploy-nginx
    %% Link *within* subgraph2:
    %% subgraph2 inherits the direction of the top-level graph (LR)
    %% outside ---> top2

```

