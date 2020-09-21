def branches = [:]
def img_list = [
    'rhel-8.2':'http://download.devel.redhat.com/rhel-8/rel-eng/RHEL-8/latest-RHEL-8.2.0/compose/BaseOS/x86_64/images/rhel-guest-image-8.2-290.x86_64.qcow2',
    'rhel-8.1':'http://download.devel.redhat.com/rhel-8/rel-eng/RHEL-8/latest-RHEL-8.1.0/compose/BaseOS/x86_64/images/rhel-guest-image-8.1-263.x86_64.qcow2',
    'rhel-8.0':'http://download.devel.redhat.com/rhel-8/rel-eng/RHEL-8/latest-RHEL-8.0.0/compose/BaseOS/x86_64/images/rhel-guest-image-8.0-1854.x86_64.qcow2',
    'rhel-7.9':'http://download.devel.redhat.com/rhel-7/rel-eng/RHEL-7/latest-RHEL-7.9/compose/Server/x86_64/images/rhel-guest-image-7.9-9.x86_64.qcow2',
    'rhel-7.8':'http://download.devel.redhat.com/rhel-7/rel-eng/RHEL-7/latest-RHEL-7.8/compose/Server/x86_64/images/rhel-guest-image-7.8-41.x86_64.qcow2',
    'rhel-7.7':'http://download.devel.redhat.com/rhel-7/rel-eng/RHEL-7/latest-RHEL-7.7/compose/Server/x86_64/images/rhel-guest-image-7.7-261.x86_64.qcow2',
    'rhel-7.6':'http://download.devel.redhat.com/rhel-7/rel-eng/RHEL-7/latest-RHEL-7.6/compose/Server/x86_64/images/rhel-guest-image-7.6-210.x86_64.qcow2',
    'rhel-7.5':'http://download.devel.redhat.com/rhel-7/rel-eng/RHEL-7/RHEL-7.5-RC-1.3/compose/Server/x86_64/images/rhel-guest-image-7.5-146.x86_64.qcow2',
    'rhel-7.4':'http://download.devel.redhat.com/rhel-7/rel-eng/RHEL-7/RHEL-7.4-RC-1.2/compose/Server/x86_64/images/rhel-guest-image-7.4-191.x86_64.qcow2',
    'rhel-7.3':'http://download.devel.redhat.com/pub/rhel/released/RHEL-7/7.3/Server/x86_64/images/rhel-guest-image-7.3-33.x86_64.qcow2',
]
def osp_rhel_list = [
    '10': 'rhel-7.7',
    '13': 'rhel-7.8',
    '14': 'rhel-7.7',
    '15': 'rhel-8.2',
    '16': 'rhel-8.1',
    '16.1': 'rhel-8.2',
    '16.1.1': 'rhel-8.2'
]
def net_config = [
    'rhos-nfv-10.lab.eng.rdu2.redhat.com': ' --topology-nodes undercloud:1,controller:1 --topology-network 4_nets_3_bridges_hybrid -e override.networks.net1.nic=em4 -e override.networks.net2.nic=em1 -e override.networks.net3.nic=em2',
    'dell-r640-oss-13.lab.eng.brq.redhat.com': ' --topology-nodes undercloud:1,controller:1 --topology-network 4_nets_3_bridges_hybrid -e override.networks.net1.nic=em2 -e override.networks.net2.nic=em3 -e override.networks.net3.nic=em4'
]
def IR_VIRSH_IMAGE = img_list[osp_rhel_list[params.release]]
def IR_NET_CONFIG = net_config[params.server]
pipeline {
    agent any
    parameters {
        string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
        string(name: 'release', defaultValue: '16.1', description: 'Who should I say hello to?')
        string(name: 'server', defaultValue: 'rhos-nfv-10.lab.eng.rdu2.redhat.com', description: 'Who should I say hello to?')

        text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')

        booleanParam(name: 'TOGGLE1', defaultValue: true, description: 'Toggle this value')
        booleanParam(name: 'TOGGLE2', defaultValue: true, description: 'Toggle this value')


        choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')

        password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
    }
    stages {
        stage('Example') {
            environment {
                mychoice = "${params.CHOICE}"
                IR_VIRSH_IMAGE = "${IR_VIRSH_IMAGE}"
                IR_NET_CONFIG = "${IR_NET_CONFIG}"
             
            }
            steps {
                echo "Hello ${params.PERSON}"

                echo "Biography: ${params.BIOGRAPHY}"

                echo "Toggle: ${params.TOGGLE}"

                echo "Choice: ${params.CHOICE}"

                echo "Password: ${params.PASSWORD}"
                sh '''
                echo $mychoice
                echo $PERSON
                echo $BIOGRAPHY
                #echo $IR_VIRSH_IMAGE
                #echo $IR_NET_CONFIG
                '''
                
            }
        }
    }
}
