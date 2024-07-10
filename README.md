# blacklist-proxy

create a file /etc/firewall.user

touch /etc/firewall.user

for IP in $(wget -O - \
 "https://raw.githubusercontent.com/junotdutra/blacklist-croxyproxy/6e25b83a7d188dee84d849d3ec681e62419efff3/ip.txt")
do echo ${IP} >> /var/ipset-croxyproxy
done

sort /var/ipset-croxyproxy |uniq > /var/temp-ip
mv /var/temp-ip /var/ipset-croxyproxy
service firewall restart


create ipset in /etc/config/firewall

config ipset 'croxyproxy'     
        option name 'croxyproxy'
        option family 'ipv4'  
        option loadfile '/var/ipset-croxyproxy'
        list match 'dest_net'  

create rule

config rule                          
        option src 'lan'           
        option dest 'wan'                       
        option dest_port '443'         
        option target 'DROP'                   
        option family 'ipv4'       
        option ipset 'croxyproxy'       

Crontab -e

0 * * * * sh /etc/firewall.user
