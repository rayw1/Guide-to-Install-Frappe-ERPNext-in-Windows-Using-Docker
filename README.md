# Install Frappe ERPNext in Windows 11 Using Docker

In Windows 11, how to install Frappe Bench and install Frappe/ERPNext Application - Aug 2024

### 0) Pre-reqs 

      Docker Desktop
      - Docker Desktop 4.33.1 (161083)
      
      Git
      - git version 2.24.0.windows.2
      
      Windows 11
      - 21H2
      
      VS Code 
      - 1.93.0
    
### 1) Check Docker version
    docker version
    git version

### 2) Clone frappe_docker and move to frappe_docker folder

    git clone https://github.com/frappe/frappe_docker.git
    
### 3) Copy container config folders

   Copy example devcontainer config from 
    
    cd frappe_docker
    devcontainer-example folder to .devcontainer folder
    
   Copy example vscode folder config for devcontainer from 
    
    development/vscode-example folder to development/.vscode folder
   
### 4) Install VSCode Remote Containers extension
    
    Install vscode
    Install 'Dev Containers' extension
    
### 5) After the extensions are installed, you can

  Open frappe_docker folder in VS Code.

  	cd frappe_docker
   	code .
  
  Launch the command, from Command Palette (Ctrl + Shift + P) Dev Containers: Reopen in Container. You can also click in the bottom left corner to access the remote container menu.
  
### Note: 
   if this error in running contaners try the below commnad in CMD
   
   Error starting userland proxy: listen tcp 0.0.0.0:80: bind: An attempt was made to access a socket in a way forbidden by its access permissions
	
    netsh http add iplisten ipaddress=::
                
   The development directory is ignored by git. It is mounted and available inside the container. Create all your benches (installations of bench, the tool that manages frappe) inside this directory.
   Node v14 and v10 are installed. Check with nvm ls. Node v14 is used by default.
                
    
### 6) Initilase frappe bench with frappe version 14 and Switch directory
    
    cd frappe_docker
    bench init --skip-redis-config-generation --frappe-branch version-15 frappe-bench
    
    
### 7) Setup hosts
    
   We need to tell bench to use the right containers instead of localhost. Run the following commands inside the container:

    cd frappe-bench
    bench set-mariadb-host mariadb
    bench set-redis-cache-host redis-cache:6379
    bench set-redis-queue-host redis-queue:6379
    bench set-redis-socketio-host redis-socketio:6379
  For any reason the above commands fail, set the values in common_site_config.json manually.

    {
      "db_host": "mariadb",
      "redis_cache": "redis://redis-cache:6379",
      "redis_queue": "redis://redis-queue:6379",
      "redis_socketio": "redis://redis-socketio:6379"
    }
    
### 8) Create a new site
   sitename MUST end with .localhost for trying deployments locally.

    MySQL/MariaDB root password: 123
    cd frappe-bench
    bench new-site acspy.localhost --no-mariadb-socket 
    
### 9) Set bench developer mode on the new site
    
    bench --site acspy.localhost set-config developer_mode 1
    bench --site acspy.localhost clear-cache   
    
    
### 10) Install ERPNext

    bench get-app --branch version-15 --resolve-deps erpnext
    bench --site acspy.localhost install-app erpnext
    
### 11) Start Frappe bench 
   Ensure you add 127.0.0.1 newsite.localhost entry to c:\windows\system32\drivers\etc\hosts file
    
    bench start
    
You can now login with user Administrator and the password you choose when creating the site. Your website will now be accessible at location acspy.localhost:8000
