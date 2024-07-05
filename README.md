# React frontend dev flow with Github action and Docker

Creating a React app with a DevOps flow using GitHub Actions and Docker involves several steps. Below is a step-by-step guide to help you set up this workflow:

### Step 1: Create a React App

1. **Install Node.js and npm**: Ensure you have Node.js and npm installed on your system.
   - Download and install from [nodejs.org](https://nodejs.org/).

2. **Create a new React app**:
   ```sh
   npx create-react-app my-react-app
   cd my-react-app
   ```

### Step 2: Set Up a GitHub Repository

1. **Initialize a Git repository**:
   ```sh
   git init
   git add .
   git commit -m "Initial commit"
   ```

2. **Create a GitHub repository**:
   - Go to GitHub and create a new repository.

3. **Link your local repository to GitHub**:
   ```sh
   git remote add origin https://github.com/yourusername/your-repo-name.git
   git push -u origin master
   ```

### Step 3: Create a Dockerfile

1. **Create a `Dockerfile` in the root of your React app**:
   ```dockerfile
   # Use an official Node runtime as a parent image
   FROM node:14

   # Set the working directory
   WORKDIR /app

   # Copy package.json and package-lock.json
   COPY package*.json ./

   # Install dependencies
   RUN npm install

   # Copy the rest of the application code
   COPY . .

   # Build the React app
   RUN npm run build

   # Serve the React app
   FROM nginx:alpine
   COPY --from=0 /app/build /usr/share/nginx/html

   EXPOSE 80
   CMD ["nginx", "-g", "daemon off;"]
   ```

### Step 4: Create a GitHub Actions Workflow

1. **Create a `.github/workflows` directory**:
   ```sh
   mkdir -p .github/workflows
   ```

2. **Create a `main.yml` file in the workflows directory**:
   ```yaml
   name: CI/CD Pipeline

   on:
     push:
       branches:
         - master

   jobs:
     build:
       runs-on: ubuntu-latest

       steps:
         - name: Checkout repository
           uses: actions/checkout@v2

         - name: Set up Node.js
           uses: actions/setup-node@v2
           with:
             node-version: '14'

         - name: Install dependencies
           run: npm install

         - name: Run tests
           run: npm test

         - name: Build the project
           run: npm run build

         - name: Build Docker image
           run: docker build -t my-react-app .

         - name: Log in to Docker Hub
           run: echo ${{ secrets.DOCKER_PASSWORD }} | docker login -u ${{ secrets.DOCKER_USERNAME }} --password-stdin

         - name: Push Docker image
           run: docker tag my-react-app:latest yourusername/my-react-app:latest
           run: docker push yourusername/my-react-app:latest
   ```

### Step 5: Set Up Docker Hub

1. **Create a Docker Hub account**: If you don't have one, create it at [hub.docker.com](https://hub.docker.com/).

2. **Create a new repository on Docker Hub**.

3. **Add Docker Hub credentials to GitHub**:
   - Go to your GitHub repository settings.
   - Navigate to Secrets and add the following secrets:
     - `DOCKER_USERNAME`: Your Docker Hub username.
     - `DOCKER_PASSWORD`: Your Docker Hub password.

### Step 6: Push Changes to GitHub

1. **Commit and push your changes**:
   ```sh
   git add .
   git commit -m "Set up CI/CD with Docker"
   git push origin master
   ```

This setup will trigger a GitHub Action workflow every time you push to the master branch. The workflow will build and test your React app, create a Docker image, and push it to Docker Hub.

### Additional Steps (Optional)

- **Automated Deployment**: You can set up further steps to deploy your Docker image to a cloud provider like AWS, Azure, or Google Cloud.
- **Custom Domain and SSL**: Configure your cloud service to use a custom domain and SSL certificate for secure access.

This guide should give you a solid foundation for setting up a React app with GitHub Actions and Docker in a DevOps workflow. If you have any questions or need further customization, feel free to ask!