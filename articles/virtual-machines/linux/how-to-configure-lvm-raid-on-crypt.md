---
title: Come configurare LVM e RAID in una macchina virtuale Linux
description: In questo articolo vengono fornite istruzioni sulla configurazione di LVM e RAID su crittografia nelle macchine virtuali Linux.This article provides instructions on configuring LVM and RAID on crypt on Linux VMs.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 78ba47ba887cf7c90adf70d9d444fbd8a3c721cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284909"
---
# <a name="how-to-configure-lvm-and-raid-on-crypt"></a>Come configurare LVM e RAID su cripta

Questo documento è un processo passo-passo su come eseguire LVM su cripta e Raid sulle configurazioni di cripta.

### <a name="environment"></a>Environment

- Distribuzioni Linux
    - RHEL 7,6
    - Ubuntu 18,04
    - SUSE 12 anni
- Passaggio singolo ADE
- ADE Dual Pass


## <a name="scenarios"></a>Scenari

**Questo scenario è applicabile alle estensioni ad adE a doppio e passaggio singolo.**  

- Configurare LVM su dispositivi crittografati (LVM-on-Crypt)
- Configurare RAID su dispositivi crittografati (RAID-on-Crypt)

Una volta crittografati i dispositivi sottostanti, è possibile creare le strutture LVM o RAID sopra a tale livello crittografato. I volumi fisici (PV) vengono creati sopra il layer crittografato.
I volumi fisici vengono utilizzati per creare il gruppo di volumi.
Creare i volumi e aggiungere le voci necessarie in /etc/fstab. 

![Controllare i dischi collegati a PowerShellCheck disks attached PowerShell](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

In modo analogo, il dispositivo RAID viene creato sopra il livello crittografato sui dischi. Un file system viene creato sopra il dispositivo RAID e aggiunto a /etc/fstab come dispositivo normale.

### <a name="considerations"></a>Considerazioni

Il metodo consigliato per l'uso è LVM-on-Crypt.

Il RAID viene considerato quando LVM non può essere utilizzato a causa di limitazioni specifiche di applicazioni/ambienti.

Si utilizzerà l'opzione EncryptFormatAll , le informazioni https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vmssu questa funzionalità si trovano qui: .

Mentre questo metodo può essere fatto quando anche la crittografia del sistema operativo, stiamo solo crittografando unità di dati.

Questa procedura presuppone che tu abbia già esaminato i https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux prerequisiti menzionati qui: e qui https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart.

La versione adE dual pass si trova nel percorso di deprecazione e non deve più essere utilizzata nelle nuove crittografie ADE.

### <a name="procedure"></a>Procedura

Quando si utilizzano le configurazioni "on crypt", si starà seguendo il processo descritto di seguito:

>[!NOTE] 
>Stiamo usando le variabili in tutto il documento, sostituire i valori di conseguenza.
## <a name="general-steps"></a>Passaggi generali
### <a name="deploy-a-vm"></a>Distribuire una macchina virtuale 
>[!NOTE] 
>Anche se questo è facoltativo, è consigliabile applicarlo in una macchina virtuale appena distribuita.

PowerShell
```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Interfaccia della riga di comando:
```bash
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Collegare i dischi alla macchina virtuale:Attach disks to the vm:
Ripetere l'operazione per $N numero di nuovi dischi da collegare a PowerShell della macchina virtualeRepeat for a new disks you want to attach to the VM PowerShell
```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```
Interfaccia della riga di comando:
```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```
### <a name="verify-the-disks-are-attached-to-the-vm"></a>Verificare che i dischi siano collegati alla macchina virtuale:Verify the disks are attached to the VM:
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Controllare l'interfaccia della](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png) riga di comando di PowerShell collegata ai dischi:Check disks attached PowerShell CLI:
```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Controllare i dischi](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png) collegati portale ![CLI:](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png) Controllare i dischi collegati CLI OS:
```bash
lsblk 
```
![Controllare i dischi collegati al portale](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)
### <a name="configure-the-disks-to-be-encrypted"></a>Configurare i dischi da crittografare
Questa configurazione viene eseguita a livello di sistema operativo, i dischi corrispondenti sono configurati per una crittografia ADE tradizionale:This configuration is done that the operating system level, the corresponding disks are configured for a traditional ADE encryption:

I file system vengono creati sopra i dischi.

I punti di montaggio temporanei vengono creati per montare i file system.

I file system sono configurati su /etc/fstab per essere montati al momento dell'avvio.

Controllare la lettera del dispositivo assegnato ai nuovi dischi, in questo esempio stiamo usando quattro dischi dati

```bash
lsblk 
```
![Controllare i dischi collegati os](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-filesystem-on-top-of-each-disk"></a>Creare un file system sopra ogni disco.
Questo comando scorre una creazione del file system ext4 su ogni disco definito nella parte "in" del ciclo "for".
```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Controllare i dischi](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png) collegati os Trovare l'UUID dei file system creati di recente, creare una cartella temporanea per montarlo, aggiungere le voci corrispondenti su /etc/fstab e montare tutti i file system.

