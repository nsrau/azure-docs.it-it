---
title: Verificare lo stato di crittografia per Linux - Crittografia dischi di Azure
description: Questo articolo contiene istruzioni su come verificare lo stato di crittografia dai livelli della piattaforma e del sistema operativo.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: e2916a71f167c415f6bf1dde8ff82a38b0e0557c
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873992"
---
# <a name="verify-encryption-status-for-linux"></a>Verificare lo stato di crittografia per Linux 

L'ambito di questo articolo è la convalida dello stato di crittografia di una macchina virtuale usando vari metodi: il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o il sistema operativo della macchina virtuale. 

È possibile convalidare lo stato di crittografia durante o dopo la crittografia, eseguendo una delle operazioni seguenti:

- Controllo dei dischi collegati a una specifica macchina virtuale. 
- Esecuzione di query sulle impostazioni di crittografia in ogni disco, collegato o meno.

Questo scenario si applica alle estensioni di Crittografia dischi di Azure a singolo passaggio o a due passaggi. Le distribuzioni di Linux sono l'unico ambiente per questo scenario.

>[!NOTE] 
>Nell'intero articolo vengono usate variabili. Sostituire i valori di conseguenza.

## <a name="portal"></a>Portale

Nella sezione **Estensioni** del portale di Azure selezionare l'estensione Crittografia dischi di Azure nell'elenco. Le informazioni **Messaggio di stato** indicano lo stato di crittografia corrente:

