# Eclipse Che Custom Docker Setup

Eclipse Che uses dockerhub by default to pull docker images however you can use your own docker registry locally/on your own network. For this tutorial I will clone an already defined eclipse-che docker image and use that as a base for a new image which includes more c++ tools. The first few steps are copied (but modified) from the official page : https://docs.docker.com/registry/#basic-commands


Note :
This process can be done on your docker machine you intend to run eclipse-che from, in which case you can copy/paste the snippets. However if you intend to run your registry on your local network or elsewhere you will need to replace localhost with an IP or url.

To do this first setup docker on your machine then :

Start your registry

```
docker run -d -p 5000:5000 --name registry registry:2

```
Note - If you intend to run your own registry you may want to set the docker container to restart automatically everytime
the machine is booted. See below.

```
docker run -d --restart unless-stopped -p 5000:5000 --name registry registry:2
```

Pull (or build) some image from the original docker hub - debian in this instance so we can add cmake etc.

```
docker pull eclipse/debian_jdk8

```
Tag the image so that it points to your registry

```
docker image tag eclipse/debian_jdk8 localhost:5000/myfirstimage

```

Now push the tagged image back to your registry

```
docker push localhost:5000/myfirstimage

```

At this point the image would now be available for use via eclipse-che. 

To use it:
```
 1. Launch eclipse-che 
 2. Create a new workspace
 3. Click add stack
 4. Scroll to the bottom of the page and look for the raw-configuration section.
 5. Click show to reveal the raw configuration of your default build machine.
 6. Look for a line similar to: "content": "services:\n dev-machine:\n  image: eclipse/debian_jdk8\n"
 7. Replace the part with eclipse/image_type with your new docker register: image : localhost:5000/myfirstimage
```
Now that the stack has been created you must create or add this stack to your workspace to use it:

```
1. Click Workspaces on the main navigation panel, usually below dashboard.
2. Click add workspace
3. Click single machine for this particular example
4. Browse for the name of the stack you just created and then select it.
5. Click create & open in IDE.
```

The workspace should now launch and try to boot the image you created. However so far all this does is re-use an existing image given to use by eclipse-che and we need to expand on it to add packages we'd wish to use for development.
