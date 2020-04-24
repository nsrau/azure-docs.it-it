---
title: Come verificare lo stato della crittografia per Linux
description: Questo articolo fornisce istruzioni su come verificare lo stato della crittografia dalla piattaforma e dal livello del sistema operativo.
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
# <a name="how-to-verify-encryption-status-for-linux"></a>Come verificare lo stato della crittografia per Linux 

**Questo scenario si applica per le estensioni ADE dual-pass e single-pass.**  
Questo ambito del documento consiste nel convalidare lo stato di crittografia di una macchina virtuale usando metodi diversi.

### <a name="environment"></a>Environment

- Distribuzioni di Linux

### <a name="procedure"></a>Procedura

Una macchina virtuale è stata crittografata con un doppio passaggio o un singolo passaggio.

È possibile convalidare lo stato della crittografia durante o dopo la crittografia utilizzando metodi diversi.

>[!NOTE] 
>Le variabili vengono usate in tutto il documento, quindi i valori vengono sostituiti.

### <a name="verification"></a>Verifica

La verifica può essere eseguita dal portale, da PowerShell, da AZ CLI e da o dal lato del sistema operativo della VM. 

Questa verifica può essere eseguita selezionando i dischi collegati a una determinata VM. 

In alternativa, eseguire una query sulle impostazioni di crittografia su ogni singolo disco, indipendentemente dal fatto che il disco sia collegato o non collegato.

Di seguito sono riportati i diversi metodi di convalida:

## <a name="using-the-portal"></a>Uso del portale

Convalidare lo stato della crittografia controllando la sezione Extensions nel portale di Azure.

All'interno della sezione **Extensions** verrà visualizzata l'estensione ade elencata. 

Fare clic su di esso per esaminare il **messaggio di stato**, che indicherà lo stato corrente della crittografia:

![Numero di controllo del portale 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

Nell'elenco delle estensioni verrà visualizzata la versione dell'estensione ADE corrispondente. La versione 0. x corrisponde a ADE dual-pass e la versione 1. x corrisponde a ADE single-pass.

È possibile ottenere altri dettagli facendo clic sull'estensione e quindi su *Visualizza stato dettagliato*.

In formato JSON verrà visualizzato uno stato più dettagliato del processo di crittografia:

![Numero di controllo del portale 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Numero di controllo del portale 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Un altro modo per convalidare lo stato della crittografia è osservare la sezione relativa ai **dischi** .

![Numero di controllo del portale 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Questo stato indica che le impostazioni di crittografia dei dischi sono state contrassegnate ma non che sono state effettivamente crittografate a livello di sistema operativo. Per impostazione predefinita, i dischi vengono contrassegnati per primi e crittografati in un secondo momento. Se il processo di crittografia ha esito negativo, i dischi potrebbero finire con timbri ma non crittografati. Per verificare se i dischi sono effettivamente crittografati, è possibile verificare la crittografia di ogni disco a livello di sistema operativo.

## <a name="using-powershell"></a>Mediante PowerShell

È possibile convalidare lo stato **generale** della crittografia di una VM crittografata usando i comandi di PowerShell seguenti:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![verificare PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

È possibile acquisire le impostazioni di crittografia da ogni singolo disco usando i comandi di PowerShell seguenti:

### <a name="single-pass"></a>Single-Pass
Se un singolo passaggio, le impostazioni di crittografia sono contrassegnate su ciascuno dei dischi (sistema operativo e dati), è possibile acquisire le impostazioni di crittografia del disco del sistema operativo in un unico passaggio, come indicato di seguito:

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
![Verificare il passaggio singolo del sistema operativo](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Se il disco non dispone di impostazioni di crittografia contrassegnate, l'output sarà vuoto, come mostrato di seguito:

![Impostazioni di crittografia del sistema operativo 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Acquisisci impostazioni di crittografia dischi dati:

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
![Verificare i dati singoli PS 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Doppio passaggio
In doppio passaggio, le impostazioni di crittografia vengono contrassegnate nel modello di macchina virtuale e non in ogni singolo disco.

Per verificare che le impostazioni di crittografia siano contrassegnate in due passaggi, è possibile usare i comandi seguenti:

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
![Verificare il passaggio doppio PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Dischi non collegati

Controllare le impostazioni di crittografia per i dischi che non sono collegati a una macchina virtuale.

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
## <a name="using-az-cli"></a>Uso di AZ CLI

È possibile convalidare lo stato **generale** della crittografia di una VM crittografata usando i comandi AZ CLI seguenti:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Verificare generale usando l'interfaccia della riga di comando ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Single-Pass
È possibile convalidare le impostazioni di crittografia da ogni singolo disco usando i comandi AZ CLI seguenti:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Impostazioni di crittografia dei dati](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Se il disco non dispone di impostazioni di crittografia contrassegnate, viene visualizzato come "disco non crittografato".

Informazioni dettagliate sullo stato e le impostazioni di crittografia:

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

![INTERFACCIA della riga di comando dati singola ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Doppio passaggio

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Verificare il doppio generale usando ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) l'interfaccia della riga di comando è anche possibile controllare le impostazioni di crittografia nel profilo di archiviazione del modello di macchina virtuale del disco del sistema operativo

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

![Verificare la doppia del profilo VM con CLI ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Dischi non collegati

Controllare le impostazioni di crittografia per i dischi che non sono collegati a una macchina virtuale.

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

Questo comando elenca tutti gli ID per tutti gli account di archiviazione:

```bash
az storage account list --query [].[id] -o tsv
```
Gli ID dell'account di archiviazione sono elencati nel formato seguente:

ID\<sottoscrizione/subscriptions/> nome\<del gruppo di risorse/ResourceGroups/\<> nome dell'account di archiviazione/Providers/Microsoft.storage/storageaccounts/>

Selezionare l'ID appropriato e archiviarlo in una variabile:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
Stringa di connessione.

Questo comando ottiene la stringa di connessione per un determinato account di archiviazione e la archivia in una variabile:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Nome del contenitore.

Il comando seguente elenca tutti i contenitori in un account di archiviazione:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Il contenitore usato per i dischi è in genere denominato "VHD"

Archiviare il nome del contenitore in una variabile 
```bash
ContainerName="name of the container"
```

Nome del disco.

Usare questo comando per elencare tutti i BLOB in un contenitore specifico
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Scegliere il disco su cui si vuole eseguire la query e archiviarne il nome in una variabile.
```bash
DiskName="diskname.vhd"
```
Eseguire una query sulle impostazioni di crittografia del disco
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Dal sistema operativo
Verificare se le partizioni del disco dati sono crittografate (e il disco del sistema operativo non lo è)

Quando una partizione o un disco viene crittografato, viene visualizzato **come tipo di crittografia,** quando non è crittografato viene visualizzato come **parte/** tipo di disco

``` bash
lsblk
```

![Livello di crittografia del sistema operativo ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

È possibile ottenere ulteriori dettagli utilizzando la seguente variante "lsblk". 

Verrà visualizzato un livello di tipo **crypt** montato dall'estensione.

Nell'esempio seguente vengono illustrati i volumi logici e i dischi normali con "**\_Crypto LUKS FSTYPE**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Sistema operativo Crypt Layer 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Come passaggio aggiuntivo, è anche possibile verificare se nel disco dati sono state caricate chiavi

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

E quali dispositivi DM sono elencati come Crypt

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Passaggi successivi

- [Guida alla risoluzione dei problemi di crittografia dischi di Azure](disk-encryption-troubleshooting.md)
