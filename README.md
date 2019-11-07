# mmreplication
This playbook is used without any warranty.
This playbook makes a new slave node from a mysql server in docker, as well as mysql server on bare os. Binlogs must be on on the mysql server. The slave node is created in a docker container only. Actually, it installes a master-master replication, so be careful if your current server is slave also. The replication parameters will be overriden on your server.
## Usage. ##
Copy hosts.example to hosts. Change paremeters of the hosts file.

    [masterip]
    XX.XX.XX.XX - your current mysql server ip address here.
    [slaveip]
    YY.YY.YY.YY - your future mysql slave ip address here.
    
    [replication:children] - this is for inheritance so you shouldn't write the same variables for different hosts.
    masterip
    slaveip
    
    [replication:vars]
    #password=-p1
    purepassword="" - your current mysql password. It will be the same on your future slave.
    password="" - purepassword and password must be the same, but password is with prefix -p
    replicationuser=replicator
    replicationpassword=123
    slavedockerdirectoryname=maria-volumes - a directory for docker mounts and a docker-compose file.
    masterdockerdirectoryname="" - it is empty line when your current master is on bare os.
    masterip=XX.XX.XX.XX
    slaveip=YY.YY.YY.YY
    slaveid=10003 - mysql server_id variable will be equal to 10003 here.
    slaveconsoleprompt=YY.YY.YY.YY - mysql's prompt
    slavecontainername=YY.YY.YY.YY - container console's prompt
    basestodump=base1 base2 base3 - bases to copy from master to the future slave
    #dockerexecline=docker exec -ti $(docker ps -q)
    dockerexecline="" - empty line when your current master is on bare os.
    proxyip=ZZ.ZZ.ZZ.ZZ - to install proxy ip address for docker service
    proxyport=8888

Launch it like: ansible-playbook RecreateSlave.yml
## Files meaning. ##
RecreateSlave.yml -- the main playbook.\
hosts -- a file with settings.\
docker-compose.yml.j2 - it is cloned to slaveip=YY.YY.YY.YY, slavedockerdirectoryname=maria-volumes\
additionalconfig.cnf.j2 - place an additional slave mysql server settings here.\
masterpos_store.sh.j2 - it is being started on master and stores masterpos and binlog file name in separate files that are transfered to the slave afrerwards.\
https-proxy.conf.j2 - for placing in /etc/systemd/system/docker.service.d/https-proxy.conf \
ansible.cfg - this file is not to write ansible settings in /etc/ansible directory. Everything is working from the playbook's directory.
