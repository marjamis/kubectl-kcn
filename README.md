# kubectl-kcn
A kubectl plugin which makes it simple to ssh into nodes within your cluster.

### Requirements
* bash
* ssh
* kubectl
* [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)
* This [script](./kubectl-n)

This script heavily relies on your [ssh_config](https://linux.die.net/man/5/ssh_config) to determine how the connections are made to the nodes which allows you to easily connect to the nodes based on your unique networking configuration.

This is made simpler by flags which will allow you to select what value is passed into the ssh command, i.e. hostname/nodename, private-ip (as registered to k8s) or public-ip (obtained using AWS API calls).

### How to use
Install the plugin by having the [script](./kubectl-n) in the $PATH of your shell. Once in the $PATH run:
```bash
kubectl plugin list
```
to ensure kubectl can find the plugin and it will use it. Once properly in place the plugin can be used by running:
```bash
kubectl n [options] [node]
```
For a complete list of options run:
```bash
kubectl n -h
```

More information about kubectl plugins in general can be found here: https://kubernetes.io/docs/tasks/extend-kubectl/kubectl-plugins/

### Samples
Sample ~/.ssh/config file:
```bash
# Defaults
Host *
  ServerAliveInterval 120
  ServerAliveCountMax 30
  Compression yes
  LogLevel INFO
  VisualHostKey yes

Host bastion
  HostName bastion.example.com
  User ec2-user
  IdentityFile ~/.ssh/bastion_private_key.pem

Host *.us-west-2.compute.internal
  IdentityFile ~/.ssh/node_private_key.pem
  User ec2-user
  ProxyCommand ssh bastion -W %h:%p
```
