WOG5
Overview
This project is a multi-game web application that includes three microservices: Memory Game, Guess Game, and Currency Roulette. Each game runs as a separate Docker container, and the main Flask app orchestrates these services. Scores are stored in a shared MySQL database.
Project Structure
WorldOfGames5/
├── flask_app/ # Main Flask application
├── memory/ # Memory Game microservice
├── guess/ # Guess Game microservice
├── roulette/ # Currency Roulette Game microservice
├── shared/ # Shared directory containing Score.py
├── tests/ # Selenium tests for all games
├── Dockerfile # Dockerfile for the Flask app
├── docker-compose.yml # Docker Compose file to set up all services
└── README.md # Project documentation
Why Score.py is Placed in a Volume
All three games—Memory Game, Guess Game, and Currency Roulette—need to use the Score.py file for updating the user’s score in the MySQL database. However, each game runs in its own Docker container, and Docker containers have isolated file systems.

Docker does not allow containers to access files located above their specific folder (build context). This would require replicating the Score.py file inside each game’s folder, which is not efficient and goes against the principle of avoiding duplication.
To solve this, the Score.py file is placed in a shared volume (C:/Shared on your host machine). By doing this, all three game containers can access Score.py without needing to replicate it across multiple directories. This approach simplifies file management and ensures consistency.

Setup and Installation

Step 1: Clone the repository
To get started, clone the project to your local machine.
git clone https://github.com/Ivgiko/WorldOfGames5.git
cd WorldOfGames5

Step 2: Create a shared folder
Create a C:/Shared folder on your local machine for storing shared files between the Docker containers and the host machine.

For Windows:
mkdir C:/Shared

For macOS/Linux (adjust folder path):
Since the docker-compose.yml is set up for C:/Shared, you will need to adjust the docker-compose.yml file to use a valid folder path for macOS/Linux. Update the volume path in the docker-compose.yml file before proceeding.
mkdir ~/Shared

Then, in the docker-compose.yml, update the shared volume path to:
volumes:

~/Shared:/app/shared
Step 3: Copy Score.py to the shared folder
Copy the Score.py file from the project directory to the shared folder.
For Windows:
copy shared/Score.py C:/Shared/
For macOS/Linux (if updated as mentioned above):
cp shared/Score.py ~/Shared/

Step 4: Run Docker Compose
In the root directory of the project, run the following command to build and start all the services (Flask app, game microservices, and MySQL database):
docker-compose up —build

Step 5: Verify that the Selenium tests pass
In the tests container logs, you should see the following message:
All tests passed successfully.

Step 6: Access the application
Once the services are running, open your browser and visit:
http://localhost:8777

Step 7: Stop and remove containers
Once you’re done, you can stop and remove all running containers with the following command:
docker-compose down
Known Issue

There is a bug in the Currency Roulette Game where the number in the question “How much is _ USD in ILS?” is blank. Despite extensive debugging, I was unable to resolve this issue fully. However, the game itself works correctly, and you can proceed with playing it.
