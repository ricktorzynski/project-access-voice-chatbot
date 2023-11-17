Add user to docker group
```
sudo groupadd docker
```
Add your user to the docker group
```
sudo usermod -aG docker $USER
```
create a directory for your project
```
cd ~/github
mkdir project-access-voice-chatbot
cd project-access-voice-chatbot
```

To create the initial Docker container and directories on your local machine
```
docker run -u 1000:1000 -v $(pwd):/app rasa/rasa:3.6.13-full init --no-prompt
```
This will create the necessary files for a Rasa Chatbot

To run the chatbot in shell:

```
docker run -u 1000:1000 -it -v $(pwd):/app rasa/rasa:3.6.13-full shell
```

To train a model:
```
docker run -u 1000:1000 -v $(pwd):/app rasa/rasa:3.6.13-full train --domain domain.yml --data data --out models
```

Adding custom actions:
```
add new class to actions/actions.py
```

in data/stories.yml, make changes

in domain.yml, add custom actions

in endpoints.yml add

create a docker network
```
docker network create project-access
```

retrain your chatbot
```
docker run -u 1000:1000 -v $(pwd):/app rasa/rasa:3.6.13-full train --domain domain.yml --data data --out models
```

There is a bug in rasa/rasa-sdk docker image
  - missing python requests module
  
Create docker-compose.yml file to manually build modified rasa/rasa-sdk docker image
Create actions/Dockerfile.action file to add request module

build the new docker image
```
docker compose build
```

start up the new image
```
docker compose up
```

Tag the docker image created
```
docker tag <source image> <new tag>
docker tag rasa/rasa:3.6.13-full rasa/rasa:pa-rasa-core
docker tag rasa/rasa-sdk:3.6.2 rasa/rasa-sdk:pa-action-server
```

Using local images that I have tagged
shell:
```
docker run -u 1000:1000 -it -v $(pwd):/app --net project-access --name rasa-core rasa/rasa:pa-rasa-core shell
```

train
```
docker run -u 1000:1000 -v $(pwd):/app rasa/rasa:pa-rasa-core train --domain domain.yml --data data --out models
```

start up action server
```
docker run -u 1000:1000 -d -v $(pwd)/actions:/app/actions --net project-access --name action-server rasa/rasa-sdk:pa-action-server
```




