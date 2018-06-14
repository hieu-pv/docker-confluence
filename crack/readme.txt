docker-compose up

access to localhost:8080, copy your Server ID

then 
...

1. copy decoder file from container, xxx is version of decoder file

docker cp confluence:/opt/atlassian/confluence/confluence/WEB-INF/lib/atlassian-extras-decoder-xxx.jar /local/folder/

2. rename file to 

atlassian-extras-2.4.jar

3. access container with root user and rename / remove decoder file 

docker exec -it -u 0 {CONTAINER_ID} bash


then 

mv /opt/atlassian/confluence/confluence/WEB-INF/lib/atlassian-extras-decoder-xxx.jar /opt/atlassian/confluence/confluence/WEB-INF/lib/atlassian-extras-decoder-xxx.jar.bak

4. path decoder file with keygen 
run keygen by following command inside \crack\iNViSiBLE

java -jar confluence_keygen.jar

5. change decoder file name back to original

6. move it back to container 

docker cp /local/folder/atlassian-extras-decoder-xxx.jar confluence:/opt/atlassian/confluence/confluence/WEB-INF/lib/

7. restart container and setup confluence 