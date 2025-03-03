# vm_backup_process_proxmox
Procedure of taking backup for a vm in proxmox. 



# Converting Proxmox VM Backup from QEMU to VDI Format

## Step 1: Identify Disk Format
Before conversion, check the format of the disk and storage pools using the following command:
```bash
rados lspools
```
This will display available storage pools such as **sdd** or **hdd**.

## Step 2: Convert Raw Disk to QEMU File Format
Use the following command to convert the total disk to the **QEMU** file format:
```bash
qemu-img convert -f raw -O qcow2 rbd:hdd-pool/vm-138-disk-0 diskname.qcow2
```
Replace **hdd-pool** with the correct pool type and **138** with the VM ID.

After conversion, the backup file will be stored in the node where the VM is located.

## Step 3: Convert QCOW2 to VDI Format
To convert the backup file from **QCOW2** to **VDI** (Ubuntu storage format), run:
```bash
qemu-img convert -f qcow2 -O raw diskname.qcow2 diskname.vdi
```
Replace **diskname** accordingly.

## Step 4: Import Disk to VM
To import the **VDI** disk into the VM, use:
```bash
qm importdisk 138 /root/diskname.qcow2 ceph-hdd --format qcow2
```
Modify **VM ID** and **disk name** as required.

## Step 5: Attach the Disk to the VM
1. Ensure the VM is **turned off**.
2. Select the VM and navigate to **Hardware**.
3. Locate the **unused disk (disk0)** at the bottom.
4. Detach **disk1**, then select **unused disk1** and click **Edit**, adding the disk.
5. Go to **Options**, enable **Secondary Disk (SCSI)**, and save.
6. Restart the VM and verify file access.

---

## Errors and Troubleshooting
### 1. Missing HDD Format
- If the HDD format is not specified while creating the VM, the process may complete, but the VM will not run.

### 2. IP Address Not Detected
- If the IP is missing, check the VM settings.
- If necessary, remove and recreate the VM using the steps above.

