# create a virtual machine of Ubuntu Server 20.04.4 LTS through vmware

- ## download Ubuntu Server 20.04.4 LTS installation file
  download the installation file from the page below
  ```
  https://jp.ubuntu.com/download
  ```
- ## install file
  - Open vmware and click the button as shown below
  ![create-a-virtual-machine](./../image/create-a-virtual-machine.png) 

  - Select the downloaded installation file as shown below
  ![select-installation-file](./../image/select-installation-file.png) 

  - input the information as shown below
  ![input-the-information](./../image/input-the-information.png) 
  
  - input the virtual machine name and select the save path <br>

  - input the disk size as you want and select the `Split the virtual disk into multiple files` as shown below
  ![input-the-disk-size](./../image/input-the-disk-size.png) 

  - Confirm the information and change the Network adapter. <br>
    change from `NAT` TO `bridge` as shown below
    ![network-adapter](./../image/network-adapter.png) 

  - Use UP, DOWN and ENTER keys to select your language.<br>

  - keyboard configuration, select your keyboard layout
  - Network Configure<br>
    Fixed IP config<br>
    `ens33` > `Edit IPv4` > `IPv4 Method: Manual`
    config as shown below
    ![IPv4-configuration](./../image/IPv4-configuration.png) 

  - configure proxy<br>
    if you do not need to make any settings, just select `Done` and press the `Enter` key.

  - configure ubuntu archive mirrow<br>
    select `Done` and press the `Enter` key.

  - Guided storage configuration<br>
    select `Done` and press the `Enter` key.<br>
    the storage settings will be displayed.check the settings. and if there are no problems, select `Done` and press the `Enter` key.

  - profile setup<br>
    set as shown below
    ![profile-setup](./../image/profile-setup.png) 

  - SSH Setup<br>
    config as shown below
    ![ssh-config](./../image/ssh-config.png) 

  - Featured server snaps<br>
    check what you want

  - restart server
    Reboot the server after installation

    