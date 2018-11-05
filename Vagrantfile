# -*- mode: ruby -*-
# vi: set ft=ruby :

$centos_version = "1809.01"
$additions_url = "https://download.virtualbox.org/virtualbox/5.2.20/VBoxGuestAdditions_5.2.20.iso"
$additions_sha256 = "d3c4a0f79a1ff0c35190c530612d83b81a2035eb9f056237888d8b056c07005f"

Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.box_version = $centos_version
  config.vm.box_check_update = false
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.ssh.insert_key = false

  config.vm.provider "virtualbox" do |v|
    v.memory = 512
    v.cpus = 1
  end

  config.vm.provision "shell", inline: <<-SHELL
    # Download
    curl -o /tmp/vboxadd.iso #{$additions_url}
    echo #{$additions_sha256} /tmp/vboxadd.iso > /tmp/vboxadd.iso.sha256sum
    sha256sum -c /tmp/vboxadd.iso.sha256sum

    # Install
    yum -y install kernel-headers-$(uname -r) kernel-devel-$(uname -r) gcc
    mount /tmp/vboxadd.iso /mnt
    /mnt/VBoxLinuxAdditions.run
    umount /mnt

    # Cleanup
    rm -f /tmp/vboxadd*
    rm -f /var/log/vboxadd*
    rm -f /etc/ssh/*key*
    yum -y history rollback 1
    yum clean all
    rm -rf /var/cache/yum
    dd if=/dev/zero of=/boot/ZERO bs=1M
    rm /boot/ZERO
    dd if=/dev/zero of=/ZERO bs=1M
    rm /ZERO
    swapoff /dev/mapper/VolGroup00-LogVol01
    dd if=/dev/zero of=/dev/mapper/VolGroup00-LogVol01 bs=1M
    mkswap /dev/mapper/VolGroup00-LogVol01
    sync
    echo "All done! Machine will now shutdown ready for a 'vagrant package'..."
    history -c && poweroff
  SHELL
end
