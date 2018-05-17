Step1:

Unzip the file 'bikewise_project.zip' or do git clone and goto the project path.

Step 2 (If you are using Docker or skip to Step 3): 

If we are using Docker then, please create an image using Dockerfile and run the container by mapping port to 8080 to 8081.

docker build -t sathish86/bikewise-app .

docker run -v ~/workspace/bikewise_incidents/:/usr/src/app -t -i -d sathish86/bikewise-app 

Step 3:

Goto the container path "/usr/src/app/bike_app" (Docker) or project path in local directory

# npm install 

Step 4:

Serve the project

# yarn serve