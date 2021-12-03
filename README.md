# DOWireGuard
For the Digital Ocean/Wire Guard project for class
1. Go to https://m.do.co/c/4d7f4ff9cfe4 and create an account (on Digital Ocean)
    - This account will have $100 that can be used for this lab
    - It will still require using a payment card to set up an account
    - If you leave the lab running, it will continue to run out of credits
    - If it uses credits after your free credits have run out/expired, then it will charge your payment card, so be sure to turn it off when you're done
2. Create a droplet
    - Go to the "Control Panel" page on digital ocean
    - Click on "Droplets" in the panel on the right side
        - If "Manage" is collapsed, you may need to expand it, "Droplets" is under this menu
    - Click "Create Droplet"
    - You can leave the default for the distribution (Ubuntu) and the plan (Basic, shared CPU)
    - Set it to the cheapest option for anything involving price, since we don't need much performance for the lab
    - Select the data center closest to you
    - Either create an SSH key, or Create a Password
        - The password is easier, but less secure
    - Finalize the droplet (optionally, you can choose a name first and/or create tags)
3. Install Docker
    - Enter your droplet's console
        - Click on your droplet
        - Click on console 
    - `sudo apt update`
    - `sudo apt upgrade` (this will just upgrade things on your computer)
    - `sudo apt install docker.io`
    - `sudo apt install curl`
    - `sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
    - `sudo chmod +x /usr/local/bin/docker-compose`
        - Test it with `docker-compose --version`
4. Install wireguard
    - `mkdir -p ~/wireguard/`
    - `mkdir -p ~/wireguard/config/
    - `nano ~/wireguard/docker-compose.yml`
    - Enter the following into the editor you just pulled up

### Data to enter
    version: '3.8'
    services:
      wireguard:
        container_name: wireguard
        image: linuxserver/wireguard
        environment:
          - PUID=1000
          - PGID=1000
          - TZ=Asia/Hong_Kong
          - SERVERURL=1.2.3.4
          - SERVERPORT=51820
          - PEERS=pc1,pc2,phone1
          - PEERDNS=auto
          - INTERNAL_SUBNET=10.0.0.0
        ports:
          - 51820:51820/udp
        volumes:
          - type: bind
            source: ./config/
            target: /config/
          - type: bind
            source: /lib/modules
            target: /lib/modules
        restart: always
        cap_add:
          - NET_ADMIN
          - SYS_MODULE
        sysctls:
          - net.ipv4.conf.all.src_valid_mark=1

-   - Replace the "Asia/Hong_Kong" with your time zone
        -  The list of timezones can be found at https://en.wikipedia.org/wiki/List_of_tz_database_time_zones  
    - Find the droplet's IP address, on the Digital Ocean dashboard, and set the `SERVERURL` to the IP address 
    - Set `PEERS` to the number of devices you're going to connect to it
