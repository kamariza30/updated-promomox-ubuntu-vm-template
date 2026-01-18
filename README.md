# updated-promomox-ubuntu-vm-template

This is updated notes/guide to create vm template using ubuntu cloud image

## Steps 1: In Proxmox, click on ```Create VM```
<img width="646" height="248" alt="image" src="https://github.com/user-attachments/assets/d818e96f-1f15-4164-8265-31e4301d3d06" />

### In OS Tab > uncheck ``` Do not use any media```
<img width="672" height="309" alt="image" src="https://github.com/user-attachments/assets/d75d7294-fd1c-469b-b85c-be3d79067da0" />

### In System Tab: 
- Machine:```q35``` 
- BIOS:```OVMF (UEFI)```
- EFI Storage:```local```
- Qemu Agent > Checked
<img width="724" height="379" alt="image" src="https://github.com/user-attachments/assets/91a742e0-dd70-405d-9af8-6c287bf9af96" />

### In Disks Tab, Delete the disk as we will create and attach new disk manually
<img width="639" height="350" alt="image" src="https://github.com/user-attachments/assets/f823f904-7d8f-40a6-a968-f168d8f9e5d5" />

### In CPU Tab, set Cores: ```2```
<img width="647" height="258" alt="image" src="https://github.com/user-attachments/assets/da6de449-10d6-4863-b8a5-84410d4c3803" />

### In Memory Tab, Set MiB: ```2028```
<img width="631" height="245" alt="image" src="https://github.com/user-attachments/assets/bed22af6-5ea1-4b59-ab34-96e44eb111fa" />

### In Network Tab, let default value.

### In Confirm Tab, Start after created: ```uncheck```. Then click on Finish
<img width="433" height="205" alt="image" src="https://github.com/user-attachments/assets/d213bf9e-7f8e-4b05-9d6e-7e7f6a4d57e7" />

## Steps 2: Modifying VM

### On the created VM > Hardware? remove CD/DVD
<img width="791" height="378" alt="image" src="https://github.com/user-attachments/assets/d0f32676-bc2d-4529-8266-ade19e6bc68d" />

### On the same Hardware > Add > CloudInit Drive > CloudInit storage: ```local```
<img width="620" height="317" alt="image" src="https://github.com/user-attachments/assets/02f14581-b0ed-4ace-8471-6d778c4136dc" />

## Steps 3: Download cloud Image Ubuntu 24.04

### Go to https://cloud-images.ubuntu.com/noble/current/ and copy the link image of ```noble-server-cloudimg-amd64.img```, https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.img

### Go to Proxmox > Node > Shell > then type wget https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.img
<img width="1242" height="348" alt="image" src="https://github.com/user-attachments/assets/24bc81c0-c0b3-4864-a0e9-2245bee026f9" />

### Rename the images, ```mv noble-server-cloudimg-amd64.img ubuntu-cloudinit.qcow2```
<img width="674" height="139" alt="image" src="https://github.com/user-attachments/assets/c787f55c-3826-4ba5-8fd0-07dd69f1536a" />

### Resize the images,  ```qemu-img resize ubuntu-cloudinit.qcow2 32G```
<img width="526" height="70" alt="image" src="https://github.com/user-attachments/assets/cdf0068d-8bcc-457b-a1be-663fae5ac310" />

### Attach the disk to the vm (my vm id is 902) ```qm importdisk 902 ubuntu-cloudinit.qcow2 local-lvm```
<img width="702" height="130" alt="image" src="https://github.com/user-attachments/assets/ff0e8dc6-c041-4541-9996-d6ca14d18fdd" />

### Wait until it showing successfully
<img width="715" height="95" alt="image" src="https://github.com/user-attachments/assets/6b175359-8219-4958-a122-2e0231a3872c" />

## Steps 4: Enabling The Disk Created on Steps 3

### Go to Proxmox> This VM > Hardware > Unused Disk. Select this Disk and click on Edit. Follow below settings
<img width="757" height="270" alt="image" src="https://github.com/user-attachments/assets/db192ed2-b700-4ab7-b32f-c3230f2bd436" />

### Now the disk is enabled
<img width="799" height="181" alt="image" src="https://github.com/user-attachments/assets/b8dda5b4-ac7a-4cdb-82de-2d73d950c7f9" />

### Go to Options > Boot Order. Disable the network boot and enabled the disk boot
<img width="805" height="330" alt="image" src="https://github.com/user-attachments/assets/29ee6401-c4dd-4bbc-bfd8-6dae50b35455" />

### Right click on this VM > Convert to template
<img width="621" height="320" alt="image" src="https://github.com/user-attachments/assets/9c202861-d67e-4534-96dd-0b35e8c770b0" />

### Template is now ready

## Steps 5: Configuring CloudInit

### Set user and password. IP Config to DHCP. Once done click on regenerate image
<img width="592" height="287" alt="image" src="https://github.com/user-attachments/assets/bdb895a6-3dbe-4a80-a70e-ee8927778d35" />

### We can also add ssh key to the template so we can configure server using ansible later

## Steps 5: Clone Template to create VM
<img width="465" height="131" alt="image" src="https://github.com/user-attachments/assets/748951c5-af02-4581-bd1b-ea71feb54462" />

### Fill in the details. Mode: Full Clone
<img width="755" height="322" alt="image" src="https://github.com/user-attachments/assets/f477c5ee-3cd0-4f6f-bb12-dfab9a77137c" />

### Once the VM created, start and login to the vm

## Post Installation
### Instal qemu guest agent with ```sudo apt install qemu-guest-agent -y``` . reboot the VM after installed. This will help to display the IP address on Summary Menu.

### Below is before installing Qemu Guest Agent
<img width="602" height="425" alt="image" src="https://github.com/user-attachments/assets/54490687-2b7a-4dda-8c06-95985db77e46" />

### After Installing Qemu Guest Agent
<img width="590" height="397" alt="image" src="https://github.com/user-attachments/assets/e04cfaa2-92c9-4c45-94eb-cd35038552ca" />

## Notes
### If you want to set fix IP you may do this after cloning from the template by editing your IP address in Cloud-init. Then click on ```Regenerate Image```. Make sure to do this before starting the VM

