![Verifica nel portale con stato, versione e messaggio di stato evidenziati](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

Nell'elenco delle estensioni si vedrà la versione dell'estensione Crittografia dischi di Azure corrispondente. La versione 0.x corrisponde a Crittografia dischi di Azure in due passaggi e la versione 1.x corrisponde a Crittografia dischi di Azure in un singolo passaggio.

È possibile ottenere altri dettagli selezionando l'estensione e quindi **Visualizza stato dettagliato**. Comparirà lo stato dettagliato del processo di crittografia in formato JSON.

![Verifica nel portale con il collegamento "Visualizza stato dettagliato" evidenziato](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Stato dettagliato in formato JSON](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Un altro modo per convalidare lo stato di crittografia è esaminare la sezione **Impostazioni disco**.

![Stato di crittografia per il disco del sistema operativo e i dischi dati](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Questo stato indica che i dischi sono contrassegnati con impostazioni di crittografia, non che sono stati effettivamente crittografati a livello di sistema operativo.
>
> Per impostazione predefinita, i dischi vengono prima contrassegnati e in un secondo momento vengono crittografati. Se il processo di crittografia ha esito negativo, i dischi potrebbero risultare contrassegnati, ma non crittografati. 
>
> Per verificare se i dischi sono effettivamente crittografati, è possibile verificare la crittografia di ogni disco a livello del sistema operativo.

## <a name="powershell"></a>PowerShell

Si può convalidare lo stato di crittografia *generale* di una macchina virtuale crittografata usando i comandi di PowerShell seguenti:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![Stato di crittografia generale in PowerShell](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

È possibile acquisire le impostazioni di crittografia da ogni disco usando i comandi di PowerShell seguenti.

### <a name="single-pass"></a>Singolo passaggio
Se si esegue un solo passaggio, le impostazioni di crittografia vengono indicate su entrambi i dischi (sistema operativo e dati). È possibile acquisire le impostazioni di crittografia per un disco del sistema operativo in un singolo passaggio come indicato di seguito:

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
![Impostazioni di crittografia per un disco del sistema operativo](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Se il disco non è contrassegnato con impostazioni di crittografia, l'output sarà vuoto:

![Output vuoto](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Usare i comandi seguenti per acquisire le impostazioni di crittografia per i dischi dati:

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
![Impostazioni di crittografia per i dischi dati](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Due passaggi
Nel caso dei due passaggi, le impostazioni di crittografia vengono indicate nel modello di macchina virtuale e non in ogni singolo disco.

Per verificare che le impostazioni di crittografia siano state contrassegnate in due passaggi, usare i comandi seguenti:

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
![Impostazioni di crittografia in due passaggi](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

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
## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Si può convalidare lo stato di crittografia *generale* di una macchina virtuale crittografata usando i comandi dell'interfaccia della riga di comando di Azure seguenti:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Stato di crittografia generale dall'interfaccia della riga di comando di Azure ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Singolo passaggio
È possibile convalidare le impostazioni di crittografia per ogni disco usando i comandi dell'interfaccia della riga di comando di Azure seguenti:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Impostazioni di crittografia dei dati](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Se il disco non presenta impostazioni di crittografia, verrà visualizzato il testo **Il disco non è crittografato**.

Usare i comandi seguenti per ottenere informazioni dettagliate sullo stato e le impostazioni di crittografia.

Disco sistema operativo:

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

![Informazioni dettagliate sullo stato e le impostazioni di crittografia per il disco del sistema operativo](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

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

![Informazioni dettagliate sullo stato e le impostazioni di crittografia per i dischi dati](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Due passaggi

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Impostazioni di crittografia generali per i due passaggi tramite l'interfaccia della riga di comando di Azure](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)

È anche possibile controllare le impostazioni di crittografia nel profilo di archiviazione del modello di macchina virtuale del disco del sistema operativo:

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

![Profilo della macchina virtuale per i due passaggi tramite l'interfaccia della riga di comando di Azure](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

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

- ID dell'account di archiviazione che contiene il disco.
- Stringa di connessione per quello specifico account di archiviazione.
- Nome del contenitore in cui è archiviato il disco.
- Nome del disco.

Questo comando elenca tutti gli ID per tutti gli account di archiviazione:

```bash
az storage account list --query [].[id] -o tsv
```
Gli ID degli account di archiviazione sono elencati nel formato seguente:

/subscriptions/\<ID sottoscrizione>/resourceGroups/\<nome gruppo di risorse>/providers/Microsoft.Storage/storageAccounts/\<nome account archiviazione>

Selezionare l'ID appropriato e archiviarlo in una variabile:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```

Questo comando ottiene la stringa di connessione per un determinato account di archiviazione e la archivia in una variabile:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Il comando seguente elenca tutti i contenitori in un account di archiviazione:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Il contenitore usato per i dischi è in genere denominato "vhds".

Archiviare il nome del contenitore in una variabile: 
```bash
ContainerName="name of the container"
```

Usare questo comando per elencare tutti i BLOB in uno specifico contenitore:
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Scegliere il disco su cui si vuole eseguire la query e archiviarne il nome in una variabile:
```bash
DiskName="diskname.vhd"
```
Eseguire la query sulle impostazioni di crittografia del disco:
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="operating-system"></a>Sistema operativo
Verificare se le partizioni del disco dati sono crittografate e il disco del sistema operativo non lo è.

Quando una partizione o un disco è crittografato, il tipo visualizzato è **crypt**. Quando non è crittografato, viene visualizzato il tipo **part/disk**.

``` bash
lsblk
```

![Livello di crittografia del sistema operativo per una partizione](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Per ottenere maggiori dettagli si può usare la variante di **lsblk** seguente. 

Si vedrà un livello di tipo **crypt** montato dall'estensione. L'esempio seguente mostra volumi logici e dischi normali con **crypto\_LUKS FSTYPE**.

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Livello di crittografia del sistema operativo per volumi logici e dischi normali](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Come passaggio aggiuntivo, è possibile verificare se nel disco dati sono caricate chiavi:

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

È anche possibile verificare quali dispositivi **DM** sono elencati come **crypt**:

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Passaggi successivi

- [Guida alla risoluzione dei problemi di crittografia dischi di Azure](disk-encryption-troubleshooting.md)
