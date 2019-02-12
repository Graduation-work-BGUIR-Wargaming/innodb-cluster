
INTENT_TYPE="internal-net"

MACHINES = {
  :node1 => {
             :box_name => "centos/7",
                 :net => [
                           {ip: '10.10.10.11', adapter: 3, netmask: "255.255.255.0", virtualbox__intnet: INTENT_TYPE},
                           ]
            },
  :node2 => {
             :box_name => "centos/7",
                 :net => [
                          {ip: '10.10.10.12', adapter: 3, netmask: "255.255.255.0", virtualbox__intnet: INTENT_TYPE},
                          ]
            },
  :node3 => {
             :box_name => "centos/7",
                 :net => [
                          {ip: '10.10.10.13', adapter: 3, netmask: "255.255.255.0", virtualbox__intnet: INTENT_TYPE},
                          ]
            },
  :"mysql-shell" => {
             :box_name => "centos/7",
                  :net => [
                          {ip: '10.10.10.14', adapter: 3, netmask: "255.255.255.0", virtualbox__intnet: INTENT_TYPE},
                          {ip: '10.11.12.14', adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "router-net"},
                          ],
                  :forwarded_port => [
                                     {guest: 3306, host: 3306}
                                     ]
            },
  :"mysql-router" => {
             :box_name => "centos/7",
                  :net => [
                          {ip: '10.10.10.10', adapter: 3, netmask: "255.255.255.0", virtualbox__intnet: INTENT_TYPE},
                          {ip: '10.11.12.10', adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "router-net"},
                          ],
                  :forwarded_port => [
                                     {guest: 3306, host: 3307}
                                     ]
            },
}

hosts_file="127.0.0.1\tlocalhost\n"

MACHINES.each do |hostname,config|  
  config[:net].each do |ip|
    if ip[:virtualbox__intnet]==INTENT_TYPE
      hosts_file=hosts_file+ip[:ip]+"\t"+hostname.to_s+"\n"
    end
  end
end

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    config.vm.define boxname do |box|
        box.vm.box = boxconfig[:box_name]
        box.vm.host_name = boxname.to_s
        boxconfig[:net].each do |ipconf|
          box.vm.network "private_network", ipconf
        end
        if boxconfig.key?(:forwarded_port)
          boxconfig[:forwarded_port].each do |port|
            box.vm.network "forwarded_port", port
          end
        end
        box.vm.provider "virtualbox" do |v|
          v.memory = 256
        end
        box.vm.provision "shell" do |shell|
          shell.inline = 'echo -e "$1" > /etc/hosts'
          shell.args = [hosts_file]
        end
        box.vm.provision "ansible" do |ansible|
          ansible.verbose = "v"
          ansible.playbook = "ansible/playbook.yml"
          ansible.tags = "all"
          ansible.extra_vars = {
            "mysql_root_password" => "_SecretPass1",
            "mysql_admin_username" => "admin",
            "mysql_admin_password" => "_AdminPassword2",
            "monitoring_user" => "monitoring",
            "monitoring_user_password" => "_m0nUserPass",
            "replication_user" => "replication",
            "replication_user_password" => "_Repl1caPass"
          }
          if boxname.to_s =~ /node\d/
            var_host = {"var_host" => "node1"}
            ansible.extra_vars[:var_host]=boxname.to_s
          end
        end
    end
  end
end

############## mysql-router 
#   Classic MySQL protocol connections to cluster 'clTest':
#   - Read/Write Connections: localhost:6446
#   - Read/Only Connections: localhost:6447
#   X protocol connections to cluster 'clTest':
#   - Read/Write Connections: localhost:64460
#   - Read/Only Connections: localhost:64470

Bootstrapping system MySQL Router instance...
Checking for old Router accounts
Creating account mysql_router1_qgzg2ryyvlcc@'%'
MySQL Router  has now been configured for the InnoDB cluster 'clTest'.

