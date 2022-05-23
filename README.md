# ssh-inlet-tunnel

Creates a inlet tunnel from an endpoint (i.e. cloud VM) to a containers VPC.
This chart turns a public remote machine into an endpoint for your private cloud
by forwarding endpoint ports to the internal VPC (typically a load balancer).

## Requirements
```Shell
# define your endpoint
export ENDPOINT=my.endpoint.example.org

# generate a ssh id key and add it to remote server's `~/.ssh/authorized_keys`
ssh-keygen -t rsa -b 4096 -f ~/.ssh/inlet/id_rsa -C inlet@k8s -N ""
ssh-copy-id -i ~/.ssh/inlet/id_rsa root@$ENDPOINT:22

# configure the remote server's `sshd_config` to accept gateway ports
ssh root@$ENDPOINT:22 'cat << EOF >> /etc/ssh/sshd_config
# allow ssh remote port forwarding for inlet-tunnel
GatewayPorts yes
EOF
systemctl reload sshd.service'
```

## Installation

```Shell
helm repo add ssh-inlet-tunnel https://honigpferd.github.io/ssh-inlet-tunnel
helm repo update

helm upgrade --install ssh-inlet-tunnel ssh-inlet-tunnel/ssh-inlet-tunnel \
    --namespace ssh-inlet-tunnel --create-namespace \
    --set tunnel.user=root \
    --set tunnel.endpoint=$ENDPOINT \
    --set tunnel.sshport=22 \
    --set tunnel.privateKey=`cat ~/.ssh/inlet/id_rsa | base64 -w0 -` \
    --set tunnel.publicKey=`cat ~/.ssh/inlet/id_rsa.pub | base64 -w0 -` \
    --set tunnel.args[0]='-R80:192.168.0.240:80' \
    --set tunnel.args[1]='-R443:192.168.0.240:443'
```
more configuration options at [values.yaml](charts/ssh-inlet-tunnel/values.yaml)
