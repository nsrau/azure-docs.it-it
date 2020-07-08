---
title: Configurare LVM e RAID nei dispositivi crittografati-crittografia dischi di Azure
description: Questo articolo fornisce le istruzioni per la configurazione di LVM e RAID nei dispositivi crittografati per le macchine virtuali Linux.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 4e342ff44af38b8e79dc8695c1270b1f5c68e0a8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80657436"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Configurare LVM e RAID nei dispositivi crittografati

Questo articolo è una procedura dettagliata per eseguire operazioni di gestione dei volumi logici (LVM) e RAID nei dispositivi crittografati. Il processo si applica agli ambienti seguenti:

- Distribuzioni di Linux
    - RHEL 7.6 +
    - Ubuntu 18.04 +
    - SUSE 12 +
- Estensione a singolo passaggio di crittografia dischi di Azure
- Estensione dual-pass di crittografia dischi di Azure


## <a name="scenarios"></a>Scenari

Le procedure descritte in questo articolo supportano gli scenari seguenti:  

- Configurare LVM su dispositivi crittografati (LVM-on-crypt)
- Configurare RAID su dispositivi crittografati (RAID-on-crypt)

Dopo la crittografia del dispositivo o dei dispositivi sottostanti, è possibile creare le strutture LVM o RAID al di sopra del livello crittografato. 

I volumi fisici (PVs) vengono creati sopra il livello crittografato. I volumi fisici vengono utilizzati per creare il gruppo di volumi. È possibile creare i volumi e aggiungere le voci necessarie in/etc/fstab. 

