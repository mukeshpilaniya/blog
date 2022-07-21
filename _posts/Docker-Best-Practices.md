## Best Practices for Docker Compose
1. Only reference a Dockerfile once in docker-compose.yml
    - https://www.viget.com/articles/local-docker-best-practices/
2. Coordinate services with wait-for-it
3. Start entrypoint scripts with set -e and end with exec "$@"
4. Controlling starting order
    - https://runnable.com/docker/advanced-docker-compose-configuration
5. Use Environment File(.env) to pass environments variables
6. Avoiding 2 Compose Files for Dev and Prod with an Override File
7. Never exposer external port of container if it's not required
8. Set default variable value in docker-compose file
9. Use YML Aliases and Anchors to reduce code duplication
    - https://nickjanetakis.com/blog/docker-tip-82-using-yaml-anchors-and-x-properties-in-docker-compose
10. Define HealthCheck in Docker compose file
11. Always define restart policy in docker compose file


## Best Practice for Dockerfile
1. Multi-stage builds to optimize image size
    - https://nickjanetakis.com/blog/best-practices-around-production-ready-web-apps-with-docker-compose
2. Running your container as a non-root user
3. Always creare an entrypoint shell in a project
4. Prefer to use Array (Exec) over String (Shell) CMD syntax
5. RUN vs CMD vs ENTRYPOINT 
6. Always Make Your ENTRYPOINT Scripts Executable

## Things to Avoid 
1. Startup oders of services
2. Passing variables form shell script or .env files
3. Extending services (volumes, links, network etc..)
4. Mounting environment variable using config
