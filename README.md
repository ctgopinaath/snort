STEPS to Install, Configure and Demonstrate Snort in ubuntu

SNORT_DEMO

 <img align="center" alt="aws-cdk" width="10000000px" src="https://github.com/ctgopinaath/snort/blob/main/snort_demo.jpg" />

1. Create 2 linux machine in same network  (ubuntu 22 or above version) and open the terminals of both machines in parallel (Side by side)
2. Perform the following operations in both machines (Refer Fig:ip.jpg)

        2.a. Identify the IP address of each machines using the following command and take a note of it

                    Command: ip a          [OR]  ifconfig          # take a note of the ethernet interface name. In this case it is ens5
4. [Example] From the Diagram **Fig: snort_demo.jpg** , Consider the machine with following IP address

  
 <img align="center" alt="aws-cdk" width="10000000px" src="https://github.com/ctgopinaath/snort/blob/main/ip.jpg" />
        
        10.0.30.98 = Application server (with snort)
        10.0.30.99 = Intruder's machine (person who is trying to attack the application server)
   
6. Perform the following commands in Application server [ 10.0.30.98 ]

        4.a.  Login as a root user
            command: sudo su -  

        4.b.  Verify the current user in linux
            command: whoami  

        4.c. Update the OS
            command: apt update                            

        4.c.  Install Docker Software to enable the application   
            commands:
                i) apt install apt-transport-https ca-certificates curl software-properties-common 
                ii) curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
                iii) echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null 
                iv) apt install docker-ce -y
                v) systemctl status docker

        4.d. Deploy the 2 different webservers using docker with following commands
            command: 
                i) docker run -dit --name webserver1 -p 80:80 httpd
                ii) docker run -dit --name webserver2 -p 8080:80 nginx  
                 
        4.e. Install the snort software in ubuntu
            command: 
                i) apt update
                ii) snap install snort [or] apt install snort

        4.f. Verify the detailed information and current status of your server using following command   
            command: snort -T -i ens5 -c /etc/snort/snort.conf  # Refer the step 2.a and add appropriate ethernet interface of your machine here

7. Perform the following commands in Intruder's machine [ 10.0.30.99 ]
   
        *** Login to Intruders machine ****
   
        5.a. Try to ping Application server from Intruder's machine
                command: ping 10.0.30.99
        5.b. Access the webervers deployed in application server from Intruders machine
                command: 
                   i) curl -v http://10.0.30.98:80
                   ii) curl -v http://10.0.30.98:8080


8. Configure Snort in Application server.

        6.a. Open the local.rules under /etc/snort/rules folder and add the following rules to configure the rules to Detect and prevent the intrusion 

                i) Command to open local.rules: vi /etc/snort/rules/local.rules
                ii) Rules to be updated: 
                        alert   tcp     any     any     -> $HOME_NET 80 (msg:"Some one is accessing your webserver"; sid:1000002; rev:2;)
                        reject   tcp     any     any     -> $HOME_NET 8080 (msg:"Some one is accessing your webserver"; sid:1000003; rev:3;)
                        alert  icmp    any     any     -> $HOME_NET any (msg:"ping is blocked here"; sid:1000004; rev:4;)

        6.b. Execute the following command to initate the snort application and monitor the events
                Command: snort -q -l /var/log/snort/ -i ens5 -A console -c /etc/snort/snort.conf    #Refer the step 2.a and add appropriate ethernet interface of your machine here

9. Start the intrusion activity from intrusion server

        7.a ping 10.0.30.99        
        7.b. curl -v http://10.0.30.98:80
        7.c. curl -v http://10.0.30.98:8080


11. Verify the logs and status of snort in Application server on basis of rules that has been defined in step 6.a.ii 






NOTE: Generate more rules in the follwoing generator

    Link : http://snorpy.cyb3rs3c.net/


References: 
    1. https://www.snort.org/documents
    2. https://docs.snort.org/
    3. https://docs.snort.org/start/rules
    4. https://docs.snort.org/start/help
    5. http://manual-snort-org.s3-website-us-east-1.amazonaws.com/
    6. http://manual-snort-org.s3-website-us-east-1.amazonaws.com/node29.html
    
