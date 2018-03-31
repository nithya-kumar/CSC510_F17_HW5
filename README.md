# HW5
## CSC 510 (Software Engineering) F17 HW5  
## Configuration Management and Continuous Deployment

The HW contains the following parts.

### Ansible playbook

**Creation of ansible playbook**

Create an ansible playbook that is able to:

* **Setup:** Install the following items:
    * Install node.js
    * Install forever
    * Pull/clone git repo into a destination: https://github.com/CSC-DevOps/App
    * Install npm packages
    
* **Tasks:**
    * Run app: `forever start node main.js`
    * Security: Create a task that ensures `bash`, `openssl`, `openssh-client`, and `openssh-server` are running latest version.
    * Cleanup: Remove content in `/tmp/*`

The ansible playbook to perform the above tasks can be seen in [setup_tasks.yml](https://github.ncsu.edu/nkumar8/HW5/blob/master/setup_tasks.yml)

**Steps to create Config VM and node VM**

* Create two VMs - one for the config machine and another for node machine using Vagrant. Follow the steps to setup and get the machines up and running 

      vagrant init ubuntu/trusty64
      vagrant up
      
* SSH into the machines using. This ensures the machines are up and running
      
      vagrant ssh

* Make the machines communicate by configuring private networks on both. Uncomment the following line in Vagrantfile of both the boxes and specify private ip addresses. In my case, I have setup the private ip address for config machine as "192.168.33.100" and node0 as "192.168.33.150"

      config.vm.network "private_network", ip: "192.168.33.100"

  Then run on both the boxes,
      
      vagrant reload  

* Setup inventory file and ssh keys

  * Create an inventory file that contains something like the following on the config machine

        [nodes]
        192.168.33.150 ansible_ssh_user=vagrant ansible_ssh_private_key_file=./keys/node0.key  
      
   * Create a /keys/node0.key file and input the contents of private_key of node0. Private key of node0 can be found in the path seen when you run,
        
          vagrant ssh-config  
          
      on node0 box  
      
  * Change the permission of the file using,
  
        chmod 600 keys/node0.key
        
  * Test the connection between config VM and node1 VM using,
  
        ssh -i keys/node0.key vagrant@192.168.33.150
        
**Steps to install ansible on Config VM**

* On the config VM, install ansible using the following commands,
  
      sudo apt-add-repository ppa:ansible/ansible
      sudo apt-get update
      sudo apt-get install ansible

* Run the ping test using Ansible to check communication between the config VM and node0 VM  
    
      ansible all -m ping -i inventory -vvvv
      
**Steps to run playbook**

* On the config VM, create a yml file named setup_tasks.yml and the copy the contents of the [playbook file](https://github.ncsu.edu/nkumar8/HW5/blob/master/setup_tasks.yml) for the tasks mentioned in the beginning of the HW  

* Run the yml file using the command,
      
      ansible-playbook setup_tasks.yml -i inventory  
 
* You wil see the given tasks being performed on the node0 VM. The yml file should run without any error

* After it is run, check on the node1 VM,
    * If the mentioned packages are installed
    * If the app is running using the `forever list` command
    * If /tmp/ directory is empty


### Concepts

#### Why should developers use configuration management tools to manage their software programs? What can go wrong?  

Configuration Management makes sure that current design and build state of the system is known, good & trusted; and is not dependent on the tacit knowledge of the development team. It is also called as IT automation. Being able to track the changes and progress of a code/application state is very useful for project management and development activities such as debugging.  

**Benefits of configuration management tools**

   * Improved efficiency, control and stability as CM tools improve visibility and tracking

   * Enhanced system reliability as it is easier for detection and correction of improper configurations

   * Ability to define and ensure code policies and procedures

   * Improved agility and faster problem resolution, thus giving better productivity

   * Efficient change management

   * Faster failure recovery i.e., restoration of service

**What can go wrong?**

If configuration management is not used, it would incur more cost in building things in the event of failure. The following can be the difficulties when we don't use CM,
   * Identifying which system component needs change when requirements change
   * Re-doing an implementation because the change did not have backward compatibility
   * Productivity loss because a component is replaced defective piece of code
   * Replacing wrong component due to lack of knowledge of which component needs to be put  
   
   [Reference](https://www.upguard.com/blog/5-configuration-management-boss)  

#### Explain the difference between continuous integration, continuous delivery, and continuous deployment, in your own words.

   * **Continuous Integration**
      * It is a practice where developers automatically build, test, and analyze a software change in response to every software change committed to the source repository
      * It is development practice that requires developers to commit and integrate code into a shared repository. Each check-in is then verified by an automated build, allowing teams to detect problems early
      * The key motivation to continous integration is that software developers often work in isolation, and then they need to integrate their changes with the rest of the team’s code base  
      [Reference 1](https://www.thoughtworks.com/continuous-integration)  
      [Reference 2](https://www.visualstudio.com/learn/what-is-continuous-integration/)
   
   * **Continuous Delivery**
      * It is a practice that makes sure that a change in software can be delivered and ready for use by a customer by testing in production-like environments
      *  Continuous Delivery makes it feasible to continuously adapt or iterate software in line with user feedback, changes in the technology and shifts in business strategy
      * Continous Delivery has these key benefits, a) Low risk releases b) Faster time to market c) Higher quality of product d) Lower costs e) Stree-free work teams  
      [Reference 1](https://www.thoughtworks.com/continuous-delivery)  
      [Reference 2](https://continuousdelivery.com/)  
      
   * **Continous Deployment**
      * It is a practice where incremental/itertive changes in software are automatically tested, vetted, and deployed to production environments
      * It is the deployment or release of code to production as soon as it’s ready
      * The key aspect of continuous deployment process is that it requires continuous integration and continuous delivery to gaurantee error-free releases  
      [Reference](https://blog.assembla.com/assemblablog/tabid/12618/bid/92411/continuous-delivery-vs-continuous-deployment-vs-continuous-integration-wait-huh.aspx)  

## Screencast

The screencast demo of performing setup, tasks, running app can be seen [here](https://goo.gl/yKA9D9)

