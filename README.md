# Build, Test and Deploy

This repository demonstrates a simple GitHub Actions workflow that builds, tests, and deploys a project using a playful command-line program called Cowsay.

## Workflow Overview

The GitHub Actions workflow triggers on a push event and consists of three main jobs: build, test, and deploy.

### Jobs

1. Build
   - Environment: Ubuntu Latest
   - Steps:
     - Checkout the code from the repository.
     - Install the Cowsay program.
     - Execute a Cowsay command and save the output in a file named `dragon.txt`.
     - Upload the `dragon.txt` file as an artifact for use in the subsequent jobs.

2. Test
   - Needs: This job depends on the build job.
   - Environment: Ubuntu Latest
   - Steps:
     - Download the `dragon.txt` file artifact produced by the build job.
     - Perform a test to check if the string "dragon" exists within the file.

3. Deploy
   - Needs: This job depends on the test job.
   - Environment: Ubuntu Latest
   - Steps:
     - Download the `dragon.txt` file artifact produced by the build job.
     - Read the contents of `dragon.txt` and display it in the console.
     - Output a message indicating that the deployment process is starting.

## Getting Started

To utilize this workflow in your own project, follow these steps:

1. Ensure you have GitHub Actions enabled in your repository.
2. Create a new file under `.github/workflows` named `build-test-deploy.yml`.
3. Copy and paste the following YAML configuration into your new file:

```yaml
name: Build, Test and Deploy

on:
  - push 

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Install Cowsay Program
        run: sudo apt-get install cowsay -y

      - name: Execute Cowsay Command 
        run: cowsay -f dragon "Run for cover, I am a DRAGON......RAWR" >> dragon.txt

      - name: Upload Dragon File
        uses: actions/upload-artifact@v4
        with:
          name: dragon-text-file
          path: dragon.txt

  test: 
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Download Dragon File
        uses: actions/download-artifact@v4
        with:
          name: dragon-text-file

      - name: Test file exists 
        run: grep -i "dragon" dragon.txt

  deploy: 
    needs: test
    runs-on: ubuntu-latest
    steps: 
      - name: Download Dragon File
        uses: actions/download-artifact@v4
        with:
          name: dragon-text-file

      - name: Read File
        run: cat dragon.txt

      - name: Deploy 
        run: echo "Deploying......."
