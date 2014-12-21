# Introduction

Compared with previous auto image building job, this one improved some interface.  

1. This demo supports all 6 bdc noded image building. Pass value with `-e "node_name=mdfup"`. Supported: mdfcp, mdfup, mdfdb, adfrr, adffr, adfdb  
2. All variables are moved from group_vars to vars. They are divided by function. But be attention, ansible will not load these vars in default. Use __ vars_files__ or __include_vars__ before using a variable.  
3. Any variable defined as __var: value__ can be passed a new value with -e. Don't recommend to pass value to variable defined as __var:subvar:value__. If you intended to, write all values as a file in jason format and pass them with @.  
4. The KVM's IP is not allowed to be passed as a value now. Here it uses the DHCP to help CI reduce management cost. The IP will be printed out after the KVM is setted up.  
If you want to debug image building in your own environment, configure a virtual network with DHCP enabled. Then, pass the virtual network name as `-e "vnet_name=$Your-Vnet"`  
5. If you want to trigger multiple jobs in one host machine, please use `-e "kvm_id=$SOME_STR"` to let libvirt distinguishes different KVMs.  
6. The arm token here it usese is from "epxpxpx" and it will expired soon. You'd better update your own arm token. If have any questions, please contact ETA team.  
7. All the 3pp tar.gz will be downloaded into host machine, not the KVM. Then they are uncompressed into KVM. See the down-3pp-pkgs.yml and untar-3pp-pkgs.yml. The 3pp package list are defined in vars/kvm-env-conf/pkg-conf.yml.
All the lastet CI rpms will directly downloaded into KVM. Then moved inside KVM. See the down-CI-pkgs.yml.  
8. You can skip certain jobs with `-skip-tag="$some_tasks"`. Like `-skip-tag="downpkgs"` will skip downloading lsv 3pp packages. These tags are defined in each play.  
9. The job add a environment check. If you fail to pass it, make some updates.

# Examples

1. `ansible-playbook bdc-auto-image-kvm.yml -e "node_name=mdfdb"`  
This command will trigger a job of creating a mdfdb image.  
2. `ansible-playbook bdc-auto-image-kvm.yml -e "node_name=mdfdb kvm_path=/var/my_data/bdc-kvms"`  
This command will additionally store the KVM disk file under /var/my_data/bdc-kvms/mdfdb-01.  
3. `ansible-playbook bdc-auto-image-kvm.yml -e "node_name=mdfdb kvm_path=/var/my_data/bdc-kvms kvm_id=Demo"`  
This command will additionally named the KVM as mdfdb-Demo and store the disk file under /var/my_data/bdc-kvms/mdfdb-Demo
4. `ansible-playbook bdc-auto-image-kvm.yml -e "node_name=mdfdb dest_image_path=/var/tmp/bdc-imgs kvm_id=Demo"`  
This command will additionally create a image named mdfdb-Demo-full-img.qcow2 under /var/tmp/bdc-imgs.  
5. `ansible-playbook bdc-auto-image-kvm.yml -e "node_name=mdfdb dest_image_path=/var/tmp/bdc-imgs kvm_id=Demo compress=true"`
This command will additionally compress the image to obviously decrease image size.  
6. `ansible-playbook bdc-auto-image-kvm.yml -e "node_name=mdfdb vnet_name=vnet1 origin_image=/var/tmp/rhel-cloud-img.qcow2"`  
This command will additionally use vnet1(DHCP enabled) as the network KVM uses and use /var/tmp/rhel-cloud-img.qcow2 as the base image to create a new KVM.  
7. `ansible-playbook bdc-auto-image-kvm.yml -e "node_name=mdfdb yum_repo_url=http://sample.com/rhel6.5/ yum_proxy=http://proxy.com:8080"`
This command will additionally configure base yum repository using "http://sample.com/rhel6.5/" via proxy of "http://proxy.com:8080".  
8. `ansible-playbook bdc-auto-image-kvm.yml -e "node_name=mdfdb rpm_version=15b git_branch=master"`  
This command will additionally download mdfdb-15b rpm packages of master branch into KVM from arm repository.
9. `ansible-playbook bdc-auto-image-kvm.yml -e "node_name=mdfdb host_pkg_path=/var/tmp/pkgs vm_pkg_path=/var/tmp"`
This command will additionally download __3pp__ tar.gz under /var/tmp/pkgs in host machine, and uncompressed to /var/tmp into KVM. The CI rpms will also be downloaded under /var/tmp.
