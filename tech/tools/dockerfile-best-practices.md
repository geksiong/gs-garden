---
title: Dockerfile Best Practices
date: 2022-04-21

tags:
  - docker

---

- Run as non-root user
- If running on pure Docker or Docker Swarm, include the HEALTHCHECK command. This is not applicable to Kubernetes, which should its Liveness and Readiness Probes.
- Use multi-stage builds
- Use container scanning tools to identify potential vulnerabilities

## Improve Build Caching

- Things that seldom change go first.
- Be specific which files you want to copy
  - Don't use folders or wildcards, they tend to invalidate the cache easily.
- Don't put everything into a single line
  - Docker caches for the entire line. It may improve your caching if you can break up the lines into logical groups (following the first rule above)

## Reduce Image Size

- Don't install unnecessary stuff
  - (Debian) Use `--no-install-recommends` with `apt-get` to not automatically install recommended packages
  - You probably don't need things like ssh and vim
- Remove the package manager's cache files after installing them
  - (Debian) `rm -rf /var/lib/apt/lists/*`

## Easier Maintenance

- Use the official images
  - You don't really want to maintain a stack of container images you loving crafted from scratch.
- Go for the slim images if available
  - Alpine
  - Debian Slim
- Pin your image versions
  - `latest` means you don't get reproducibility with your builds.

## Java-specific stuff

- Try to use Java 11 or above if you can


## Sources

- https://medium.com/@marcong_54227/how-to-write-a-production-ready-dockerfile-58d18d4daddc
- https://eldermoraes.com/java-containers-what-i-wish-i-knew-before-i-used-it/
