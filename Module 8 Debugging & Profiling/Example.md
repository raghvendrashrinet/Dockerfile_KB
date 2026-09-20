## 🔎 Debugging Containers
1. Inspect Image Metadata
```bash
docker inspect myapp:latest
```
2. Check Build History
  `docker history myapp:latest`  
 Lists each layer created during the build.
3. Logs
   `docker logs <container_id>`
   Displays stdout/stderr from the container.

4. Exec into a Running Container
`docker exec -it <container_id> /bin/sh`

5. Resource Usage
 `docker stats`


   
