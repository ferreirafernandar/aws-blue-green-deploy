Login into your Jekins server via SSH, configure your aws credentials and run


```
eksctl create cluster \
						--name capstone-cluster-udacity \
						--version 1.17 \
						--nodegroup-name standard-workers \
						--node-type t2.small \
						--nodes 2 \
						--nodes-min 1 \
						--nodes-max 3 \
						--node-ami auto \
						--region us-east-1 \
						--zones us-east-1a \
						--zones us-east-1b \
						--zones us-east-1c \
```

After creation of cluster, run the follow command to create a config file
```
    aws eks --region us-east-1 update-kubeconfig --name capstone-cluster-udacity
```

Take note from the output and update the Jenkins file.

For more informations visit https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html

The `playblook.yaml` file is for install jenkins and necessary packages
The `Jenkinsfile` is for the pipeline
The `Dockerfile` is for Dockerimage
The `index.html` file is upload to a nginx server
All the `.json` files is used for blue/green deploy