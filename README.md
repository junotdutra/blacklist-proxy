# blacklist-proxy

create a file /etc/firewall.user

touch /etc/firewall.user

for IP in $(wget -O - \
 "https://raw.githubusercontent.com/junotdutra/blacklist-proxy/main/ip.txt")
do echo ${IP} >> /var/ipset-proxy
done

sort /var/ipset-proxy |uniq > /var/temp-ip
mv /var/temp-ip /var/ipset-proxy
service firewall restart


create ipset in /etc/config/firewall

config ipset 'proxy'     
        option name 'proxy'
        option family 'ipv4'  
        option loadfile '/var/ipset-proxy'
        list match 'dest_net'  

create rule

config rule                          
        option src 'lan'           
        option dest 'wan'                       
        option dest_port '443'         
        option target 'DROP'                   
        option family 'ipv4'       
        option ipset 'proxy'       

Crontab -e

0 * * * * sh /etc/firewall.user
