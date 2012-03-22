def create_vm(config, name, network=false, puppet=true)
    domain  = "yelllabs"
    fqdn    = "#{name}.#{domain}"

    config.vm.define name do |c|
        c.vm.box            = "centos58-puppet26"
        # Host only NICs on EL5 take a long time.
        c.ssh.max_tries     = 100
        c.ssh.forward_agent = true

        c.vm.host_name = fqdn
        c.vm.customize(["modifyvm", :id,
            "--name", fqdn,
            # Workaround for https://github.com/mitchellh/vagrant/issues/516
            "--nictype1", "Am79C973"
        ])

        if network
            c.vm.network(:hostonly, network)
        end

        if puppet
            c.vm.share_folder("extdata", "/tmp/vagrant-puppet/extdata", "puppet/extdata")
            c.vm.provision :puppet do |puppet|
                puppet.manifest_file = "site.pp"
                puppet.manifests_path = "puppet/manifests"
                puppet.module_path = "puppet/modules"
            end
        end
    end
end

Vagrant::Config.run do |config|
    # Load balancing.
    create_vm(config, :lb0, "172.16.200.11")
    create_vm(config, :lb1, "172.16.200.12")

    create_vm(config, :wc0, "172.16.200.20")
    create_vm(config, :wc1, "172.16.200.21")


    # Frontends.
    create_vm(config, :papp0, "172.16.200.50")
    create_vm(config, :papp1, "172.16.200.51")

    create_vm(config, :gapp0, "172.16.200.60")
    create_vm(config, :gapp1, "172.16.200.61")

    create_vm(config, :japp0, "172.16.200.70")
    create_vm(config, :japp1, "172.16.200.71")

    create_vm(config, :plapp0, "172.16.200.80")
    create_vm(config, :plapp1, "172.16.200.81")


    # Datastores.
    create_vm(config, :db0, "172.16.200.100")
    create_vm(config, :db1, "172.16.200.101")

    create_vm(config, :mq0, "172.16.200.110")
    create_vm(config, :mq1, "172.16.200.111")

    create_vm(config, :es0, "172.16.200.120")
    create_vm(config, :es1, "172.16.200.121")

    create_vm(config, :mg0, "172.16.200.130")


    # Misc.
    create_vm(config, :mon0)
    create_vm(config, :rpmbuild)
end
