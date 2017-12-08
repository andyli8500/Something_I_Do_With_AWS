## To enable the password-less / identity-less ssh from a master to its slaves (Manually)

1. On your own laptop/machine:
  
  - Adds private key identities to the authentication agent:
    
    ```
    $ ssk-add -K /path/to/the/key.pem
    Identity added: /path/to/the/key.pem (/path/to/the/key.pem)
    ```
    
  - Enables forwarding of the authentication agent connection when connecting to the master
    
    ```
    $ ssh -A hadoop@Master_DNS
    <This will be successfully logged into master node>
    ```
  
2. In Master node:
  
  - Generate a new key for use:
    
    ```
    $ ssh-keygen -t rsa -P "" -f ~/.ssh/id_rsa
    ...<some output>...
    ```
  
  - Add the key to all the slave nodes, using private ip address. Note that, if the '-A' flag was not provided in the previous steps, the command will fail.
  
    ```
    $ ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@slave-private-ip
    ...attempting to log in with the new key(s)...
    ```
3. Now to make sure the above steps are succeeded, run `$ ssh hadoop@slave-private-ip`, and this should login successfully to slave node without using a ".pem" file

Testing of ssh to slaves on master in a EMR step:

- Now, to test it by submitting a EMR step, I have generated a simple bash script which has the following content:
```shell
#!/bin/bash
ssh hadoop@slave-private-ip "echo it works"
```

## Set up all the above during bootstraping

```
#!/bin/bash

# json util
sudo yum install jq -y

# get if the current instance is master or slave
isMaster=$(jq .isMaster /mnt/var/lib/info/instance.json)

# if master -> generate a key -> put in s3
if [ $isMaster = "true" ]; then
	ssh-keygen -t rsa -P "" -f $HOME/.ssh/id_rsa
	aws s3 cp $HOME/.ssh/id_rsa.pub s3://path/to/id_rsa.pub

# if slave -> wait for the key generated -> grab the key -> put the key into ssh authorized keys
else
	sleep 10

	aws s3 cp s3://path/to/id_rsa.pub .
	cat id_rsa.pub >> $HOME/.ssh/authorized_keys
	rm id_rsa.pub
fi
```

Note that, since bootstrap actions does not guarentee orders, put 'sleep 10' for making sure the key is successfully generated and uploaded to S3.
