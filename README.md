# CD task

create diagram
sg rules:
scripts


#### Create new Jenkins cd job
- In Source Code Management
  - Add your repo via ssh address with the correct credentials
  - Select the main branch as that has the merged dev branch from the last job
  - specify ssh agent - use eng130.pem as key
- In Build Triggers
  - Select Build after other projects are built
  - Projects to watch and add your CI job
  - Tick Trigger only if build is stable
- Go to build and add Execute shell
- Transfer all the files you need to the EC2 Instances `scp -v -r -o StrictHostKeyChecking=no app/ ubuntu@ec2-ip:/home/ubuntu/`
- SSH into the EC2 machine ```ssh -A -o "StrictHostKeyChecking=no" ubuntu@ec2-ip << EOF
                                        cd app
                                        npm install
                                        EOF```

- Now ssh  into ec2 from local host
- In jenkins launch npm in background `nohup node /app/app.js &`
- if all tests have passed, new job is triggered