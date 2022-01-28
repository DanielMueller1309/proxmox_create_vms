## DanielMueller1309.proxmox_create_vms
### Beschreibung
Erstellt alle in den dafür zuständigem Dictionary aufgeführten VMs auf dem Proxmox Host.
- Zu Beginn wird ``cloud-init`` und das ebenfalls benötigte Programm ``proxmoxer`` heruntergeladen sowie das cloud-init Image auf Basis von Ubuntu.
- Danach erstellt das Proxmox_KVM Modul die VMs mit vorgegebenen Einstellungen aus dem Dictionary, Anschließend wird die Systemfestplatte importiert und startet ``cloud-init`` anschließend neu, insofern neue VMs erstellt wurden.
- Zum Schluss werden die VMs hochgefahren, damit anschließende Loginversuche z.B. über SSH möglich sind, läuft anschließend wird 3 Minuten gewartet. Dies ist nötig um bei einer weiteren Automation zur Ersteinrichtung der VMs, diese auch via SSH erreichbar zu machen.

### Funktioniert auf
- [x] Debian (Version 10)
- [ ] Debian (Version 11)

### Variablen + Defaults
##### Download Variablen
```yml
source_url: https://cloud-images.ubuntu.com/focal/current/focal-server-cloudimg-amd64.img
dest_path: /tmp/focal-server-cloudimg-amd64.img
```yml
#Statische Variablen für VMs:
```yml
vm_node: 'pve' # nodename which we working on
vm_api_token_id: 'ci4pve' # the token to use the api
vm_api_token_secret: "{{ lookup('keepass', 'ci4pve', 'password') }}" # secret to token id
vm_api_host: 'pve' # hostname of the api host, most same as node
vm_api_user: 'root@pam' # the token to use the api
vm_api_password: 'hallowelt' # the password of the api user f.E. root
vm_scsihw: 'virtio-scsi-pci' # Proxmox scsi module for vm
vm_scsi0: 'local-lvm:1,format=raw' # scsi0 device in local-lvm with 1GB and format raw
vm_ide2: 'local:cloudinit,format=qcow2' # ide2 device for cloudinit
vm_bootdisk: 'scsi0' # bootdisk is scsi 0
vm_ciuser: 'user' # cloudinit username
vm_cipassword: 'hallowelt' # cloudinit user passwort
vm_net0: 'virtio,bridge=vmbr0' # networkdevice
vm_nameservers: '192.168.178.11' # ip address of the DNS
vm_resize_size: '+8G' # how to resize scsi0 value is like under 'man qm resize' shown
```
Das scsi0 device wird als platzhalter für das cloudimg benötigt.
###### veränderliche VM eigenschaften

```yml
vms:
  - vm_name: testvm1 # name of the vm
    vm_vmid: 101 # vm id
    vm_ipconfig0: 'ip=192.168.178.15/24,gw=192.168.178.1' # set ip of vm and give gateway
  - vm_name: testvm2
    vm_vmid: 102
    vm_ipconfig0: "ip=dhcp"
```