Questo comando scorre anche su ogni disco definito nella parte "in" del ciclo "for":
```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 
### <a name="verify-the-disks-are-mounted-properly"></a>Verificare che i dischi siano montati correttamente:
```bash
lsblk
```
![Controllare i file](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png) system temporanei montati e configurati:
```bash
cat /etc/fstab
```
![Controllare fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)
### <a name="encrypt-the-data-disks"></a>Crittografare i dischi dati:
PowerShell con KEK:
```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```
CLI con KEK:
```bash
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>Verificare lo stato della crittografia

Continuare con il passaggio successivo solo quando tutti i dischi sono crittografati.

PowerShell:
```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Controllare la](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png) crittografia ps CLI:
```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Controllare la](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png) crittografia ![CLI Portale: Controllare la crittografia OS](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png) OS Level:
```bash
lsblk
```
![Controllare l'interfaccia della riga di comando della crittografia](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

L'estensione aggiungerà i file system a "/var/lib/azure_disk_encryption_config/azure_crypt_mount" (una vecchia crittografia) o aggiunto a "/etc/crypttab" (nuove crittografie).

Non modificare nessuno di questi file.

Questo file si occuperà dell'attivazione di questi dischi durante il processo di avvio in modo che possano essere successivamente utilizzati da LVM o RAID. 

Non preoccuparti per i punti di montaggio su questo file, come ADE perderà la possibilità di ottenere i dischi montati come un normale file system dopo che creiamo un volume fisico o un dispositivo raid in cima a quei dispositivi crittografati (che si libererà del formato del file system che abbiamo usato durante il processo di preparazione).
### <a name="remove-the-temp-folders-and-temp-fstab-entries"></a>Rimuovere le cartelle temporanee e le voci temp fstab
Smontare i file system sui dischi che verranno utilizzati come parte di LVM
```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
E rimuovere le voci /etc/fstab:
```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Verificare che i dischi non siano montati e che le voci in /etc/fstab siano state rimosse
```bash
lsblk
```
![Controllare i file](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png) system temporanei smontati e configurati:
```bash
cat /etc/fstab
```
![Controllare le voci di temp fstab vengono rimosse](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)
## <a name="for-lvm-on-crypt"></a>Per LVM-on-crypt
Ora che i dischi sottostanti sono crittografati, è possibile procedere alla creazione delle strutture LVM.

Anziché utilizzare il nome del dispositivo, utilizzare i percorsi /dev/mapper per ognuno dei dischi per creare un volume fisico (sul livello di crittografia sul disco non sul disco stesso).
### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Configurare LVM sopra i livelli crittografati
#### <a name="create-the-physical-volumes"></a>Creare i volumi fisici
Verrà visualizzato un avviso in cui viene chiesto se è possibile cancellare la firma del file system. 

È possibile continuare immettendo 'y' o utilizzare l'eco "y" come mostrato:
```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![pvcreate](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)
>[!NOTE] 
>I nomi /dev/mapper/device devono essere sostituiti per i valori effettivi in base all'output di lsblk.
#### <a name="verify-the-physical-volumes-information"></a>Verificare le informazioni sui volumi fisici
```bash
pvs
```
![controllare i volumi fisici 1](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)
#### <a name="create-the-volume-group"></a>Creare il gruppo di volumi
Creare il VG utilizzando gli stessi dispositivi già inizializzati
```bash
vgcreate vgdata /dev/mapper/
```
### <a name="check-the-volume-group-information"></a>Controllare le informazioni sul gruppo di volumi
```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![controllare i volumi fisici 2](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)
#### <a name="create-logical-volumes"></a>Creare volumi logici
```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 
#### <a name="check-the-logical-volumes-created"></a>Controllare i volumi logici creati
```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![controllare lvs](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)
#### <a name="create-filesystems-on-top-of-the-logical-volumes-structures"></a>Creare file system sopra le strutture dei volumi logici
```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```
#### <a name="create-the-mount-points-for-the-new-filesystems"></a>Creare i punti di montaggio per i nuovi file system
```bash
mkdir /data0
mkdir /data1
```
#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Aggiungere i nuovi file system a /etc/fstab e montarli
```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
#### <a name="verify-that-the-new-filesystems-are-mounted"></a>Verificare che i nuovi file system siano montati
```bash
lsblk -fs
df -h
```
![check logical](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png) volumes On this variation of lsblk, we're listing the devices showing the dependencies on reverse order, this option helps to identify the devices grouped by the logical volume instead of the original /dev/sd [disk] device names.

Importante: assicurarsi che l'opzione "nofail" venga aggiunta alle opzioni del punto di montaggio dei volumi LVM creati su un dispositivo crittografato ADE. È importante evitare che il sistema operativo si blocchi durante il processo di avvio (o in modalità di manutenzione). 

Il disco crittografato viene sbloccato alla fine del processo di avvio, i volumi LVM e i file system verranno montati automaticamente.

Se l'opzione nofail non viene utilizzata, il sistema operativo non entrerà mai nella fase in cui ADE viene avviato e i dischi dati vengono sbloccati e montati.

È possibile testare il riavvio della macchina virtuale e la convalida dei file system vengono automaticamente montati dopo l'avvio. 

Tenuto conto del fatto che questo processo può richiedere alcuni minuti a seconda del numero di file system e delle dimensioni
#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Riavviare la macchina virtuale e verificare dopo il riavvio
```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="for-raid-on-crypt"></a>Per RAID-on-Crypt
Ora che i dischi sottostanti sono crittografati è possibile continuare a creare le strutture RAID, come LVM, invece di usare il nome del dispositivo, utilizzare i percorsi /dev/mapper per ognuno dei dischi.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Configurare RAID sopra il livello crittografato dei dischi
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![mdadm creare](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)
>[!NOTE] 
>I nomi /dev/mapper/device devono essere sostituiti per i valori effettivi in base all'output di lsblk.
#### <a name="checkmonitor-the-raid-creation"></a>Controllare/monitorare la creazione del RAID:
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![controllare mdadm](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)
#### <a name="create-a-filesystem-on-top-of-the-new-raid-device"></a>Creare un file system sopra il nuovo dispositivo Raid:
```bash
mkfs.ext4 /dev/md10
```
Creare un nuovo punto di montaggio per il file system, aggiungere il nuovo file system a /etc/fstab e montarlo
```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```
Verificare che i nuovi file system siano montati
```bash
lsblk -fs
df -h
```
![controllare mdadm](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Importante: assicurarsi che l'opzione "nofail" venga aggiunta alle opzioni del punto di montaggio dei volumi RAID creati su un dispositivo crittografato ADE. 

È molto importante evitare che il sistema operativo si blocchi durante il processo di avvio (o in modalità di manutenzione). 

Il disco crittografato verrà sbloccato alla fine del processo di avvio e i volumi RAID e i file system verranno montati automaticamente fino a quando non saranno sbloccati da ADE, se non viene utilizzata l'opzione nofail.

Il sistema operativo non entrerà mai nella fase in cui ADE viene avviato e i dischi dati vengono sbloccati e montati.

È possibile testare il riavvio della macchina virtuale e la convalida dei file system vengono automaticamente montati dopo l'avvio. Tenuto conto del fatto che questo processo può richiedere alcuni minuti a seconda del numero di file system e delle dimensioni
```bash
shutdown -r now
```
E quando è possibile accedere:
```bash
lsblk
df -h
```
## <a name="next-steps"></a>Passaggi successivi

- [Guida alla risoluzione dei problemi di crittografia dischi di Azure](disk-encryption-troubleshooting.md)

