# Filename: Dockerfile
#Choose Main Env
FROM python:3.8

# set the working directory in the container
WORKDIR /code

# copy the content of the local src directory to the working directory
COPY src/ .

# command to run on container start
CMD [ "python", "./helloworld.py" ]