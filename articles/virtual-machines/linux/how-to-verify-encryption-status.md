---
title: Come verificare lo stato di crittografia per Linux
description: In questo articolo vengono fornite istruzioni sulla verifica dello stato di crittografia dal livello di piattaforma e sistema operativo.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123430"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Come verificare lo stato di crittografia per Linux 

**Questo scenario si applica alle estensioni ADE a doppio passaggio e a passaggio singolo.**  
Questo ambito del documento consiste nel convalidare lo stato di crittografia di una macchina virtuale utilizzando metodi diversi.

### <a name="environment"></a>Environment

- Distribuzioni di Linux

### <a name="procedure"></a>Procedura

Una macchina virtuale è stata crittografata usando il doppio passaggio o il passaggio singolo.

Lo stato della crittografia può essere convalidato durante o dopo la crittografia utilizzando metodi diversi.

>[!NOTE] 
>Stiamo usando le variabili in tutto il documento, sostituire i valori di conseguenza.

### <a name="verification"></a>Verifica

La verifica può essere eseguita dal portale, PowerShell, l'interfaccia della riga di comando e, o dal lato del sistema operativo della macchina virtuale. 

Questa verifica può essere eseguita controllando i dischi collegati a una particolare macchina virtuale. 

Oppure, interrogando le impostazioni di crittografia su ogni singolo disco, indipendentemente dal fatto che il disco sia collegato o non collegato.

Di seguito i diversi metodi di convalida:

## <a name="using-the-portal"></a>Uso del portale

Convalidare lo stato della crittografia controllando la sezione delle estensioni nel portale di Azure.Validate the encryption status by checking the extensions section on the Azure portal.

All'interno della sezione **Estensioni,** vedrai l'estensione ADE elencata. 

Fare clic su di esso e dare un'occhiata al messaggio di **stato**, indicherà lo stato di crittografia corrente:

