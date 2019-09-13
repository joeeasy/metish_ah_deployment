# Metish_ah_deployment
Manually deploy simulations's frontend to multiple Virtual machines

#### Vagrant Multi-VM
To get started with, 
- create a directory call `vagrant_multi_vm`, you can name it anything of your choice. 
- `cd` into `vagrant_multi_vm` and run `vagrant init` this should create a vagrant config file for you
- create a `.env` file and a `.gitignore` file, and add `.env` to the `.gitignore` file to prevent you from pushing it to any repository
- Next up, run `vagrant plugin install vagrant-env` this will allow us load data from your `.env` file
  **Note:** Your `.env` file should contain `USERNAME` and `PASSWORD` variables, which by default should be `vagrant`, check the `sample.env` file
- Replace what's on your current `vagrant file` with 
  ``` 
  # Number of machines I want to create
  NODE_COUNTS = 2

  Vagrant.configure("2") do |config|

    # Box settings
    (1..NODE_COUNTS).each do |i|
      nodeName = (i == 1) ? "frontend_server" : "backend_server"
      PORT = (i == 1) ? 3000 : 5000
      config.env.enable # Enable vagrant-env(.env)
      
      config.vm.define nodeName do |subconfig|
        subconfig.ssh.username= ENV['USERNAME']
        subconfig.ssh.password= ENV['PASSWORD']
        subconfig.vm.box = BOX_OS
        subconfig.vm.provider "virtualbox" do |provider| 
          provider.name = nodeName
          provider.gui = false
          provider.memory = "1024"
          provider.cpus = 2
        end
        subconfig.vm.network "private_network", ip: "10.0.0.#{ i + 10}"
        subconfig.vm.network "forwarded_port", guest: 80, host: PORT
      end
    end

  end


Notice that I already know the number of boxes I wanted to create and instead of repeating the process of creating the boxes, I declared a `NODE_COUNTS` with the value of `2` since I'm creating two machines, then I got the range of numbers between `1` and `NODE_COUNTS` and iterated over it `(1..NODE_COUNTS).each do |i|`, this will create two machines call `frontend_server` and `backend_server`.

For the purpose of this tutorial, We'll focus on manually provisioning the `frontend_server`
##### Manually provisioning your machine

- Run `vagrant up`
- SSH into the `frontend_server` by runing `vagrant ssh frontend_server`
- `sudo apt-get update`
- Run `sudo apt-get install -y avahi-daemon libnss-mdns`
- `sudo apt-get install git -y` creates a network layer for your front-end and backend server to work together
- `curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -` this will Install the standard Debian/Ubuntu packages for Node and npm
- `sudo apt-get install -y nodejs` 
- `sudo npm install -y node-gyp -g` node-gyp is a tool which compiles Node.js Addons. Node.js Addons are native Node.js Modules, written in C or C++, which therefore need to be compiled on your machine
- `sudo chmod -R a+rwX /home/vagrant`  makes /home/vagrant writable 
- ```
  if [[ -d sims_project ]]; then 
      sudo rm -rf sims_project
    fi
  ```
  checks if there's already a folder call sims_project, before attempting to create via the next step

- `git clone https://github.com/andela/metis-ah-frontend.git sims_project`

- `cd sims_project`
- `sudo npm i `
- `npm run build`
- `npm start`

Goto http://10.0.0.11:3000