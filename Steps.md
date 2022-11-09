### First we need to create an environment and test if it works
- Create an ec2 instance
- Use app AMI
- use the defasult vpc
- set the security groups as below:
    - Allow port 22 for your ip
    - allow port 22 for jenkins ip
    - allow port 80 for nginx
    - allow port 3000 for node app
    INSERT PIC
- launch ec2 instance
- ssh into instance and run npm start
- check if app is working
- ps aux to kill node app

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

#### Now make changes to the app homepage 
- 
- in vscode go to `index.ejs` and make changes 
- save and push to github
- rebuild project in jenkins
- go to ec2 public IP
- changes should be synced