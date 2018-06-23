# kubectl-kcn
A script and kubectl plugin which makes it simply to ssh into nodes within your cluster.

### Requirements
* bash
* ssh
* kubectl
* [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)
* This [script](./kcn)

This script heavily relies on your [ssh_config](https://linux.die.net/man/5/ssh_config) to determine how the connections are made to the nodes which allows you to easily connect to the nodes based on your unique networking configuration.

This is made simpler by available flags which will allow you to select what value is passed into the ssh command, i.e. hostname/nodename, private-ip (as registered to k8s) or public-ip (obtained using AWS API calls).

### How to use
#### Plugin mode
If the plugin file is installed:
```bash
kubectl plugin kcn [options] <node>
```
More information on how to use the plugin can be found with:
```bash
kubectl plugin kcn --help
```

More information about kubectl plugins in general can be found here: https://kubernetes.io/docs/tasks/extend-kubectl/kubectl-plugins/

#### Direct mode
If just running the script directly:
```bash
kcn [options] <node>
```
More information on how to use the script can be found with:
```bash
kcn [--help | -h ]
```

Example of how to use the primary flags:
```bash
kcn <node_name> or kcn -a <node_name> # Passes the nodename to ssh.
kcn -i <node_name> # Passes the private ip supplied from k8s to ssh.
kcn -p <node_name> # Makes an AWS API call to get the public ip of the hostname mataching the nodename. This will also cache this value to reduce API calls.
```

### Samples
Sample ~/.ssh/config file:
```
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
  ProxyCommand ssh bastion nc %h %p 2> /dev/null
```
