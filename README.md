# Basic Installation & Setup
1. Update apt packages - `sudo apt update`
2. Install snort dependency packages - `sudo apt install -y gcc libpcre3-dev zlib1g-dev libluajit-5.1-dev libpcap-dev openssl libssl-dev libnghttp2-dev libdumbnet-dev bison flex libdnet autoconf libtool`  
3. Make a temporary folder `snort_src`
4. Download DAQ `wget https://www.snort.org/downloads/snort/daq-2.0.7.tar.gz`
5. Extract the tar file and cd into it `tar -xvzf daq-2.0.7.tar.gz`, `cd daq-2.0.7`
6. Auto reconfigure DAQ - `autoreconf -f -i`
7. Run DAQ configuration script - `./configure && make && sudo make install`
8. Navigate back to parent folder, and then download snort source code `cd ..`, `wget https://www.snort.org/downloads/snort/snort-2.9.20.tar.gz`
9. Extract the tar file and cd into it `tar -xvzf snort-2.9.20.tar.gz`, `cd snort-2.9.20`
10. Configure the installation with sourcefire enabled, run make and make install. - `./configure --enable-sourcefire && make && sudo make install`
11. Update share libraries, and create symlink `sudo ldconfig`, `sudo ln -s /usr/local/bin/snort /usr/sbin/snort`
12. Create a user group and user to run snort - `sudo groupadd snort`, `sudo useradd snort -r -s /sbin/nologin -c SNORT_IDS -g snort`
13. Create some directories for snort usages:
    ```
    sudo mkdir -p /etc/snort/rules
    sudo mkdir /var/log/snort
    sudo mkdir /usr/local/lib/snort_dynamicrules
    ```
14. Set the permissions on the newly created directories:
    ```
    sudo chmod -R 5775 /etc/snort
    sudo chmod -R 5775 /var/log/snort
    sudo chmod -R 5775 /usr/local/lib/snort_dynamicrules
    sudo chown -R snort:snort /etc/snort
    sudo chown -R snort:snort /var/log/snort
    sudo chown -R snort:snort /usr/local/lib/snort_dynamicrules
    ```
15. Create rules file for usage:
    ```
    sudo touch /etc/snort/rules/white_list.rules
    sudo touch /etc/snort/rules/black_list.rules
    sudo touch /etc/snort/rules/local.rules
    ```
16. Copy the configuration file from the downloaded folder to `etc` directory
    ```
    sudo cp ~/snort_src/snort-2.9.16/etc/*.conf* /etc/snort
    sudo cp ~/snort_src/snort-2.9.16/etc/*.map /etc/snort
    ```

17. Download community rules (there are other option like subscription rules)
    1.  `wget https://www.snort.org/rules/community -O ~/community.tar.gz`
    2.  Extract the file `sudo tar -xvf ~/community.tar.gz -C ~/`
    3.  Copy the file to the `etc` rules folder - `sudo cp ~/community-rules/community.rules /etc/snort/rules`
    4.  Comment out unnecessary lines - `sudo sed -i 's/include $RULE_PATH/#include $RULE_PATH/' /etc/snort/snort.conf`

17. Configure Network and Rules file path:
    1.  Use Vim to edit `/etc/snort/snort.conf` as specified in Reference

# Setup Verification

18. Test and Validate Configuration - `sudo snort -T -c /etc/snort/snort.conf`
19. Test Local rules
    1.  Add `alert icmp any any -> $HOME_NET any (msg:"ICMP test"; sid:10000001; rev:001;)` rules into the `/etc/snort/rules/local.rules`
    2.  test with `sudo snort -A console -i eth1 -u snort -g snort -c /etc/snort/snort.conf`. (My VM will use eth1)
    3.  Check snort log with `sudo snort -r /var/log/snort/snort.log.` <--- Tab Autocomplete

20. Run snort in backgroup
    1.  Add the required text (Remember to use eth1 for my VM) to `/lib/systemd/system/snort.service` file
    2.  Execute the following
        1.  `sudo systemctl daemon-reload`
        2.  `sudo systemctl start snort`
        3.  `sudo systemctl status snort`


# Reference
[Ucloud documentation](https://upcloud.com/resources/tutorials/install-snort-ubuntu)