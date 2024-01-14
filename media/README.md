# PREREQUISITES

## Network Share & CIFS mount config:

> NOTE: A lot of these scripts rely on you defining a UID/GID so that the permissions line up across hosts. Assigning these is left as an exercise for the reader, but for the scripts below, I've subsituted the chosen UID with XXX and the chosen GID with YYY. Please replace them whenever you see XXX or YYY in a given command/script. Additionally, replace SHARE_NAME and IP_ADDRESS with their respective values too!

### Proxmox config:

For shares via Proxmox into Portainer you need to mount them as CIFS shares first

1. we'll mount the NAS into /mnt/remote/`SHARENAME` using the following command on the Proxmox instance:
    ```
    mount -t cifs -o rw,relatime,vers=3.1.1,addr=IP_ADDRESS,username=SHARE_USER,password=SHARE_PASS,uid=XXX,gid=YYY //IP_ADDRESS/SHARE_NAME /mnt/remote/SHARE_NAME
    ```
2. for the UID/GID to pass-through, we need to do some custom UID/GID mapping - because these are unprivledged containers:
    - add this in the container config (located at `/etc/pve/lxc/CONTAINER_ID.conf`)
    ```
    lxc.idmap: u 0 100000 XXX   # for UID, from 0, starting at 100000, allocate XXX
    lxc.idmap: g 0 100000 YYY   # for GID, from 0, starting at 100000, allocate YYY
    lxc.idmap: u XXX XXX 1      # for UID, from XXX, starting at XXX, allocate 1
    lxc.idmap: g YYY YYY 1      # for GID, from YYY, starting at YYY, allocate 1
    # for the next command, I'll use AAA and BBB to mean the number XXX+1, and YYY+1 respectively
    lxc.idmap: u AAA 100AAA 65535-XXX # for UID, from AAA (XXX+1), starting at 100AAA, allocate 65535-XXX
    lxc.idmap: g BBB 100BBB 65535-YYY # for GID, from BBB (YYY+1), starting at 100BBB, allocate 65535-YYY
    ```
3. Also now enable the UID/GID mapping to a real user (root?)
    - in `/etc/subuid`, add the following:
    ```
    root:100000:65536
    root:XXX:1 # substituting your XXX UID from above
    ```
    - in `/etc/subgid`, add the following:
    ```
    root:100000:65536
    root:YYY:1 # substituting your YYY GID from above
    ```
4. Now confirm that everything works! If it doesn't (or you get PERMISSION DENIED errors) reboot Proxmox!
    ```
    touch /mnt/remote/SHARE_NAME/test
    ```
5. Go back to Step 1 and repeat that for as many shares as you want!

## Portainer

1. You can do this via the GUI if you want via your LXC > Resources > Add > Mount Point.
2. If that doesn't work (or you don't see your new Storage listed), then we need to drop into config:
    - add this in the container config (located at `/etc/pve/lxc/CONTAINER_ID.conf`)
    ```
    mp0: /mnt/remote/SHARE_NAME,mp=/mnt/remote/SHARE_NAME
    ```
    - If you've added multiple shares, then add them too, increasing the number next to `mp` by one each time.
3. Reboot the Portainer LXC and check the `Resources` tab and ensure they are listed there!

## Containers/Stacks

For shares via Portainer into containers you can then reference them as normal volumes (local to the Portainer image)
