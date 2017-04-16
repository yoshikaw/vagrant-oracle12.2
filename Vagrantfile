VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = 'http://yum.oracle.com/boxes/oraclelinux/ol73/ol73.box'
#  config.vm.box = 'http://yum.oracle.com/boxes/oraclelinux/ol69/ol69.box'

  config.vm.network 'forwarded_port', guest: 1521, host: 1521

  # detect local ansible command
  if RUBY_PLATFORM !~ /cygwin|mswin|mingw|bccwin|wince|emx/ and system('type ansible >/dev/null 2>&1')
    provisioner = 'ansible'
  else
    provisioner = 'ansible_local'
  end

  # install ansible
  config.vm.provision 'shell', inline: <<SCRIPT if provisioner == 'ansible_local'
type ansible >/dev/null 2>&1 && exit 0
os_version=$(. /etc/os-release; echo "${VERSION%%.*}")

yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-${os_version}.noarch.rpm
yum -y install ansible

sudo -u vagrant bash -c 'cat > $HOME/.ansible.cfg' <<EOF
[defaults]
allow_world_readable_tmpfiles = True
EOF
SCRIPT

  # provision oracle database
  config.vm.provision provisioner do |ansible|
    ansible.playbook = 'setup.yml'
    ansible.extra_vars = {
      script_dir: '/vagrant'
    }
  end
end