![Numero di assegno del portale 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

Nell'elenco delle estensioni, vedrai la versione dell'estensione ADE corrispondente. La versione 0.x corrisponde a ADE Dual-Pass e la versione 1.x corrisponde ad ADE Single-Pass.

È possibile ottenere ulteriori dettagli facendo clic sull'estensione e quindi su *Visualizza stato dettagliato*.

Vedrai uno stato più dettagliato del processo di crittografia in formato json:

![Numero di assegno del portale 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Controllo portale numero 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Un altro modo per convalidare lo stato di crittografia consiste nell'esaminare la sezione **Dischi.**

![Numero di assegno del portale 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Questo stato significa che i dischi hanno impostazioni di crittografia contrassegnate, ma non che sono stati effettivamente crittografati a livello di sistema operativo. Per impostazione specifica, i dischi vengono timbrati per primi e crittografati in un secondo momento. Se il processo di crittografia non riesce, i dischi potrebbero finire timbrati ma non crittografati. Per verificare se i dischi sono veramente crittografati, è possibile controllare la crittografia di ogni disco a livello di sistema operativo.

## <a name="using-powershell"></a>Utilizzo di PowerShell

È possibile convalidare lo stato di crittografia generale di una macchina virtuale crittografata usando i comandi di PowerShell seguenti:You can validate the **general** encryption status of an encrypted VM using the following PowerShell commands:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![controllare PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

È possibile acquisire le impostazioni di crittografia da ogni singolo disco usando i comandi di PowerShell seguenti:You can capture the encryption settings from each individual disk using the following PowerShell commands:

### <a name="single-pass"></a>Passaggio singolo
Se a passaggio singolo, le impostazioni di crittografia sono contrassegnate su ciascuno dei dischi (OS e dati), è possibile acquisire le impostazioni di crittografia del disco del sistema operativo in un unico passaggio come segue:

``` powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Verificare il sistema operativo Passaggio singolo 01Verify OS Single pass 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Se sul disco non sono state contrassegnate le impostazioni di crittografia, l'output sarà vuoto come illustrato di seguito:

![Impostazioni crittografia sistema operativo 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Impostazioni di crittografia dei dischi dati:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Verificare i dati singoli ps 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Doppio passaggio
In Dual Pass, le impostazioni di crittografia sono contrassegnate nel modello VM e non su ogni singolo disco.

Per verificare che le impostazioni di crittografia siano state contrassegnate nel doppio passaggio, è possibile utilizzare i seguenti comandi:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Verificare il passaggio doppio PowerShell 1Verify dual pass PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Dischi non collegati

Controllare le impostazioni di crittografia per i dischi non collegati a una macchina virtuale.

### <a name="managed-disks"></a>Dischi gestiti
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="using-az-cli"></a>Utilizzo dell'interfaccia della riga di comando di A

È possibile convalidare lo stato di crittografia generale di una macchina virtuale crittografata usando i comandi seguenti dell'interfaccia della riga di comando:You can validate the **general** encryption status of an encrypted VM using the following A -CLI commands:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Verificare generale utilizzando l'interfaccia della riga di comando ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Passaggio singolo
È possibile convalidare le impostazioni di crittografia da ogni singolo disco utilizzando i seguenti comandi dell'interfaccia della riga di comando:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Impostazioni di crittografia dei dati](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Nel caso in cui il disco non dispone di impostazioni di crittografia contrassegnate, verrà visualizzato come "Disco non crittografato"

Impostazioni dettagliate di stato e crittografia:

Disco del sistema operativo:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![OSSingleCLI](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Dischi dati:

```bash
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Interfaccia della riga di comando singola dei dati ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Doppio passaggio

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Verificare il ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) doppio generale usando l'interfaccia della riga di comando È anche possibile controllare le impostazioni di crittografia nel profilo di archiviazione del modello DI macchina virtuale del disco del sistema operativo:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Verificare il profilo della macchina virtuale duale tramite l'interfaccia della riga di comandoVerify vm ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Dischi non collegati

Controllare le impostazioni di crittografia per i dischi non collegati a una macchina virtuale.

### <a name="managed-disks"></a>Dischi gestiti

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Dischi non gestiti

I dischi non gestiti sono file disco rigido virtuale che sono archiviati come BLOB di pagine negli account di archiviazione di Azure.

Per ottenere i dettagli di un disco specifico, è necessario fornire:

ID dell'account di archiviazione che contiene il disco.
Stringa di connessione per l'account di archiviazione specifico.
Nome del contenitore in cui è archiviato il disco.
Nome del disco.

Questo comando elenca tutti gli URL per tutti gli account di archiviazione:This command lists all the IDs for all your storage accounts:

```bash
az storage account list --query [].[id] -o tsv
```
Gli ID dell'account di archiviazione sono elencati nel formato seguente:The storage account IDs are listed in the following form:

/subscriptions/\<ID sottoscrizione>/gruppidi risorse/nome\<del gruppo di risorse\<>/providers/Microsoft.Storage/storageAccounts/nome account di archiviazione>

Selezionare l'ID appropriato e memorizzarlo in una variabile:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
Stringa di connessione.

Questo comando ottiene la stringa di connessione per un account di archiviazione specifico e la archivia in una variabile:This command gets the connection string for one particular storage account and stores it on a variable:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Nome del contenitore.

Il comando seguente elenca tutti i contenitori in un account di archiviazione:The following command lists all the containers under a storage account:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Il contenitore utilizzato per i dischi è normalmente denominato "vhds"

Archiviare il nome del contenitore in una variabile 
```bash
ContainerName="name of the container"
```

Nome del disco.

Usare questo comando per elencare tutti i BLOB in un contenitore specificoUse this command to list all the blobs on a particular container
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Scegliere il disco su cui si desidera eseguire una query e memorizzarne il nome in una variabile.
```bash
DiskName="diskname.vhd"
```
Eseguire query sulle impostazioni di crittografia del disco
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Dal sistema operativo
Verificare se le partizioni del disco dati sono crittografate (e il disco del sistema operativo non lo è)

Quando una partizione/disco è crittografato viene visualizzato come tipo di **cripta,** quando non è crittografato viene visualizzato come **parte/tipo di disco**

``` bash
lsblk
```

![Livello Cripta di Os ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

È possibile ottenere ulteriori dettagli utilizzando la seguente variante "lsblk". 

Vedrai un livello di tipo **cripta** montato dall'estensione.

L'esempio seguente mostra i volumi logici e i dischi normali con un "**LUKS\_FSTYPE crittografico**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Livello Cripta di Os 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Come passaggio aggiuntivo, è anche possibile verificare se sul disco dati sono caricate chiavi

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

E quali dispositivi dm sono elencati come cripta

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Passaggi successivi

- [Guida alla risoluzione dei problemi di crittografia dischi di Azure](disk-encryption-troubleshooting.md)
