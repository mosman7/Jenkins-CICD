# Merging Github with automation
![cicd jobs](https://user-images.githubusercontent.com/115226294/200851535-ee25a857-9b09-4cb0-a84b-32854ad1385c.png)

# Job 1
##### Main branch:
- First we generate a new key `$ ssh-keygen -t ed25519 -C "mohamedosman998@hotmail.com"` - email must be github email
- Name the key `eng130-jenkins-osman`
- Leave password blank 
- Public key will be saved in `eng130-jenkins-osman.pub` in .ssh folder
- `ls` in .ssh and both public and private key should be there
- `cat eng130-jenkins-osman.pub` and copy all contents
- Create a new repo
- Connect to VSCode
- In repo settings, go to deploy keys and create new key
- Add the public key and name it

- Copy app and environment folder into new repo folder on localhost
- push these changes to github

- Log into Jenkins
- Create new job
- Name the job `osman-CI`, select `freestyle project` and create
- Give a brief description
- Select `Discard old builds` and set max to 3
- Select `GitHub project` and then copy the HTTPS link of repo (clock on code)
- In Office 365 Connector tick `Restrict where this project can be run` and input `sparta-ubuntu-node` might need to backspace and click again to get it to work
- In `Source Code Management` select `Git` and copy the ssh link from the same place you got the HTTPS link and paste it in.
- This will display an error as there is no private key

- Now we need to link the private key to allow access to github:
    - Click `add` and select `jenkins`
    - for `Kind`, select `SSH username with private key`
    - Enter a username 
    - Select `enter directly` on private key
    - Copy private key from .ssh folder in `eng130-jenkins-osman` - copy exactly everything
    - Leave passphrase blank and Add
- Find the private key in credentials and this error should now go
- In branches to build input the branch you are using either main or dev
- To set up webhook:
    - In `Build Triggers` select `github hook trigger` - after setting up webhook in github
- In `Build environment` select `Provide Node & npm bin`
- In `Build` select `Execute shell`
- Input commands to run app
```
cd app - Navigate to app folder
npm install
npm test
```
- Save
- You have now set up a jenkins job

# Job 2
- Create a new branch in github called `dev`
- Pull changes on vscode and branch into dev `git branch -M dev`
- Create job in jenkins
- Follow staps from job 1 but add the following
- In `branches to build` specify the dev branch
- Underneath that, in `Additional behavious` add new and select `Merge before build` 
    - Name of repository is `origin`
    - Branch to merge to is `main`
- In `build triggers`, select `build after other projects are built` and select your prejious jenkins job to follow
- Do not need a web hook
- Add post-build actions
    - Select `push only if build succeeds`
    - Select `merge results`
    - Add branch, `branch to push - main`, `Target - origin`
- Save
- Change something in the dev branch and commit
- Jobs 1 and 2 should now launch automatically
# Job 3
## Step 1
### First we need to create an environment and test if it works
- Create an ec2 instance
- Use app AMI
- use the defasult vpc
- set the security groups as below:
    - Allow port 22 for your ip
    - allow port 22 for jenkins ip
    - allow port 80 for access to the app
    - allow port 3000 for node app
    ![rules](https://user-images.githubusercontent.com/115226294/200851591-794f6fbc-2d36-4dbd-8974-5f71ad73df4f.png)

- launch ec2 instance
- ssh into instance and run npm start
- check if app is working
- ps aux to kill node app

## Step 2
### Now create a new jenkins job
#### In General
- Give a brief description
- Discard old builds - set to 3
- Github project - insert url to your github repo

#### In Office 365 connector
- Restrict where this project can be run
    - set to `sparta-ubuntu-node`

#### Source Code management
- Select Git
    - input https link from repo
    - select your private key
    - Branches to build - set to `main`

#### Build Environment
- Provide Node & npm bin
- SSH agent - select eng130.pem

#### Build
- Execute shell
    - run commands to copy app folder, ssh into instance, cd into app and run npm in background
```
rsync -avz -e "ssh -o StrictHostKeyChecking=no" app ubuntu@ec2-3-249-239-139.eu-west-1.compute.amazonaws.com:/home/ubuntu - copies app folder containing recent changes

ssh -A -o "StrictHostKeyChecking=no" ubuntu@ec2-3-249-239-139.eu-west-1.compute.amazonaws.com << EOF - ssh into instance

cd app
sudo killall -9 node    - kills previous npms running
npm install
nohup node app.js > /dev/null 2>&1 & - runs npm in background

EOF
```    

- Now save and build app

## Step 3
#### Now make changes to the app homepage 
- in vscode go to `index.ejs` and make changes 
- save and push to github
- rebuild project in jenkins
- go to ec2 public IP
- changes should be synced
