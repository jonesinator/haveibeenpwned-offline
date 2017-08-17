Vagrant.configure("2") do |config|
  config.vm.box = "bento/debian-9.0"
  config.vm.provision "shell", inline: "apt-get install -y p7zip"
  config.vm.provision "shell", path: "install-is-pwned"
end
