# If your Vagrant version is lower than 1.5, you can still use this provisioning
# by commenting or removing the line below and providing the config.vm.box_url parameter,
# if it's not already defined in this Vagrantfile. Keep in mind that you won't be able
# to use the Vagrant Cloud and other newer Vagrant features.
Vagrant.require_version ">= 1.5"

# Check to determine whether we're on a windows or linux/os-x host,
# later on we use this to launch ansible in the supported way
# source: https://stackoverflow.com/questions/2108727/which-in-ruby-checking-if-program-exists-in-path-from-ruby
def which(cmd)
    exts = ENV['PATHEXT'] ? ENV['PATHEXT'].split(';') : ['']
    ENV['PATH'].split(File::PATH_SEPARATOR).each do |path|
        exts.each { |ext|
            exe = File.join(path, "#{cmd}#{ext}")
            return exe if File.executable? exe
        }
    end
    return nil
end

Vagrant.configure("2") do |config|

    config.vm.provider :virtualbox do |v|
        v.name = "default"
        v.customize [
            "modifyvm", :id,
            "--name", "GIIN environment",
            "--memory", 2048,
            "--natdnshostresolver1", "on",
            "--cpus", 6,
        ]
    end

    config.vm.box = "ubuntu/trusty64"
    
    config.vm.network :private_network, ip: "192.168.33.99"
    config.ssh.forward_agent = true

    # If ansible is in your path it will provision from your HOST machine
    # If ansible is not found in the path it will be instaled in the VM and provisioned from there
    if which('ansible-playbook')
        config.vm.provision "ansible" do |ansible|
            ansible.playbook = "ansible/playbook.yml"
            ansible.inventory_path = "ansible/inventories/dev"
            ansible.limit = 'all'
        end
    else
        config.vm.provision :shell, path: "ansible/windows.sh", args: ["default"]
    end

    config.vm.synced_folder "./www", "/var/www/html", type: "nfs"
end
