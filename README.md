# experiment-gitlab-ci-docker-image

A test to see how Gitlab CI reacts to docker images with an 
entry point.

## Problem

Gitlab CI starts overrides the entry point of a Docker image with 
the following script:

```
sh -c if [ -x /usr/local/bin/bash ]; then
	exec /usr/local/bin/bash 
elif [ -x /usr/bin/bash ]; then
	exec /usr/bin/bash 
elif [ -x /bin/bash ]; then
	exec /bin/bash 
elif [ -x /usr/local/bin/sh ]; then
	exec /usr/local/bin/sh 
elif [ -x /usr/bin/sh ]; then
	exec /usr/bin/sh 
elif [ -x /bin/sh ]; then
	exec /bin/sh 
else
	echo shell not found
	exit 1
fi
```

This makes it impossible to run a container with a set entry 
point such as `ENTRYPOINT ["composer"]`.

## Solution

The solution I found is based on `https://github.com/composer/docker/blob/master/docker-entrypoint.sh`.

The trick is to detect the incoming command and based on that 
command differentiate the processing of it.

## Bonus

Use tini (https://github.com/krallin/tini) to initialize containers.
Tini spawns a single child and waits for it to exit all the while 
reaping zombies and performing signal forwarding.

