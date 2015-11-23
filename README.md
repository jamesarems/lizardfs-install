# lizardfs-install
Install lizardfs on CentOS 7 . 

Master : 10.0.0.1

Shadow master : 10.0.0.2

Chunk server1 : 10.0.0.11

Chunk server2 : 10.0.0.12

Metadata server (cgi web) : 10.0.0.21



This configuration is for Centos 7 minimal

Add repo to all machines ( including client )

 #curl http://packages.lizardfs.com/lizardfs.key > /etc/pki/rpm-gpg/RPM-GPG-KEY-LizardFS

 #curl http://packages.lizardfs.com/yum/el7/lizardfs.repo > /etc/yum.repos.d/lizardfs.repo 

 #yum update 
					

       1. Master installation  ( 10.0.0.1 )

( add all hosts name and IP’s in /etc/hosts file )

 #yum install lizardfs-master

 #cd /etc/mfs

 #cp mfsmaster.cfg.default.dist mfsmaster.cfg

 #cp mfsexports.cfg.default.dist mfsexports.cfg

 #cp mfsgoals.cfg.default.dist mfsgoals.cfg

 #cp mfstopology.cfg.default.dist mfstopology.cfg

 #cp /var/lib/mfs/metadata.mfs.empty /var/lib/mfs/metadata.mfs 

 #systemctl enable lizardfs-master 

 #systemctl start lizardfs-master
							

      2 . Shadow server ( 10.0.0.2 )

This installation is same for every shadow server nodes
 					
				
( add all hosts name and IP’s in /etc/hosts file )
IMPORTENT : Add   ‘msfmaster’ name with IP in all hosts except master .

Eg : 10.0.0.1      msfmaster

 #yum install lizardfs-master

 #cd /etc/mfs

 #cp mfsmaster.cfg.default.dist mfsmaster.cfg	

 #vi msfmaster.cfg

 PERSONALITY=shadow

 #systemctl start lizardfs-master

 #systemctl enable lizardfs-master

       3 . 	Chunk server  ( 10.0.0.11  and 10.0.0.12 )

( add all hosts name and IP’s in /etc/hosts file )

Also add,
10.0.0.1		mfsmaster
	
 #yum install lizardfs-chunkserver

 #cd /etc/mfs

 #cp mfshdd.cfg.default.dist mfshdd.cfg

 #cp mfschunkserver.cfg.default.dist mfschunkserver.cfg			

mfshdd.cfg file is needed to indicate mountpoints of hard drives for your chunkserver.Assuming that there are 2 disks mounted at /mnt/chunk1 and/mnt/chunk2 locations, your mfshdd.cfg file should look like this 

  /mnt/chunk1

  /mnt/chunk2							
		 	 	 		
Remember that chunk servers are run as user mfs, so directories above need appropriate permissions:	

 #chown -R mfs:mfs /mnt/chunk1				

 #chown -R mfs:mfs /mnt/chunk2

 #mfschunkserver start

( Unfortunately default service is not available in CentOS 7 )		

NOTE: Repeat this steps in all chunk servers .		
		
        
           4.   Metadata server & CGI Web  ( 10.0.0.21 )

( add all hosts name and IP’s in /etc/hosts file )

Also add,

10.0.0.1		mfsmaster


 #yum install lizardfs-cgiserv lizardfs-metalogger

 #cp mfsmetalogger.cfg.default.dist mfsmetalogger.cfg

 #mfsmetalogger start

 #mfscgiserv start

NOTE : Repeat this steps in all metadata servers

Acces web at  http://10.0.0.21:9425/mfs.cgi?masterhost=mfsmaster


       5.  Client access

( add all hosts name and IP’s in /etc/hosts file )

Also add,

10.0.0.1		mfsmaster

 #yum install lizardfs-client

 #mkdir /opt/mydata

 #mfsmount /opt/mydata


If we are using debian client,
						
 #echo "deb http://packages.lizardfs.com/debian/$(lsb_release ­sc) $(lsb_release ­sc) main" > /etc/apt/sources.list.d/lizardfs.list

 #echo "deb­src http://packages.lizardfs.com/debian/$(lsb_release ­sc) $(lsb_release ­sc) main" >> /etc/apt/sources.list.d/lizardfs.list 

 #apt-get update

 #apt-get install lizardfs-client

That's it . Now we have world’s best highly reliable  open source data server/center . 

We can add more chunk servers and shadow servers for  data security and high availability .

                   7.  Recover deleted files from trash bin

  # mfsmount /trash -H mfsmaster -o mfsmeta
  
  # ls
  
  reserved  trash

  # cd trash
  
  # mv test.txt undel    ( Move files to ‘undel’ folder inside the trash folder )
