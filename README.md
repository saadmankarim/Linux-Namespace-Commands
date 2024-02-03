# Linux-Namespace-Commands
<h2>##Adding two Namespaces and conneting then with one veth.</h2>
#Adding/Creating Nameservers\
sudo ip netns add ns1\
sudo ip netns add ns2\
ip netns show

#Creating virtual ethernet cable\
sudo ip link add ns1-veth0 type veth peer name ns2-veth0\
ip link list

#Adding the cable to the namespaces interface\
sudo ip link set ns1-veth0 netns ns1\
sudo ip link set ns2-veth0 netns ns2

#Setting IP address to Namespace 1\
sudo ip netns exec ns1 bash

ip addr add 10.10.10.1/24 dev ns1-veth0\
ip addr show

ip link set lo up\
ip link set ns1-veth0 up

#Setting IP address to Namespace 2\
sudo ip netns exec ns2 bash

ip addr add 10.10.10.2/24 dev ns2-veth0\
ip addr show

ip link set lo up\
ip link set ns2-veth0 up

#Checking the connectivity\
sudo ip netns exec ns1 ping 10.10.10.3\
sudo ip netns exec ns2 ping 10.10.10.2

<h2>##Adding two Namespaces and conneting then with using bridge.</h2>
#Adding/Creating Nameservers\
sudo ip netns add ns-a\
sudo ip netns add ns-b\
ip netns show

#Creating Bridge\
sudo ip link add br0 type bridge\
sudo ip link set br0 up

#Setting IP to Bridge\
sudo ip addr add 192.168.0.1/16 dev br0

#Creating virtual ethernet cable\
sudo ip link add vnsa-eth0 type veth peer name br1\
sudo ip link add vnsb-eth0 type veth peer name br2

#Adding the cable to the namespaces interface\
sudo ip link set vnsa-eth0 netns ns-a\
sudo ip link set vnsb-eth0 netns ns-b

#Adding the cable to the Bridge interface\
sudo ip link set br1 master br0\
sudo ip link set br2 master br0

ip link list\
sudo ip link set br1 up\
sudo ip link set br2 up

#Setting IP address to Namespace 1\
sudo ip netns exec ns-a bash

ip addr add 10.10.0.2/16 dev vnsa-eth0\
ip addr show

ip link set lo up\
ip link set vnsa-eth0

#Setting IP address to Namespace 2\
sudo ip netns exec ns-b bash

ip addr add 10.10.0.3/16 dev vnsa-eth0\
ip addr show

ip link set lo up\
ip link set vnsb-eth0

#Checking the connectivity\
sudo ip netns exec ns-a ping 10.10.0.3\
sudo ip netns exec ns-b ping 10.10.0.2
