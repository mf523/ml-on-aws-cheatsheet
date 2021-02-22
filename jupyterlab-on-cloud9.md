# Run JupyterLab on Cloud9

## Create an Cloud9 environment 
* Configure Security Group:  
  * open custom TCP and port 9999 
  * open HTTPS, HTTP to anywhere
  * open SSH to anywhere
* Attach an Elastic IP to the instance

ssh into EC2 from Mac Terminal and run 
```bash
sudo apt remove node
sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y && sudo apt install libevent-dev -y;
sudo snap install node --channel=13/stable --classic;
pip3 install jupyterlab
jupyter labextension install @jupyter-widgets/jupyterlab-manager jupyter-matplotlib jupyterlab-datawidgets jupyter-vue jupyter-threejs @jupyterlab/toc @krassowski/jupyterlab_go_to_definition jupyterlab-plotly plotlywidget jupyterlab-chart-editor;
jupyter nbextension enable --py widgetsnbextension;
pip3 install --upgrade jupyterlab-git && jupyter lab build;
```

## Config Jupyter
Concurrently, you can install conda env and jupyter to EC2 
```bash
echo "generate jupyter password to ~/.jupyter/jupyter_server_config.json"
jupyter server --generate-config;
jupyter server password;
#
echo "generate ssl credentials to ~/.ssh"
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout sslkey.key -out sslcert.pem;
mv sslkey.key ~/.ssh/sslkey.key && mv sslcert.pem ~/.ssh/sslcert.pem;
```
Add the following to `~/.jupyter/jupyter_server_config.py`
```
# use https
c.ServerApp.certfile = u'/home/ubuntu/.ssh/sslcert.pem'
c.ServerApp.keyfile = u'/home/ubuntu/.ssh/sslkey.key'
# Set ip to '*' to bind on all interfaces (ips) for the public server
c.ServerApp.ip = '*'
c.ServerApp.password = u'argon2:...<your hashed password here>' #find it in ~/.jupyter/jupyter_server_config.json
c.ServerApp.open_browser = False
c.ServerApp.port = 9999
```

## Launch Jupyter from Cloud9
Set Cloud9 Project Preferrence `Stop my environemnt`  to  `never`
Now launch jupyter from Cloud 9's terminal

## Setup tunneling from local 
Ubuntu / MacOS: 
```bash
ssh -i private_key_to_ec2.pem -NfL localhost:9999:localhost:9999 ubuntu@ec2_host
```
Windows: Using MobaXterm

## Enjoy!
https://localhost:9999

## CodeCommit Tip
```
git config --global credential.useHttpPath true;
git config --local credential.helper 'store';
git pull;
```
