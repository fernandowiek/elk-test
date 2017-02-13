$install_docker_script = <<SCRIPT
echo Installing Docker...
sysctl -w vm.max_map_count=262144
curl -sSL https://get.docker.com/ | sh
usermod -aG docker ubuntu
SCRIPT

$manager_script = <<SCRIPT
echo Swarm Init...
docker swarm init --listen-addr 10.100.199.200:2377 --advertise-addr 10.100.199.200:2377
docker swarm join-token --quiet worker > /vagrant/worker_token
docker service create \
  --name=viz \
  --publish=8080:8080/tcp \
  --limit-memory=128m \
  --constraint=node.role==manager \
  --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
  manomarks/visualizer
SCRIPT

$worker_script = <<SCRIPT
echo Swarm Join...
docker swarm join --token $(cat /vagrant/worker_token) 10.100.199.200:2377
SCRIPT

$es_script = <<SCRIPT
echo ElasticSearch Init...
cd /vagrant && docker stack deploy -c docker-compose.yml elk_cluster
SCRIPT

Vagrant.configure('2') do |config|

  vm_box = 'ubuntu/xenial64'

  config.vm.define :manager, primary: true  do |manager|
    manager.vm.box = vm_box
    manager.vm.box_check_update = true
    manager.vm.network :private_network, ip: "10.100.199.200"
    manager.vm.network :forwarded_port, guest: 8080, host: 8080
    manager.vm.network :forwarded_port, guest: 5601, host: 5601
    manager.vm.network :forwarded_port, guest: 9200, host: 9200
    manager.vm.hostname = "manager"
    manager.vm.synced_folder ".", "/vagrant"
    config.vm.provision "file", source: "~/.gitconfig", destination: ".gitconfig"
    manager.vm.provision "shell", inline: $install_docker_script, privileged: true
    manager.vm.provision "shell", inline: $manager_script, privileged: true
    manager.vm.provision "shell", inline: $es_script, privileged: true
    manager.vm.provider "virtualbox" do |vb|
      vb.name = "manager"
      vb.memory = "3072"
    end
  end

  (1..2).each do |i|
    config.vm.define "worker0#{i}" do |worker|
      worker.vm.box = vm_box
      worker.vm.box_check_update = true
      worker.vm.network :private_network, ip: "10.100.199.20#{i}"
      worker.vm.hostname = "worker0#{i}"
      worker.vm.synced_folder ".", "/vagrant"
      worker.vm.provision "shell", inline: $install_docker_script, privileged: true
      worker.vm.provision "shell", inline: $worker_script, privileged: true
      worker.vm.provider "virtualbox" do |vb|
        vb.name = "worker0#{i}"
        vb.memory = "1024"
      end
    end
  end
end
