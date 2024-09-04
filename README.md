# Github-Actions-Deployments

This repository uses GitHub Actions to automate various CI/CD workflows. These actions streamline the development process by automating deploying of code. Below is a detailed description of the workflows currently implemented, including the triggers, steps, and purposes of each.

### Deployment Workflow

#### Purpose:
Automates the deployment of the website to a development or production environment.

#### Triggers:
- push event on branch: `main` (production deployment)
- push event on branch: `development` (development deployment)

#### Steps:

##### 1. Checkout Code:
- Uses the `actions/checkout` action to pull the latest code from the repository..

##### 2. Set Up Environment:
- Configures the deployment environment, such as setting up server credentials.

##### 3. Deploy Code:
- Deploys the files to the specified environment 

## SFTP WP Engine Deployment Action

### Environment Variables

##### `WPE_SSHG_KEY_PRIVATE`
- <b style="color:red;">( Required )</b> - Generated SSH Private Key. 

##### `WPE_ENV`
- <b style="color:red;">( Required )</b> - Name of site in WP Engine ( Production, Staging or Development ). 

##### `REMOTE_PATH`
- <b style="color:red;">( Required )</b> - File path to update in the server i.e. (` '/wp-content/plugins/{ PLUGIN_NAME }' `).

For other environment variables and secrets check <a href="https://wpengine.com/support/github-action-deploy/" target="_blank">here</a>.

### Example Usage

```
name: Push Updates to Server
on:
  push:
    branches:
    - main
jobs:
  develop-deploy:
    if: github.ref_name == 'main'
    name: Push to Main
    runs-on: ubuntu-latest
    steps:
    - name: 🚚 Get latest code
      uses: actions/checkout@v3

    - name: GitHub Action Deploy to WP Engine
      uses: wpengine/github-action-wpe-site-deploy@v3
      with:
        WPE_SSHG_KEY_PRIVATE: ${{ secrets.WPE_SSHG_KEY_PRIVATE }} 
        WPE_ENV: ${{ secrets.WPE_ENV }}
        REMOTE_PATH: ${{ secrets.REMOTE_PATH }}
```

## FTP Deployment Action

### Environment Variables

##### `server-dir`
- <b style="color:red;">( Required )</b> - File path to update in the server i.e. (` '/wp-content/plugins/{ PLUGIN_NAME }' `). 

##### `server`
- <b style="color:red;">( Required )</b> - FTP Server Address. 

##### `username`
- <b style="color:red;">( Required )</b> - FTP Server Username.

##### `password`
- <b style="color:red;">( Required )</b> - FTP Server Password.

##### `port`
- <b style="color:gray;">( Optional )</b> - FTP Server Port.

### Example Usage
2 Deployment Process based on the branch you pushed.

```
name: Publish Updates to Website Cpanel
on:
  push:
    branches:
    - develop
    - master
jobs:
  develop-deploy:
    if: github.ref_name == 'develop'
    name: Push to Develop
    runs-on: ubuntu-latest
    steps:
    - name: 🚚 Get latest code
      uses: actions/checkout@v2

    - name: Sync files on Develop
      uses: SamKirkland/FTP-Deploy-Action@4.3.3
      with:
        server-dir: ${{ secrets.FTP_DEV_SERVER_DIR }}
        server: ${{ secrets.FTP_DEV_SERVER }}
        username: ${{ secrets.FTP_DEV_USERNAME }}
        password: ${{ secrets.FTP_DEV_PASSWORD }}
        port: ${{ secrets.FTP_DEV_PORT }}

  live-deploy:
    if: github.ref_name == 'master'
    name: Push to Master
    runs-on: ubuntu-latest
    steps:
    - name: 🚚 Get latest code
      uses: actions/checkout@v2

    - name: Sync files on Live
      uses: SamKirkland/FTP-Deploy-Action@4.3.3
      with:
        server-dir: ${{ secrets.FTP_LIVE_SERVER_DIR }}
        server: ${{ secrets.FTP_LIVE_SERVER }}
        username: ${{ secrets.FTP_LIVE_USERNAME }}
        password: ${{ secrets.FTP_LIVE_PASSWORD }}
        port: ${{ secrets.FTP_LIVE_PORT }}
```

## Note

All environment variables should use secret variables in Github. Using secret variables in GitHub Actions is a critical security measure that helps protect sensitive data, ensures consistent and secure workflow execution, and adheres to industry best practices. By leveraging secret variables, you can automate your CI/CD processes with confidence, knowing that your confidential information is securely managed.