删除未命名的镜像：docker rmi $(docker images -f "dangling=true" -q)  

docker run --privileged -p 8899:8080 -v C:\Users\wll\Documents\docker_volumes\tomcat\webapps:/usr/local/tomcat/webapps  -v C:\Users\wll\Documents\docker_volumes\tomcat\conf:/usr/local/tomcat/conf --name tomcat  -it tomcat

mount -t cifs -o uid=0,gid=0,uid=0,gid=0,rw,file_mode=0777,dir_mode=0777,username=guest,password= //192.168.1.47/Users/wll/Documents/docker_volumes/tomcat/conf /usr/local/tomcat/conf

apt-get install vim

apt-get update

apt-get install vim

