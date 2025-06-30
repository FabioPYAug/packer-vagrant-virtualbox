 Vagrant.configure("2") do |config|
    config.vm.box = "debian12"

    config.vm.network "public_network", bridge: "enp2s0"

    config.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
        vb.cpus = 2
    end
end