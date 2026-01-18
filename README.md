# updated-promomox-ubuntu-vm-template

This is updated notes/guide to create vm template using ubuntu cloud image

## Step 1: Create a New VM in Proxmox
Click on Create VM in the Proxmox interface.

<img width="646" height="248" alt="image" src="https://github.com/user-attachments/assets/d818e96f-1f15-4164-8265-31e4301d3d06" />

### OS Tab
Uncheck Do not use any media.

<img width="672" height="309" alt="image" src="https://github.com/user-attachments/assets/d75d7294-fd1c-469b-b85c-be3d79067da0" />

### System Tab
- Machine:```q35``` 
- BIOS:```OVMF (UEFI)```
- EFI Storage:```local```
- Qemu Agent: ✅ Checked
<img width="724" height="379" alt="image" src="https://github.com/user-attachments/assets/91a742e0-dd70-405d-9af8-6c287bf9af96" />

### Disks Tab
Delete the default disk. We will create and attach a new disk manually later.

<img width="639" height="350" alt="image" src="https://github.com/user-attachments/assets/f823f904-7d8f-40a6-a968-f168d8f9e5d5" />

### CPU Tab
Set Cores: ```2```

<img width="647" height="258" alt="image" src="https://github.com/user-attachments/assets/da6de449-10d6-4863-b8a5-84410d4c3803" />

### Memory Tab 
Set Memory (MiB): ```2048```

<img width="631" height="245" alt="image" src="https://github.com/user-attachments/assets/bed22af6-5ea1-4b59-ab34-96e44eb111fa" />

### Network Tab
let default value.

### Confirm Tab 
Uncheck **Start after created**, then click **Finish**.

<img width="433" height="205" alt="image" src="https://github.com/user-attachments/assets/d213bf9e-7f8e-4b05-9d6e-7e7f6a4d57e7" />

## Steps 2: Modifying VM

### Remove CD/DVD Drive
Navigate to the created VM → Hardware → Select the CD/DVD drive and remove it.

<img width="791" height="378" alt="image" src="https://github.com/user-attachments/assets/d0f32676-bc2d-4529-8266-ade19e6bc68d" />

### Add CloudInit Drive
In Hardware → Click Add → CloudInit Drive → Set Storage to ```local```.

<img width="620" height="317" alt="image" src="https://github.com/user-attachments/assets/02f14581-b0ed-4ace-8471-6d778c4136dc" />

## Step 3: Download Ubuntu 24.04 Cloud Image

### Get the Image URL
Go to **https://cloud-images.ubuntu.com/noble/current/** and copy the link for ```noble-server-cloudimg-amd64.img```.

### Download the Image via Shell
Navigate to Proxmox → Node → Shell, then run:
```wget https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.img```

<img width="1242" height="348" alt="image" src="https://github.com/user-attachments/assets/24bc81c0-c0b3-4864-a0e9-2245bee026f9" />

### Rename the Image
```mv noble-server-cloudimg-amd64.img ubuntu-cloudinit.qcow2```

<img width="674" height="139" alt="image" src="https://github.com/user-attachments/assets/c787f55c-3826-4ba5-8fd0-07dd69f1536a" />

### Resize the Image
```qemu-img resize ubuntu-cloudinit.qcow2 32G```

<img width="526" height="70" alt="image" src="https://github.com/user-attachments/assets/cdf0068d-8bcc-457b-a1be-663fae5ac310" />

### Attach the Disk to the VM
Replace 902 with your VM ID:
```qm importdisk 902 ubuntu-cloudinit.qcow2 local-lvm```

<img width="702" height="130" alt="image" src="https://github.com/user-attachments/assets/ff0e8dc6-c041-4541-9996-d6ca14d18fdd" />

### Wait until the import completes successfully.
<img width="715" height="95" alt="image" src="https://github.com/user-attachments/assets/6b175359-8219-4958-a122-2e0231a3872c" />

## Steps 4: Enable the Imported Disk

### Attach the Unused Disk
Go to Proxmox → VM → Hardware → Select Unused Disk → Click Edit and apply the settings below.

<img width="757" height="270" alt="image" src="https://github.com/user-attachments/assets/db192ed2-b700-4ab7-b32f-c3230f2bd436" />

### The disk is now enabled.
<img width="799" height="181" alt="image" src="https://github.com/user-attachments/assets/b8dda5b4-ac7a-4cdb-82de-2d73d950c7f9" />

### Configure Boot Order
Go to Options → Boot Order. Disable network boot and enable the disk as the primary boot device

<img width="805" height="330" alt="image" src="https://github.com/user-attachments/assets/29ee6401-c4dd-4bbc-bfd8-6dae50b35455" />

### Convert to Template
Right-click on the VM → Convert to template.

<img width="621" height="320" alt="image" src="https://github.com/user-attachments/assets/9c202861-d67e-4534-96dd-0b35e8c770b0" />

### Template is now ready

## Steps 5: Configure CloudInit

### Set the username, password, and IP configuration (DHCP). Once configured, click Regenerate Image.
<img width="592" height="287" alt="image" src="https://github.com/user-attachments/assets/bdb895a6-3dbe-4a80-a70e-ee8927778d35" />

### You can also add an SSH public key to the template for Ansible configuration later.

## Step 6: Clone Template to Create a VM
Right-click on the template and select Clone.

<img width="465" height="131" alt="image" src="https://github.com/user-attachments/assets/748951c5-af02-4581-bd1b-ea71feb54462" />

### Fill in the details and set Mode to Full Clone.
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

























