## Step-by-step creating Local LLM and connect it to Routing Director LLM connector

1. You can use Ubuntu 22.04 VM to host the LLM connector (this VM must be reachable from Routing Director cluster)
2. Create 2 new folders (ollama and ollamaUI)
3. Copy docker-compose.yml file to Ubuntu machine
4. Install Docker containers: docker compose up -d
  <img width="1119" height="61" alt="image" src="https://github.com/user-attachments/assets/a69dcd32-be21-4661-abf5-60425348ba34" />

5. Ensure the Docker containers are running with: docker ps
6. Pull LLM (I am using Ollama for this test): docker exec -t ollama ollama pull mistral or docker exec -t ollama ollama pull llama3.2:3b (depends on your VM resource). You may head to https://ollama.com/library
   <img width="451" height="64" alt="image" src="https://github.com/user-attachments/assets/97896e9e-ef12-43f9-80d5-0b311f9055b0" />

7. When it is finished then you just need to configure the Routing Director LLM Connector setting from the GUI
   <img width="399" height="235" alt="image" src="https://github.com/user-attachments/assets/d425a187-ef3f-46ec-8a6d-c48b0708f386" />
