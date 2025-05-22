3-1 Document your inventory and base commands

all:
 vars:
   ansible_user: admin
   ansible_ssh_private_key_file: ~/.ssh/id_rsa
 children:
   prod:
     hosts: eduardoantonio.polancoarrindell.takima.cloud

3-2 Document your playbook
In this step i created two .yml, first i created setup.yml, where i called the role (install docker) in charge of installing docker.
then, then, in the main.yml file of the task i added the instructions to install docker.

Content of setup.yml
- name: Deploy my app
  hosts: all
  become: true

  roles:
    - install_docker #Calling the role

Content of main.yml
---
# tasks file for roles/install_docker
- name: Install docker using apt
  apt:
    name: docker.io
    state: present
    update_cache: yes

- name: Start and enable docker
  service:
    name: docker
    enabled: true
    state: started

3-3 Document your docker_container tasks configuration.

Role to create container database:
---
# tasks file for roles/launch_database
- name: Remove old database container
  docker_container:
    name: database
    state: absent #removing previous containers with the same name in the server

- name: Run database container
  docker_container:
    name: Database
    image: eduardo9703/database:latest #extracting the image from my dockerhub
    pull: true #Defining that the image must be pulled even though we have an image with the same tag in our server
    env: #assigning environmental variables
      POSTGRES_USER: usr
      POSTGRES_PASSWORD: pwd
      POSTGRES_DB: database
    
    networks:
      - name: my_network #adding to a common network

    ports:
      - "4000:5432" #assigning ports

Role to create container backend:

- name: Remove old backend container
  docker_container:
    name: backend
    state: absent #removing previous containers with the same name in the server

- name: Run backend app container
  docker_container:
    name: backend
    pull: true #Defining that the image must be pulled even though we have an image with the same tag in our server
    image: eduardo9703/backend:latest
    env: #adding the credentials of the database
      SPRING_DATASOURCE_URL: "jdbc:postgresql://database:5432/database"
      SPRING_DATASOURCE_USERNAME: "usr"
      SPRING_DATASOURCE_PASSWORD: "pwd"
    networks:
      - name: my_network #adding to a common network
    ports:
      - "8080:8080"

Role to create container proxy:
---
# tasks file for roles/launch_proxy
- name: Remove old proxy container
  docker_container:
    name: proxy
    state: absent #removing previous containers with the same name in the server

- name: Run HTTPD launch_proxy
  docker_container:
    name: proxy
    image: eduardo9703/proxy:latest
    pull: true #Defining that the image must be pulled even though we have an image with the same tag in our server
    ports:
      - "80:80"
    networks:
      - name: my_network #Adding to common network

3-4 Is it really safe to deploy automatically every new image on the hub ? explain. What can I do to make it more secure?
It is not safe because it does not guarantee that the image is well constructed, it just construct the containers
and run then, but if a container that is not well configured is installed in out production server it
could break the whole app.

To make it safetier i would use GitHub images to test the code before deplying to the server. This way
you can ensure the code have the level of quality needed for being launched to production.
