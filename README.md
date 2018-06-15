### Step 1: Start docker container

```
docker-compose up -d
```

### Step 2: Access to localhost:8080, follow installation process and copy your Server ID, stop on "License Activation Screen"

Copy decoder file from container, xxx is version of decoder file

```
docker cp confluence:/opt/atlassian/confluence/confluence/WEB-INF/lib/atlassian-extras-decoder-xxx.jar /local/folder/
```

### Step 3: Rename decoder file
> We need this file to generate license key, now keygen work on windows system only so you have to download this file to local device to generate license

```
atlassian-extras-2.4.jar
```

### Step 4: Access container with root user and rename / remove decoder file 

```
docker exec -it -u 0 {CONTAINER_ID} bash
```

then

```
mv /opt/atlassian/confluence/confluence/WEB-INF/lib/atlassian-extras-decoder-xxx.jar /opt/atlassian/confluence/confluence/WEB-INF/lib/atlassian-extras-decoder-xxx.jar.bak
```

### Step 5: Path decoder file with keygen 

Run keygen (on local windows device) by following command 

> Now keygen works on windows system only and you need java JDK to run keygen

> Use your `Server ID` from `step 1` and `atlassian-extras-2.4.jar` from `step 2`

> `confluence_keygen.jar` can be found inside `/crack/iNViSiBLE`

```
java -jar confluence_keygen.jar
```

### Step 6: Change decoder file name back to original

Keygen will update your decoder file, you need to save new decoder file to its location inside container

### Step 7: Move new decoder file back to container 

```
docker cp /local/folder/atlassian-extras-decoder-xxx.jar confluence:/opt/atlassian/confluence/confluence/WEB-INF/lib/
```

### Step 8: Restart container and setup confluence 
```
docker-compose restart
```

### Step 9: Continue installation instruction with generated key