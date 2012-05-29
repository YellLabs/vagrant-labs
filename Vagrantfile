# Available options:
#
#   :box        Set the base box.
#   :ip         Set the static IP of an additional interface.
#   :network    Set the additional interface type - :hostonly, :bridged
#   :puppet     Set to false to disable Puppet provisioning.
#   :memory     Override a basebox's default RAM allocation - numeric in MB.
#                   Note: the default value for an existing box will not be
#                   restored until the box is destroyed and recreated.

domain  = "yelllabs"
nodes   = {
    # Load balancing.
    :lb0 => { :ip => "172.16.200.11" },
    :lb1 => { :ip => "172.16.200.12" },

    :wc0 => { :ip => "172.16.200.20" },
    :wc1 => { :ip => "172.16.200.21" },


    # Frontends.
    :papp0 => { :ip => "172.16.200.50" },
    :papp1 => { :ip => "172.16.200.51" },

    :gapp0 => { :ip => "172.16.200.60" },
    :gapp1 => { :ip => "172.16.200.61" },

    :japp0 => { :ip => "172.16.200.70" },
    :japp1 => { :ip => "172.16.200.71" },

    :plapp0 => { :ip => "172.16.200.80" },
    :plapp1 => { :ip => "172.16.200.81" },


    # Datastores.
    :db0 => { :ip => "172.16.200.100" },
    :db1 => { :ip => "172.16.200.101" },

    :mq0 => { :ip => "172.16.200.110" },
    :mq1 => { :ip => "172.16.200.111" },

    :es0 => { :ip => "172.16.200.120" },
    :es1 => { :ip => "172.16.200.121" },

    :mg0 => { :ip => "172.16.200.130" },


    # Misc.
    :mon0 => {},
    :rpmbuild => {},
}

Vagrant::Config.run do |config|
    nodes.each do |node_name, node_opts|
        config.vm.define node_name do |c|
            c.vm.box = node_opts[:box] || "centos58-puppet26"

            # Host only NICs on EL5 take a long time.
            c.ssh.max_tries = 100
            c.ssh.forward_agent = true

            fqdn = "#{node_name}.#{domain}"
            c.vm.host_name = fqdn

            modifyvm_args = ['modifyvm', :id]
            modifyvm_args << '--name' << fqdn

            # workaround for https://github.com/mitchellh/vagrant/issues/516
            modifyvm_args << '--nictype1' << 'Am79C973'

            unless node_opts[:memory].nil?
                modifyvm_args << '--memory' << node_opts[:memory].to_s
            end

            c.vm.customize(modifyvm_args)

            if node_opts[:network] == :bridged
                c.vm.network(:bridged)
            elsif node_opts[:ip]
                c.vm.network(:hostonly, node_opts[:ip])
            end

            unless node_opts[:puppet] == false
                c.vm.share_folder("extdata", "/tmp/vagrant-puppet/extdata", "puppet/extdata")
                c.vm.provision :puppet do |puppet|
                    puppet.manifest_file = "site.pp"
                    puppet.manifests_path = "puppet/manifests"
                    puppet.module_path = "puppet/modules"
                end
            end
        end
    end
end
