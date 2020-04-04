---
title: Configurare LVM e RAID nei dispositivi crittografati - Crittografia disco di AzureConfigure LVM and RAID on encrypted devices - Azure Disk Encryption
description: In questo articolo vengono fornite istruzioni per la configurazione di LVM e RAID su dispositivi crittografati per macchine virtuali Linux.This article provides instructions for configuring LVM and RAID on encrypted devices for Linux VMs.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 4e342ff44af38b8e79dc8695c1270b1f5c68e0a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657436"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Configurare LVM e RAID su dispositivi crittografati

Questo articolo è un processo dettagliato per l'esecuzione di LVM (Logical Volume Management) e RAID su dispositivi crittografati. Il processo si applica agli ambienti seguenti:The process applies to the following environments:

- Distribuzioni di Linux
    - RHEL 7,6
    - Ubuntu 18,04
    - SUSE 12 anni
- Estensione a passaggio singolo di Crittografia disco di AzureAzure Disk Encryption single-pass extension
- Estensione a doppio passaggio di Crittografia dischi di AzureAzure Disk Encryption dual-pass extension


## <a name="scenarios"></a>Scenari

Le procedure descritte in questo articolo supportano gli scenari seguenti:The procedures in this article support the following scenarios:  

- Configurare LVM su dispositivi crittografati (LVM-on-crypt)
- Configurare RAID su dispositivi crittografati (RAID-on-crypt)

Dopo aver crittografato il dispositivo o i dispositivi sottostanti, è possibile creare le strutture LVM o RAID sopra a tale livello crittografato. 

I volumi fisici (PV) vengono creati sopra il livello crittografato. I volumi fisici vengono utilizzati per creare il gruppo di volumi. Creare i volumi e aggiungere le voci necessarie in /etc/fstab. 

