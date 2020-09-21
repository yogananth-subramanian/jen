def branches = [:]
def img_list = [
    '8.2':'http://download.devel.redhat.com/rhel-8/rel-eng/RHEL-8/latest-RHEL-8.2.0/compose/BaseOS/x86_64/images/rhel-guest-image-8.2-290.x86_64.qcow2',
    '8.1':'http://download.devel.redhat.com/rhel-8/rel-eng/RHEL-8/latest-RHEL-8.1.0/compose/BaseOS/x86_64/images/rhel-guest-image-8.1-263.x86_64.qcow2',
    '8.0':'http://download.devel.redhat.com/rhel-8/rel-eng/RHEL-8/latest-RHEL-8.0.0/compose/BaseOS/x86_64/images/rhel-guest-image-8.0-1854.x86_64.qcow2',
    '7.9':'http://download.devel.redhat.com/rhel-7/rel-eng/RHEL-7/latest-RHEL-7.9/compose/Server/x86_64/images/rhel-guest-image-7.9-9.x86_64.qcow2',
    '7.8':'http://download.devel.redhat.com/rhel-7/rel-eng/RHEL-7/latest-RHEL-7.8/compose/Server/x86_64/images/rhel-guest-image-7.8-41.x86_64.qcow2',
    '7.7':'http://download.devel.redhat.com/rhel-7/rel-eng/RHEL-7/latest-RHEL-7.7/compose/Server/x86_64/images/rhel-guest-image-7.7-261.x86_64.qcow2',
    '7.6':'http://download.devel.redhat.com/rhel-7/rel-eng/RHEL-7/latest-RHEL-7.6/compose/Server/x86_64/images/rhel-guest-image-7.6-210.x86_64.qcow2',
    '7.5':'http://download.devel.redhat.com/rhel-7/rel-eng/RHEL-7/RHEL-7.5-RC-1.3/compose/Server/x86_64/images/rhel-guest-image-7.5-146.x86_64.qcow2',
    '7.4':'http://download.devel.redhat.com/rhel-7/rel-eng/RHEL-7/RHEL-7.4-RC-1.2/compose/Server/x86_64/images/rhel-guest-image-7.4-191.x86_64.qcow2',
    '7.3':'http://download.devel.redhat.com/pub/rhel/released/RHEL-7/7.3/Server/x86_64/images/rhel-guest-image-7.3-33.x86_64.qcow2',
]
def osp_rhel_list = [
    '10': '7.7',
    '13': '7.8',
    '14': '7.7',
    '15': '8.2',
    '16': '8.1',
    '16.1': '8.2',
    '16.1.1': '8.2'
]
def net_config = [
    'rhos-nfv-10.lab.eng.rdu2.redhat.com': ' --topology-nodes undercloud:1,controller:1 --topology-network 4_nets_3_bridges_hybrid -e override.networks.net1.nic=em4 -e override.networks.net2.nic=em1 -e override.networks.net3.nic=em2',
    'dell-r640-oss-13.lab.eng.brq.redhat.com': ' --topology-nodes undercloud:1,controller:1 --topology-network 4_nets_3_bridges_hybrid -e override.networks.net1.nic=em2 -e override.networks.net2.nic=em3 -e override.networks.net3.nic=em4'
]
def IR_VIRSH_IMAGE = img_list[osp_rhel_list[release]]
def OS_REL = osp_rhel_list[release]
def IR_NET_CONFIG = net_config[server]
def stagelst='baremetal,undercloud,overcloud,update-fix,update-undercloud,update-overcloud'
def excludelst=[]
for (i in exclude.split(',')){
  for (j in stagelst.split(',')){
  if ( j.matches(i) ){
    excludelst.add(j)
  
  }
 }
}
pipeline {
    agent any
    parameters {
        choice(name: 'server', choices: ['rhos-nfv-10.lab.eng.rdu2.redhat.com', 'dell-r640-oss-13.lab.eng.brq.redhat.com'], description: 'Pick something')
        choice(name: 'release', choices: ['16.1', '16', '13'], description: 'Pick something')
        string(name: 'build', defaultValue: 'GA', description: 'passed_phase2,passed_phase1,GA')
        string(name: 'beaker_user', defaultValue: 'ysubrama', description: 'Beaker username')
        choice(name: 'deploymnet', choices: ['virtual', 'hybrid'], description: 'Pick something')
        string(name: 'build_update', defaultValue: 'passed_phase2', description: 'passed_phase2,passed_phase1')
        string(name: 'exclude', defaultValue: '.*', description: 'Who should I say hello to?')
        text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')
    }    
    stages {
        stage('Install Baremetal') {
            when {
                expression { !('baremetal' in excludelst)  }
            }
            environment {
                SERVICE_CREDS = credentials("${beaker_user}")
            }
            steps {
                echo 'Building'
                echo 'Starting'
                  withCredentials([usernamePassword(credentialsId: "${beaker_user}", passwordVariable: 'beakerpass', usernameVariable: 'beakerusr')]) {
                      lock("${server}") {
                        sh '''
                        cd /root/infrared
                        source .venv/bin/activate
                        ir beaker --url='https://beaker.engineering.redhat.com' --beaker-user='ysubrama' --beaker-password="$beakerpass" --host-address="$server" --image='rhel-7.8'  --host-pubkey '/root/.ssh/id_rsa.pub' --host-privkey '/root//.ssh/id_rsa' --web-service 'rest' --host-password="$beakerpass"  --host-user='root'  --release='True'
                        ir beaker --url='https://beaker.engineering.redhat.com' --beaker-user='ysubrama' --beaker-password="$beakerpass" --host-address="$server" --image='rhel-7.8'  --host-pubkey '/root/.ssh/id_rsa.pub' --host-privkey '/root//.ssh/id_rsa' --web-service 'rest' --host-password="$beakerpass"  --host-user='root'
                        '''
                    }
                }
                echo 'Finish'
            }
        }
        stage('Configure Hypervisor') {
            when {
                expression { !('baremetal' in excludelst)  }
            }
            steps {
                echo 'Building'
                sh '''
                ssh  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@${server}  'yum install -y git python3 libselinux-python3 python-virtualenv libselinux-python'
                keygen='yes|ssh-keygen -t rsa -q -f "$HOME/.ssh/id_rsa" -N ""; cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys '
                ssh  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@${server} $keygen
                ssh  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@${server} 'git clone https://github.com/redhat-openstack/infrared.git;cd infrared/;virtualenv .venv;echo "export IR_HOME=`pwd`" >> .venv/bin/activate && source .venv/bin/activate; pip install -U pip;pip install .;infrared plugin add all'
                '''
                echo 'Finish'
            }
        }
        stage('Setup Topology') {
            when {
                expression { !('undercloud' in excludelst)  }
            }
            environment {
                IR_VIRSH_IMAGE = "${IR_VIRSH_IMAGE}"
                IR_NET_CONFIG = "${IR_NET_CONFIG}"
            }
            steps {
                echo 'Building'
                 sh '''
                 ir_cli="infrared virsh -vv    --host-address $server  --host-key ~/.ssh/id_rsa     --image-url ${IR_VIRSH_IMAGE}  --host-memory-overcommit False --disk-pool /home/ -e override.controller.cpu=4 -e override.undercloud.cpu=4 "
                 if [ $deployment = 'virtual' ]
                 then 
                  IR_SETUP="cd infrared/;source .venv/bin/activate;$ir_cli --topology-nodes 'undercloud:1,controller:1,compute:1'   "
                 else  
                  IR_SETUP="cd infrared/;source .venv/bin/activate;$ir_cli ${IR_NET_CONFIG} "
                 fi
                 echo ${IR_SETUP}
                 ssh  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@${server}  ${IR_SETUP}
                 '''
                echo 'Finish'
            }
        }
        stage('Setup Undercloud') {
            when {
                expression { !('undercloud' in excludelst)  }
            }
            steps {
                echo 'Testing'
                 sh '''
                 ir_cli="infrared tripleo-undercloud -vv --version ${release}  --build=${build} --images-task=rpm --images-update yes "
                 UN_SETUP="cd infrared/;source .venv/bin/activate;$ir_cli"
                 echo ${UN_SETUP}
                 ssh  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@${server}  ${UN_SETUP}
                 '''
            }
        }
        stage('Introspect Overcloud nodes') {
            when {
                expression { !('overcloud' in excludelst)  }
            }
            steps {
                echo 'Testing'
                 sh '''
                 ir_cli="infrared tripleo-overcloud -vv -o prepare_instack.yml     --version ${release}  --introspect=yes     --tagging=yes     --deploy=no "
                 if [ $deployment = 'virtual' ]
                 then 
                  OVER_SETUP="$ir_cli --deployment-files virt "
                 else  
                  OVER_SETUP="$ir_cli  --deployment-files ovn    -e provison_virsh_network_name=br-ctlplane     --hybrid hybrid_nodes.json --vbmc-host undercloud "
                 fi
                 DEP_SETUP="cd infrared/;source .venv/bin/activate;${OVER_SETUP} "
                 echo ${DEP_SETUP}
                 ssh  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@${server}  ${DEP_SETUP}
                 '''
            }
        }
        stage('Intall Overcloud') {
            when {
                expression { !('overcloud' in excludelst)  }
            }
            steps {
                echo 'Testing'
                 sh '''
                 ir_cli="infrared tripleo-overcloud -vv -o prepare_instack.yml     --version ${release}  --introspect=no     --tagging=no  --tht-roles yes   --deploy=yes "
                 if [ $deployment = 'virtual' ]
                 then 
                  OVER_SETUP="$ir_cli --deployment-files virt  --network-backend=geneve"
                 else  
                  OVER_SETUP="$ir_cli  --deployment-files ovn    -e provison_virsh_network_name=br-ctlplane     --hybrid hybrid_nodes.json --vbmc-host undercloud "
                 fi
                 OC_SETUP="cd infrared/;source .venv/bin/activate;${OVER_SETUP} "
                 echo ${OC_SETUP}
                 ssh  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@${server}  ${OC_SETUP}
                 '''
            }
        }
        stage('update fix') {
            when {
                expression { !('update-fix' in excludelst)  }
            }
            steps {
                echo 'Testing'
                 sh '''
                 ir_cli="infrared plugin remove tripleo-upgrade;infrared plugin add --revision stable/train tripleo-upgrade;mkdir -p /root/infrared/plugins/tripleo-upgrade/infrared_plugin/roles/tripleo-upgrade;find /root/infrared/plugins/tripleo-upgrade -maxdepth 1 -mindepth 1 -not -name infrared_plugin -exec mv '{}' /root/infrared/plugins/tripleo-upgrade/infrared_plugin/roles/tripleo-upgrade \\; "
                 UN_SETUP="cd infrared/;source .venv/bin/activate;$ir_cli"
                 echo ${UN_SETUP}
                 ssh  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@${server}  ${UN_SETUP}
                 '''
            }
        }
        stage('Update repo and containers') {
            when {
                expression { !('update-undercloud' in excludelst)  }
            }
            environment {
                OS_REL = "${OS_REL}"
            }
            steps {
                echo 'Testing'
                 sh '''
                 ir_cli="infrared tripleo-undercloud --update-undercloud yes --version ${release} --build ${build_update} --ansible-args='tags=upgrade_repos,undercloud_containers'  --osrelease ${OS_REL} "
                 UN_SETUP="cd infrared/;source .venv/bin/activate;$ir_cli"
                 echo ${UN_SETUP}
                 ssh  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@${server}  ${UN_SETUP}
                 '''
            }
        }
        stage('Update undercloud') {
            when {
                expression { !('update-undercloud' in excludelst)  }
            }
            steps {
                echo 'Testing'
                 sh '''
                 ir_cli="infrared tripleo-upgrade --undercloud-update yes --overcloud-stack overcloud "
                 UN_SETUP="cd infrared/;source .venv/bin/activate;$ir_cli"
                 echo ${UN_SETUP}
                 ssh  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@${server}  ${UN_SETUP}
                 '''
            }
        }
        stage('Update overcloud repo containers') {
            when {
                expression { !('update-overcloud' in excludelst)  }
            }
            environment {
                OS_REL = "${OS_REL}"
            }
            steps {
                echo 'Testing'
                 sh '''
                 ir_cli="infrared tripleo-overcloud --ocupdate True  --build ${build_update} --ansible-args='tags=update_collect_info,update_undercloud_validation,update_repos,update_prepare_containers;skip-tags=container-prepare-reboot' --osrelease ${OS_REL} "
                 if [ $deployment = 'virtual' ]
                 then 
                  OVER_SETUP="$ir_cli --deployment-files virt "
                 else  
                  OVER_SETUP="$ir_cli  --deployment-files ovn "
                 fi
                 OC_SETUP="cd infrared/;source .venv/bin/activate;${OVER_SETUP} "
                 echo ${OC_SETUP}
                 ssh  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@${server}  ${OC_SETUP}
                 '''
            }
        }
        stage('Update overcloud') {
            when {
                expression { !('update-overcloud' in excludelst)  }
            }
            steps {
                echo 'Testing'
                 sh '''
                 ir_cli="infrared tripleo-upgrade  --upgrade-floatingip-check no  --upgrade-workload yes --overcloud-update yes --overcloud-stack overcloud  "
                 if [ $deployment = 'virtual' ]
                 then 
                  OVER_SETUP="$ir_cli --deployment-files virt "
                 else  
                  OVER_SETUP="$ir_cli  --deployment-files ovn "
                 fi
                 OC_SETUP="cd infrared/;source .venv/bin/activate;${OVER_SETUP} "
                 echo ${OC_SETUP}
                 ssh  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@${server}  ${OC_SETUP}
                 '''
            }
        }
        stage('Post update reboot and check') {
            when {
                expression { !('update-overcloud' in excludelst)  }
            }
            steps {
                echo 'Testing'
                 sh '''
                 ir_cli="echo -e 'cleanup_services: []' > cleanup_services.yml;infrared tripleo-overcloud --postreboot True --postreboot_evacuate yes --overcloud-stack overcloud --network-ovn no -e @cleanup_services.yml "
                 if [ $deployment = 'virtual' ]
                 then 
                  OVER_SETUP="$ir_cli --deployment-files virt "
                 else  
                  OVER_SETUP="$ir_cli  --deployment-files ovn "
                 fi
                 OC_SETUP="cd infrared/;source .venv/bin/activate;${OVER_SETUP} "
                 echo ${OC_SETUP}
                 ssh  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@${server}  ${OC_SETUP}
                 '''
            }
        }
        
    }
}
