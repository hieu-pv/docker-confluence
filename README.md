# Confluence setup instruction

- [Confluence setup instruction](#confluence-setup-instruction)
  - [Without Swarm Mode](#without-swarm-mode)
    - [With docker-compose](#with-docker-compose)
      - [Step 1: Start docker container](#step-1-start-docker-container)
      - [Step 2: Get your Server ID](#step-2-get-your-server-id)
      - [Step 3: Get your decoder file](#step-3-get-your-decoder-file)
      - [Step 4: Rename decoder file](#step-4-rename-decoder-file)
      - [Step 5: Access container with root user and rename / remove decoder file](#step-5-access-container-with-root-user-and-rename--remove-decoder-file)
      - [Step 6: Path decoder file with keygen](#step-6-path-decoder-file-with-keygen)
      - [Step 7: Change decoder file name back to original](#step-7-change-decoder-file-name-back-to-original)
      - [Step 8: Move new decoder file back to container](#step-8-move-new-decoder-file-back-to-container)
      - [Step 9: Restart container and setup confluence](#step-9-restart-container-and-setup-confluence)
      - [Step 10: Continue installation instruction with generated key](#step-10-continue-installation-instruction-with-generated-key)
  - [Swarm Mode](#swarm-mode)
    - [Server](#server)
    - [Confluence](#confluence)
      - [Pre-install](#pre-install)
      - [Crack](#crack)
  - [Restore data from old server](#restore-data-from-old-server)
      - [Pre-install](#pre-install-1)
      - [Backup data](#backup-data)
      - [Transfer data to new server](#transfer-data-to-new-server)
      - [Setup server](#setup-server)
      - [Crack](#crack-1)

## Without Swarm Mode

### With docker-compose

If you are using docker-composer mode and setting up new server, follow instruction bellow

#### Step 1: Start docker container

```
docker-compose up -d
```

#### Step 2: Get your Server ID

Access to http://localhost:8080 then follow installation process, you can see your `Server ID` after few steps

#### Step 3: Get your decoder file

Copy decoder file from container, xxx is version of decoder file

```
docker cp confluence:/opt/atlassian/confluence/confluence/WEB-INF/lib/atlassian-extras-decoder-xxx.jar /local/folder/
```

#### Step 4: Rename decoder file

> We need this file to generate license key, now keygen work on windows system only so you have to download this file to local device to generate license

```
atlassian-extras-2.4.jar
```

#### Step 5: Access container with root user and rename / remove decoder file

```
docker exec -it -u 0 {CONTAINER_ID} bash
```

then

```
mv /opt/atlassian/confluence/confluence/WEB-INF/lib/atlassian-extras-decoder-xxx.jar /opt/atlassian/confluence/confluence/WEB-INF/lib/atlassian-extras-decoder-xxx.jar.bak
```

#### Step 6: Path decoder file with keygen

Run keygen (on local windows device) by following command

> Now keygen works on windows system only and you need java JDK to run keygen

> Use your `Server ID` from `step 1` and `atlassian-extras-2.4.jar` from `step 2`

> `confluence_keygen.jar` can be found inside `/crack/iNViSiBLE`

```
java -jar confluence_keygen.jar
```

#### Step 7: Change decoder file name back to original

Keygen will update your decoder file, you need to save new decoder file to its location inside container

#### Step 8: Move new decoder file back to container

```
docker cp /local/folder/atlassian-extras-decoder-xxx.jar confluence:/opt/atlassian/confluence/confluence/WEB-INF/lib/
```

#### Step 9: Restart container and setup confluence

```
docker-compose restart
```

#### Step 10: Continue installation instruction with generated key

## Swarm Mode

### Server

Setup new server with traefik https://github.com/hieu-pv/docker-traefik

> use branch `without_jenkins`

### Confluence

#### Pre-install

Create new stack with configuration from https://github.com/hieu-pv/docker-confluence and please use branch `traefik`

> This stack need some environment variables `DOMAIN`, `USERNAME` and `HASHED_PASSWORD`

Config your stack environments

`DOMAIN` for your server url example `confluence.yourserver.com`

`USERNAME` for adminer authentication

`HASHED_PASSWORD` for adminer authentication

Wait for few minutes you can access to

- confluence server at {DOMAIN}
- adminer at adminer.{DOMAIN}

#### Crack

following crack instruction above

## Restore data from old server

#### Pre-install

Setup new server with traefik https://github.com/hieu-pv/docker-traefik

#### Backup data

The first we need to backup all data from your **old server**

By running the following command you will create a tar file for your volumne

```
docker run --rm --volumes-from {your_old_confluence_container_name} -v $(pwd):/backup ubuntu tar cvf /backup/confluence.tar /var/atlassian/application-data/confluence
```

> It backup all data inside `/var/atlassian/application-data/confluence` directory to confluence.tar

You need to backup your database volume also

```
docker run --rm --volumes-from {your_old_database_container_name} -v $(pwd):/backup ubuntu tar cvf /backup/database.tar /var/lib/postgresql/data
```

#### Transfer data to new server

> SSH to your new server add do `scp` command to move your 2 backup file to new server

Create new volume called `confluence_confluence_data` then copy all data from `confluence.tar`

```
docker volume create confluence_confluence_data
```

Create new volume called `confluence_confluence_opt_lib` then copy all data from `database.tar`

```
docker volume create confluence_pgdata
```

Unarchive your backup file to correct volume

```
docker run -rm -it -v confluence_confluence_data:/var/atlassian/application-data/confluence -v $(pwd):/backup ubuntu bash
```

> It will create new container from ubuntu image then you can do copy yourself

#### Setup server

Create new stack called `confluence` same as Sawrm mode section

> It will bootstrap new confluence server , you probaly will see few errors

You need update build number https://confluence.atlassian.com/confkb/confluence-will-not-start-up-because-the-build-number-in-the-home-directory-doesn-t-match-the-build-number-in-the-database-after-upgrade-376834096.html?_ga=2.50287652.383639342.1564339994-1190080985.1532407189

Check your build number in database by running the SQL command

```
select * from CONFVERSION;
```

Correct your build number in `/var/atlassian/application-data/confluence/confluence.cfg.xml` with the build number from SQL command


#### Crack

Crack your confluence server with Server ID from `/var/atlassian/application-data/confluence/confluence.cfg.xml`.

Update file `/var/atlassian/application-data/confluence/confluence.cfg.xml` with the key generated (remove line before put to file)

You key will be look like this

```
<property name="atlassian.license.message">AAABKQ0ODAoPeJxtkFtPwjAUgN/7K5r4XLIVCULSxLkuYZNtKCDxsdYDNtm6pZdF/r2VwYvx8dy+8 51zV3YaF77BdIEjuryPl7MpztMdplG8QBysNKp3qtMs7fSx8aAloMq3H2Dq496CsYzEKDUgfpu4c MB+J0k0J3SBwowT0lWiBbZS4PsByYCZhJwagDnj4daTlUI1bFCy+wzQx1MbwonsWpQNovEXOjuKx sJIWCsJ2sLu3MOFntZlmb2mebJGAaQdaBFMs+9emfNoNZ3OSUwJnY2A2w1p460DU4W1lkVom1Xsv d7jMnnOcJnhBG8TjjdJxZMJqs1JaGVHmberKtqCGcDknD0VdUHoA+XkZVUfyGxzKNDVM1TXOb9F/ 2ttvJFfwsKfJ/4A7YmIFDAsAhR34mIcWHAb4MVy3kjd0CIvboP7lAIUEWcFX9slBGm8uZIyOXsga NooEOw=X02eu</property>
```