![Diagramma degli strati delle strutture LVM](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

In modo analogo, il dispositivo RAID viene creato sopra il livello crittografato sui dischi. Un file system viene creato sopra il dispositivo RAID e aggiunto a /etc/fstab come dispositivo normale.

## <a name="considerations"></a>Considerazioni

Si consiglia di utilizzare LVM-on-crypt. RAID è un'opzione quando LVM non può essere utilizzato a causa di specifiche limitazioni dell'applicazione o dell'ambiente.

Si userà l'opzione **EncryptFormatAll.** Per ulteriori informazioni su questa opzione, vedere [Usare la funzionalità EncryptFormatAll per i dischi dati nelle macchine virtuali Linux.](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vms)

Anche se è possibile utilizzare questo metodo quando si sta anche crittografando il sistema operativo, stiamo solo crittografando le unità di dati qui.

Le procedure presuppongono che siano già stati esaminati i prerequisiti negli scenari di Crittografia disco di Azure nelle [macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux) e in Guida [introduttiva: Creare e crittografare una macchina virtuale Linux con l'interfaccia della riga](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart)di comando di Azure.

La versione a doppio passaggio di Crittografia dischi di Azure si trova in un percorso di deprecazione e non deve più essere usata nelle nuove crittografie.

## <a name="general-steps"></a>Passaggi generali

Quando si utilizzano le configurazioni "on-crypt", utilizzare il processo descritto nelle procedure seguenti.

>[!NOTE] 
>Stiamo usando le variabili in tutto l'articolo. Sostituire i valori di conseguenza.

### <a name="deploy-a-vm"></a>Distribuire una macchina virtuale 
I comandi seguenti sono facoltativi, ma è consigliabile applicarli in una macchina virtuale (VM) appena distribuita.

PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Interfaccia della riga di comando di Azure:

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
### <a name="attach-disks-to-the-vm"></a>Collegare i dischi alla macchina virtualeAttach disks to the VM
Ripetere i comandi `$N` seguenti per il numero di nuovi dischi che si desidera collegare alla macchina virtuale.

PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Interfaccia della riga di comando di Azure:

```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Verificare che i dischi siano collegati alla macchina virtualeVerify that the disks are attached to the VM
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Elenco dei dischi collegati in PowerShellList of attached disks in PowerShell](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Interfaccia della riga di comando di Azure:

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Elenco di dischi collegati nell'interfaccia della riga di comando di AzureList of attached disks in the Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portale:

![Elenco dei dischi collegati nel portale](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

Sistema operativo:

```bash
lsblk 
```
![Elenco dei dischi collegati nel sistema operativo](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>Configurare i dischi da crittografare
Questa configurazione viene eseguita a livello di sistema operativo. I dischi corrispondenti sono configurati per una crittografia tradizionale tramite Crittografia disco di Azure:The corresponding disks are configured for a traditional encryption through Azure Disk Encryption:

- I file system vengono creati sopra i dischi.
- I punti di montaggio temporanei vengono creati per montare i file system.
- I file system sono configurati su /etc/fstab per essere montati al momento dell'avvio.

Controllare la lettera del dispositivo assegnata ai nuovi dischi. In questo esempio vengono usati quattro dischi dati.

```bash
lsblk 
```
![Dischi dati collegati al sistema operativo](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Creare un file system sopra ogni disco
Questo comando scorre la creazione di un file system ext4 su ogni disco definito nella parte "in" del ciclo "for".

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Creazione di un file system ext4](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Trovare l'identificatore univoco universale (UUID) dei file system creati di recente, creare una cartella temporanea, aggiungere le voci corrispondenti in /etc/fstab e montare tutti i file system.

Questo comando scorre anche su ogni disco definito nella parte "in" del ciclo "for":

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Verificare che i dischi siano montati correttamente
```bash
lsblk
```
![Elenco dei file system temporanei montati](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Verificare inoltre che i dischi siano configurati:

```bash
cat /etc/fstab
```
![Informazioni di configurazione tramite fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Crittografare i dischi datiEncrypt the data disks
PowerShell che usa una chiave di crittografia della chiave (KEK):

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

Azure CLI using a KEK:

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
![Stato della crittografia in PowerShellEncryption status in PowerShell](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Interfaccia della riga di comando di Azure:

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Stato della crittografia nell'interfaccia della riga di comando di AzureEncryption status in the Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portale:

![Stato della crittografia nel portale](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

Livello del sistema operativo:

```bash
lsblk
```
![Stato della crittografia nel sistema operativo](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

L'estensione aggiungerà i file system a /var/lib/azure_disk_encryption_config/azure_crypt_mount (una vecchia crittografia) o a /etc/crypttab (nuove crittografie).

>[!NOTE] 
>Non modificare nessuno di questi file.

Questo file si occuperà dell'attivazione di questi dischi durante il processo di avvio in modo che LVM o RAID possano utilizzarli in un secondo momento. 

Non preoccuparti dei punti di montaggio su questo file. Crittografia disco di Azure perderà la possibilità di ottenere i dischi montati come un normale file system dopo aver creato un volume fisico o un dispositivo RAID sopra quei dispositivi crittografati. (Questo rimuoverà il formato del file system che abbiamo utilizzato durante il processo di preparazione.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Rimuovere le cartelle temporanee e le voci fstab temporanee
Smontare i file system sui dischi che verranno utilizzati come parte di LVM.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
E rimuovere le voci /etc/fstab:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Verificare che i dischi non siano montati e che le voci in /etc/fstab siano state rimosse

```bash
lsblk
```
![Verifica che i file system temporanei non siano montati](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

E verificare che i dischi siano configurati:
```bash
cat /etc/fstab
```
![Verifica della rimozione delle voci fstab temporanee](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Passaggi per LVM-on-crypt
Ora che i dischi sottostanti sono crittografati, è possibile creare le strutture LVM.

Anziché utilizzare il nome del dispositivo, utilizzare i percorsi /dev/mapper per ognuno dei dischi per creare un volume fisico (sul livello di crittografia sopra il disco, non sul disco stesso).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Configurare LVM sopra i livelli crittografati
#### <a name="create-the-physical-volumes"></a>Creare i volumi fisici
Verrà visualizzato un avviso che chiede se è possibile cancellare la firma del file system. Continuare immettendo **y**o utilizzare **echo "y"** come mostrato:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Verifica della creazione di un volume fisico](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>I nomi /dev/mapper/device devono essere sostituiti per i valori effettivi in base all'output di **lsblk**.

#### <a name="verify-the-information-for-physical-volumes"></a>Verificare le informazioni per i volumi fisici
```bash
pvs
```

![Informazioni per i volumi fisici](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>Creare il gruppo di volumi
Creare il gruppo di volumi utilizzando gli stessi dispositivi già inizializzati:Create the volume group by using the same devices alreadyd:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>Controllare le informazioni per il gruppo di volumi

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Informazioni per il gruppo di volumi](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Creare volumi logici

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>Controllare i volumi logici creati

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Informazioni per i volumi logici](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Creare file system sopra le strutture per i volumi logici

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Creare i punti di montaggio per i nuovi file system

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

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Verificare che i nuovi file system siano montati

```bash
lsblk -fs
df -h
```
![Informazioni per i file system montati](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

In questa variante di **lsblk,** stiamo elencando i dispositivi che mostrano le dipendenze in ordine inverso. Questa opzione consente di identificare i dispositivi raggruppati in base al volume logico anziché ai nomi di periferica /dev/sd[disco] originali.

È importante assicurarsi che l'opzione nofail venga aggiunta alle opzioni del punto di montaggio dei volumi LVM creati in un dispositivo crittografato tramite Crittografia disco di Azure.It's important to make sure that the **nofail** option is added to the mount point options of the LVM volumes created on to a device encrypted through Azure Disk Encryption. Impedisce al sistema operativo di rimanere bloccato durante il processo di avvio (o in modalità di manutenzione).

Se non si utilizza l'opzione **nofail:**

- Il sistema operativo non entrerà mai nella fase in cui viene avviata la crittografia del disco di Azure e i dischi dati vengono sbloccati e montati. 
- I dischi crittografati verranno sbloccati al termine del processo di avvio. I volumi LVM e i file system verranno montati automaticamente fino a quando Azure Disk Encryption non li sbloccherà. 

È possibile verificare il riavvio della macchina virtuale e verificare che anche i file system vengano montati automaticamente dopo l'avvio. Questo processo potrebbe richiedere alcuni minuti, a seconda del numero e delle dimensioni dei file system.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Riavviare la macchina virtuale e verificare dopo il riavvio

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Passaggi per RAID-on-crypt
Ora che i dischi sottostanti sono crittografati, è possibile continuare a creare le strutture RAID. Il processo è lo stesso di quello per LVM, ma invece di usare il nome del dispositivo, usa i percorsi /dev/mapper per ogni disco.

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
![Informazioni per IL RAID configurato tramite il comando mdadm](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>I nomi /dev/mapper/device devono essere sostituiti con i valori effettivi, in base all'output di **lsblk**.

### <a name="checkmonitor-raid-creation"></a>Controllare/monitorare la creazione di RAID
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Stato del RAID](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Creare un file system sopra il nuovo dispositivo RAID
```bash
mkfs.ext4 /dev/md10
```

Creare un nuovo punto di montaggio per il file system, aggiungere il nuovo file system a /etc/fstab e montarlo:

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Verificare che il nuovo file system sia montato:

```bash
lsblk -fs
df -h
```
![Informazioni per i file system montati](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

È importante assicurarsi che l'opzione nofail venga aggiunta alle opzioni del punto di montaggio dei volumi RAID creati in un dispositivo crittografato tramite Crittografia disco di Azure.It's important to make sure that the **nofail** option is added to the mount point options of the RAID volumes created on to a device encrypted through Azure Disk Encryption. Impedisce al sistema operativo di rimanere bloccato durante il processo di avvio (o in modalità di manutenzione).

Se non si utilizza l'opzione **nofail:**

- Il sistema operativo non entrerà mai nella fase in cui viene avviata la crittografia del disco di Azure e i dischi dati vengono sbloccati e montati.
- I dischi crittografati verranno sbloccati al termine del processo di avvio. I volumi RAID e i file system verranno montati automaticamente fino a quando Azure Disk Encryption non li sbloccherà.

È possibile verificare il riavvio della macchina virtuale e verificare che anche i file system vengano montati automaticamente dopo l'avvio. Questo processo potrebbe richiedere alcuni minuti, a seconda del numero e delle dimensioni dei file system.

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

