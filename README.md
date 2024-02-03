# Linux-Namespace-Commands
##Adding two Namespaces and conneting then with one veth.
#Adding/Creating Nameservers
sudo ip netns add ns1
sudo ip netns add ns2
ip netns show

#Creating virtual ethernet cable
sudo ip link add ns1-veth0 type veth peer name ns2-veth0
ip link list

#Adding the cable to the namespaces interface
sudo ip link set ns1-veth0 netns ns1
sudo ip link set ns2-veth0 netns ns2

#Setting IP address to Namespace 1
sudo ip netns exec ns1 bash

ip addr add 10.10.10.1/24 dev ns1-veth0
ip addr show

ip link set lo up
ip link set ns1-veth0 up

#Setting IP address to Namespace 2
sudo ip netns exec ns2 bash

ip addr add 10.10.10.2/24 dev ns2-veth0
ip addr show

ip link set lo up
ip link set ns2-veth0 up
