image: itzg/minecraft-bedrock-server

most notes can be found on their github

Download your RTX pack of choice to pc

unzip .mcaddon file to pc



on docker host, create a docker-compose.yml:
```
version: "3.5"

services:

  minecraftBedrock:
    image: itzg/minecraft-bedrock-server
    container_name: minecraftBedrock
    restart: always
    volumes:
      - ./data:/data
    environment:
      EULA: "TRUE"
      GAMEMODE: survival
      DIFFICULTY: normal
    ports:
      - 19132:19132/udp

volumes:
  data: {}
```

start the container by CDing to the dir with the docker-compose and running `sudo docker-compose up -d`

edit `<containerDIR>/data/server.properties`:
* view-distance=64
* tick-distance=12
* pkayer-idle-timeout=0
* max-threads=0
* level-seed (if you want a specific seed)

create a dir in the resource_packs dir for your resource pack files (`mkdir ./data/resource_packs/kellyRTX`)

copy the unzipped mcaddon files to the dir you just created

edit `<containerDIR>/data/valid_known_packs.json`:
* add an entry for the pack you will use.
* Path is the dir you created
* UUID and version can be obtained by looking at the `manifest.json` file from the unzipped mcaddon
* example:
    ```
  {
		"file_system" : "RawPath",
		"path" : "resource_packs/kellyRTX",
		"uuid" : "e7fba840-ce5b-4811-be0c-372040ff66d8",
		"version" : "3.6.0"
	}
    ```

create `<containerDIR>/data/worlds/<yourworldname>/world_resource_packs.json`:
* this is the same uuid and version info as above, formatted differently
```
[
	{
		"pack_id": "e7fba840-ce5b-4811-be0c-372040ff66d8",
		"version": [3, 6, 0]
	}
]
```

run `sudo docker exec <minecraftContainerName> showcoordinates true`

restart container: `sudo docker-compose down; sudo docker-compose up -d`


