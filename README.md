# Simple DevOps Project.
## Integration with Git, Maven, Jenkins, Ansible, and Docker

![image](https://user-images.githubusercontent.com/57230194/128148530-91494aa1-e1fd-4d93-a020-05398103aec5.png)


#### Pre-Requisites:
1. Docker installed on ansible server 
2. login to "docker hub" on ansible server

1. Create *Jenkins job*,
   - *Source Code Management:*
      - Repository : ``
      - Branches to build : `*/main`  
   - *Build:*
     - Root POM:`pom.xml`
     - Goals and options : `clean install package`
   - *Post Steps*
     - *Send files or execute commands over SSH*
       - Name: `Ansible server`
       - Source files	: `webapp/target/*.war`
       - Remove prefix	: `webapp/target`
       - Remote directory	: `//opt//docker`

     - *Send files or execute commands over SSH*
       - Name: `Ansible serverr`
       - Source files	: `Dockerfile`
       - Remote directory	: `//opt//docker`
       - Exec Command: 
            - `cd /opt/docker`
            - `sudo docker build -t $JOB_NAME .`
            - `sudo docker tag $JOB_NAME sourabhmiraje/$JOB_NAME:$BUILD_ID`
            - `sudo docker tag $JOB_NAME sourabhmiraje/$JOB_NAME:latest`
            - `sudo docker push sourabhmiraje/$JOB_NAME:$BUILD_ID`
            - `sudo docker push sourabhmiraje/$JOB_NAME:latest`
            - `sudo docker rmi -f sourabhmiraje/$JOB_NAME:latest`
            - `sudo docker rmi -f sourabhmiraje/$JOB_NAME:$BUILD_ID`
            - `sudo docker rmi -f $JOB_NAME`


### Part-02 : Deploy Containers

1. Write a yaml file to create a container (file name : playbook.yaml)
   ```yaml
     ---
     - hosts: docker
       tasks:
          - name: Stop a container
            community.docker.docker_container:
              name: myapp
              state: stopped
            ignore_errors: yes

          - name: Remove container from all networks
            community.docker.docker_container:
              name: myapp
              purge_networks: yes
            ignore_errors: yes

          - name: Remove image
            community.docker.docker_image:
              state: absent
              force_absent: yes
              name: sourabhmiraje/project_4
              tag: latest
            ignore_errors: yes

          - name: Start a container with a command
            community.docker.docker_container:
              name: myapp
              image: sourabhmiraje/project_4
              ports: 8090:8080

1. Run playbook on Ansible server. 
     - *Under post build actions*
        - Send files or execute commands over SSH
          - Exec Command: 
          ```sh
             cd /opt/playbooks
             ansible-playbook create_docker_container.yml
            ```