![Diagramma dei livelli delle strutture LVM](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

In modo analogo, il dispositivo RAID viene creato sopra il livello crittografato sui dischi. Viene creata una file system sulla periferica RAID e aggiunta a/etc/fstab come dispositivo normale.

## <a name="considerations"></a>Considerazioni

Si consiglia di usare LVM-on-crypt. RAID è un'opzione in cui non è possibile usare LVM a causa di limitazioni specifiche dell'applicazione o dell'ambiente.

Usare l'opzione **EncryptFormatAll** . Per altre informazioni su questa opzione, vedere [usare la funzionalità EncryptFormatAll per i dischi dati nelle macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vms).

Anche se è possibile usare questo metodo quando si esegue la crittografia del sistema operativo, è sufficiente crittografare le unità dati qui.

Nelle procedure si presuppone che siano già stati esaminati i prerequisiti negli [scenari di crittografia dischi di Azure nelle VM Linux](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux) e in [Guida introduttiva: creare e crittografare una VM Linux con l'interfaccia della](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart)riga di comando di Azure.

La versione dual-pass di crittografia dischi di Azure si trova in un percorso di deprecazione e non deve più essere usata con nuove crittografie.

## <a name="general-steps"></a>Passaggi generali

Quando si usano le configurazioni "on-crypt", usare il processo descritto nelle procedure riportate di seguito.

>[!NOTE] 
>Nell'intero articolo vengono usate variabili. Sostituire i valori di conseguenza.

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
### <a name="attach-disks-to-the-vm"></a>Connetti i dischi alla macchina virtuale
Ripetere i comandi seguenti per `$N` il numero di nuovi dischi che si desidera aggiungere alla macchina virtuale.

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

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Verificare che i dischi siano collegati alla macchina virtuale
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Elenco di dischi collegati in PowerShell](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Interfaccia della riga di comando di Azure:

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Elenco di dischi collegati nell'interfaccia della riga di comando di Azure](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portale:

![Elenco di dischi collegati nel portale](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

Sistema operativo:

```bash
lsblk 
```
![Elenco di dischi collegati nel sistema operativo](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>Configurare i dischi da crittografare
Questa configurazione viene eseguita a livello di sistema operativo. I dischi corrispondenti sono configurati per una crittografia tradizionale tramite crittografia dischi di Azure:

- I file System vengono creati sopra i dischi.
- Per montare i file System vengono creati punti di montaggio temporanei.
- I file System sono configurati in/etc/fstab per essere montati in fase di avvio.

Controllare la lettera del dispositivo assegnata ai nuovi dischi. In questo esempio vengono utilizzati quattro dischi dati.

```bash
lsblk 
```
![Dischi dati collegati al sistema operativo](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Creare un file system su ogni disco
Questo comando esegue l'iterazione della creazione di un file system ext4 in ogni disco definito nella parte "in" del ciclo "for".

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Creazione di un file system ext4](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Trovare l'identificatore univoco universale (UUID) dei file System creati di recente, creare una cartella temporanea, aggiungere le voci corrispondenti in/etc/fstab e montare tutti i file System.

Questo comando esegue anche l'iterazione su ogni disco definito nella parte "in" del ciclo "for":

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
![Elenco di file system temporanei montati](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Verificare inoltre che i dischi siano configurati:

```bash
cat /etc/fstab
```
![Informazioni di configurazione tramite fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Crittografare i dischi dati
PowerShell con una chiave di crittografia della chiave (KEK):

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

INTERFACCIA della riga di comando di Azure con KEK:

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
![Stato della crittografia in PowerShell](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Interfaccia della riga di comando di Azure:

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Stato della crittografia nell'interfaccia della riga di comando di Azure](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portale:

![Stato della crittografia nel portale](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

Livello sistema operativo:

```bash
lsblk
```
![Stato della crittografia nel sistema operativo](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

L'estensione consente di aggiungere i file System a/var/lib/azure_disk_encryption_config/azure_crypt_mount (una crittografia precedente) o a/etc/crypttab (nuove crittografie).

>[!NOTE] 
>Non modificare nessuno di questi file.

Questo file si occuperà di attivare questi dischi durante il processo di avvio in modo che LVM o RAID possa usarli in un secondo momento. 

Non preoccuparti dei punti di montaggio in questo file. Crittografia dischi di Azure perderà la possibilità di montare i dischi come un normale file system dopo aver creato un volume fisico o un dispositivo RAID sopra i dispositivi crittografati. Questo eliminerà il formato file system usato durante il processo di preparazione.

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Rimuovere le cartelle temporanee e le voci fstab temporanee
Si smontano i file System sui dischi che verranno usati come parte di LVM.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
E rimuovere le voci/etc/fstab:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Verificare che i dischi non siano montati e che le voci in/etc/fstab siano state rimosse

```bash
lsblk
```
![Verifica della disinstallazione dei file system temporanei](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

E verificare che i dischi siano configurati:
```bash
cat /etc/fstab
```
![Verifica della rimozione delle voci fstab temporanee](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Passaggi per LVM-on-crypt
Ora che i dischi sottostanti sono crittografati, è possibile creare le strutture LVM.

Invece di usare il nome del dispositivo, usare i percorsi/dev/mapper per ciascuno dei dischi per creare un volume fisico (sul livello Crypt sulla parte superiore del disco, non sul disco).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Configurare LVM oltre i livelli crittografati
#### <a name="create-the-physical-volumes"></a>Creare i volumi fisici
Verrà visualizzato un avviso che chiede se è OK eliminare la firma del file system. Per continuare, immettere **y**oppure usare **echo "y"** , come illustrato di seguito:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Verifica della creazione di un volume fisico](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>È necessario sostituire i nomi di/dev/mapper/Device per i valori effettivi in base all'output di **lsblk**.

#### <a name="verify-the-information-for-physical-volumes"></a>Verificare le informazioni per i volumi fisici
```bash
pvs
```

![Informazioni per i volumi fisici](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>Creare il gruppo di volumi
Creare il gruppo di volumi usando gli stessi dispositivi già inizializzati:

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

#### <a name="create-logical-volumes"></a>Creazione di volumi logici

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

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Creare file System sopra le strutture per i volumi logici

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Creare i punti di montaggio per i nuovi file System

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Aggiungere i nuovi file System a/etc/fstab e montarli

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Verificare che i nuovi file System siano montati

```bash
lsblk -fs
df -h
```
![Informazioni per i file system montati](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

In questa variante di **lsblk**sono elencati i dispositivi che mostrano le dipendenze in ordine inverso. Questa opzione consente di identificare i dispositivi raggruppati in base al volume logico anziché ai nomi dei dispositivi/dev/sd [disco] originali.

È importante assicurarsi che l'opzione **nofail** venga aggiunta alle opzioni del punto di montaggio dei volumi LVM creati sopra un dispositivo crittografato tramite crittografia dischi di Azure. Impedisce al sistema operativo di rimanere bloccati durante il processo di avvio o in modalità di manutenzione.

Se non si usa l'opzione **nofail** :

- Il sistema operativo non sarà mai in fase di avvio di crittografia dischi di Azure e i dischi dati vengono sbloccati e montati. 
- I dischi crittografati verranno sbloccati alla fine del processo di avvio. I volumi e i file system LVM verranno montati automaticamente finché la crittografia dischi di Azure non li sblocca. 

È possibile testare il riavvio della macchina virtuale e verificare che anche i file System vengano montati automaticamente dopo l'avvio. Questo processo potrebbe richiedere diversi minuti, a seconda del numero e delle dimensioni dei file System.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Riavviare la macchina virtuale e verificare dopo il riavvio

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Passaggi per RAID-on-crypt
Ora che i dischi sottostanti sono crittografati, è possibile continuare a creare le strutture RAID. Il processo è identico a quello per LVM, ma invece di usare il nome del dispositivo, usare i percorsi/dev/mapper per ogni disco.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Configurare RAID in cima al livello crittografato dei dischi
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Informazioni per il RAID configurato tramite il comando mdadm](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>I nomi di/dev/mapper/Device devono essere sostituiti con i valori effettivi, in base all'output di **lsblk**.

### <a name="checkmonitor-raid-creation"></a>Esegui controllo/monitoraggio creazione RAID
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Stato di RAID](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Creare una file system sul nuovo dispositivo RAID
```bash
mkfs.ext4 /dev/md10
```

Creare un nuovo punto di montaggio per il file system, aggiungere la nuova file system a/etc/fstab e montarla:

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

È importante assicurarsi che l'opzione **nofail** venga aggiunta alle opzioni del punto di montaggio dei volumi RAID creati sopra un dispositivo crittografato tramite crittografia dischi di Azure. Impedisce al sistema operativo di rimanere bloccati durante il processo di avvio o in modalità di manutenzione.

Se non si usa l'opzione **nofail** :

- Il sistema operativo non sarà mai in fase di avvio di crittografia dischi di Azure e i dischi dati vengono sbloccati e montati.
- I dischi crittografati verranno sbloccati alla fine del processo di avvio. I volumi RAID e i file System verranno montati automaticamente finché la crittografia dischi di Azure non li sblocca.

È possibile testare il riavvio della macchina virtuale e verificare che anche i file System vengano montati automaticamente dopo l'avvio. Questo processo potrebbe richiedere diversi minuti, a seconda del numero e delle dimensioni dei file System.

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

