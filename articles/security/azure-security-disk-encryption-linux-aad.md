---
title: Crittografia dischi di Azure con un'app Azure AD per macchine virtuali IaaS Linux (versione precedente)
description: Questo articolo offre informazioni sull'abilitazione di Crittografia dischi di Microsoft Azure per le macchine virtuali IaaS Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 1e535ed92305d124499fd0ce9933b7edd19df32e
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286160"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms-previous-release"></a>Abilitare Crittografia dischi di Azure per macchine virtuali IaaS Linux (versione precedente)

**La nuova versione di Crittografia dischi di Azure elimina la necessità di specificare un parametro dell'applicazione Azure AD per abilitare la crittografia dei dischi per le macchine virtuali. Con la nuova versione, non è più necessario specificare le credenziali di Azure AD durante il passaggio di abilitazione della crittografia. Tutte le nuove macchine virtuali devono essere crittografate senza i parametri dell'applicazione Azure AD. Per istruzioni su come abilitare la crittografia dei dischi di macchine virtuali usando la nuova versione, vedere [Crittografia dischi di Azure per macchine virtuali Linux](azure-security-disk-encryption-linux.md). Le macchine virtuali che sono già state crittografate con i parametri dell'applicazione Azure AD sono ancora supportate e dovrebbero continuare a essere gestite con la sintassi di AAD.**

È possibile abilitare molti scenari di crittografia dei dischi e la procedura può variare in base allo scenario. Le sezioni seguenti illustrano in modo più dettagliato gli scenari per le macchine virtuali IaaS Linux. Prima di poter usare la crittografia dischi, è necessario soddisfare i [prerequisiti di Crittografia dischi di Azure](azure-security-disk-encryption-prerequisites-aad.md) e consultare la sezione [Additional prerequisites for Linux IaaS VMs](azure-security-disk-encryption-prerequisites-aad.md#bkmk_LinuxPrereq) (Altri prerequisiti per le macchine virtuali IaaS Linux).

Catturare uno [snapshot](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/o eseguire il backup prima che i dischi vengono crittografati. I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Le macchine virtuali con dischi gestiti richiedono il backup prima della crittografia. Una volta che viene eseguito un backup, è possibile usare il cmdlet Set-AzVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro - skipVmBackup. Per altre informazioni su come eseguire il backup e il ripristino di macchine virtuali crittografate, vedere l'articolo [Backup di Azure](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Se in precedenza è stata usata la [Crittografia dischi di Azure con l'app Azure AD](azure-security-disk-encryption-prerequisites-aad.md) per crittografare la macchina virtuale, sarà necessario continuare a usare questa opzione per crittografare la macchina virtuale. Non è possibile usare [Crittografia dischi di Azure](azure-security-disk-encryption-prerequisites.md) in questa macchina virtuale crittografata perché non è uno scenario supportato, ovvero non è ancora supportato il trasferimento dall'applicazione AAD per questa macchina virtuale.
 > - Per assicurarsi che i segreti di crittografia non superino i confini a livello di area, Crittografia dischi di Azure richiede che l'insieme di credenziali delle chiavi e le macchine virtuali si trovino nella stessa area. Creare e usare un insieme di credenziali delle chiavi nella stessa area della macchina virtuale da crittografare.
 > - La procedura per crittografare i volumi del sistema operativo Linux potrebbe richiedere alcune ore. È normale che i volumi del sistema operativo Linux richiedano più tempo rispetto ai volumi di dati da crittografare.
> - Quando la crittografia dei volumi del sistema operativo Linux, la macchina virtuale deve essere considerata non disponibile. È consigliabile per evitare accessi SSH, mentre la crittografia è in corso per evitare i problemi che bloccano i file aperti che dovranno essere accessibili durante il processo di crittografia. Controllare lo stato, il [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) oppure [show di crittografia della macchina virtuale](/cli/azure/vm/encryption#az-vm-encryption-show) comandi possono essere utilizzati. Questo processo può richiedere alcune ore per un volume di sistema operativo da 30 GB, più un tempo aggiuntivo per la crittografia dei volumi di dati. Il tempo per la crittografia del volume di dati è proporzionale alla dimensione e quantità dei volumi di dati a meno che non venga usata l'opzione "encrypt format all". 
 > - La disabilitazione della crittografia nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportata nei dati o nei volumi del sistema operativo, se il volume del sistema operativo è stato crittografato.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningLinux"> </a> Abilitare la crittografia in una macchina virtuale IaaS Linux esistente o in esecuzione

In questo scenario è possibile abilitare la crittografia usando il modello di Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. 

>[!IMPORTANT]
 >È obbligatorio catturare uno snapshot e/o eseguire il backup di un'istanza di macchina virtuale basata su un disco gestito all'esterno di Crittografia dischi di Azure e prima di abilitarla. È possibile catturare uno snapshot del disco gestito dal portale o tramite [Backup di Azure](../backup/backup-azure-vms-encryption.md). I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Una volta che viene eseguito un backup, il cmdlet Set-AzVMDiskEncryptionExtension è utilizzabile per crittografare i dischi gestiti specificando il parametro - skipVmBackup. Il comando Set-AzVMDiskEncryptionExtension avrà esito negativo nelle macchine virtuali basate su disco gestito fino a quando non è stata eseguita una copia di backup e questo parametro è stato specificato. 
>
>La crittografia o la disabilitazione della crittografia può causare il riavvio della macchina virtuale. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Abilitare la crittografia in una macchina virtuale Linux esistente o in esecuzione usando l'interfaccia della riga di comando di Azure 
È possibile abilitare la crittografia dischi nel disco rigido virtuale crittografato installando e usando lo strumento da riga di comando [Azure CLI 2.0](/cli/azure). È possibile usarlo nel browser con [Azure Cloud Shell](../cloud-shell/overview.md) oppure installarlo nel computer locale e usarlo in una sessione di PowerShell. Per abilitare la crittografia in macchine virtuali IaaS Linux esistenti o in esecuzione in Azure, usare i comandi seguenti dell'interfaccia della riga di comando:

Usare il comando [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure.

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave per eseguire il wrapping del segreto client:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verificare che i dischi siano crittografati:** Per controllare lo stato della crittografia di una VM IaaS, usare il [show di az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-show) comando. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Disabilitare la crittografia:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). È possibile disabilitare la crittografia solo nei volumi di dati per le macchine virtuali Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Abilitare la crittografia in una macchina virtuale Linux esistente o in esecuzione usando PowerShell
Usare la [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure. Richiedere un [snapshot](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/o eseguire il backup della macchina virtuale con [Backup di Azure](../backup/backup-azure-vms-encryption.md) prima che i dischi vengono crittografati. Il parametro - skipVmBackup è già specificato negli script di PowerShell per crittografare una VM Linux in esecuzione.

- **Crittografare una macchina virtuale in esecuzione usando un segreto client:** Lo script seguente consente di inizializzare le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale, l'insieme di credenziali delle chiavi, l'app AAD e il segreto client sono già stati creati per impostazione predefinita. Sostituire con i valori MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret. Modificare il parametro - VolumeType per specificare quali dischi in corso la crittografia.

    ```azurepowershell
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
    ```
- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave per eseguire il wrapping del segreto client:** Crittografia dischi di Azure consente di specificare una chiave esistente nell'insieme di credenziali delle chiavi per eseguire il wrapping dei segreti di crittografia dei dischi generati quando la crittografia è stata abilitata. Quando viene specificata una chiave di crittografia della chiave, Crittografia dischi di Azure la usa per eseguire il wrapping dei segreti di crittografia prima di scrivere nell'insieme di credenziali delle chiavi. Modificare il parametro - VolumeType per specificare quali dischi in corso la crittografia. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > La sintassi per il valore del parametro disco-encryption-Key Vault è la stringa dell'identificatore completo: / subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
  La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Verificare che i dischi siano crittografati:** Per controllare lo stato della crittografia di una VM IaaS, usare il [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Disabilitare la crittografia del disco:** per disabilitare la crittografia, usare il cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). È possibile disabilitare la crittografia solo nei volumi di dati per le macchine virtuali Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Abilitare la crittografia in una macchina virtuale IaaS Linux esistente o in esecuzione con un modello

È possibile abilitare la crittografia dei dischi nelle macchine virtuali IaaS Linux esistenti o in esecuzione usando il modello di [Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Fare clic su **Deploy to Azure** nel modello di avvio rapido di Azure.

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, i parametri, i termini legali e il contratto. Fare clic su **Crea** per abilitare la crittografia in una macchina IaaS esistente o in esecuzione.

La tabella seguente elenca i parametri del modello di Resource Manager per macchine virtuali esistenti o in esecuzione che usano un ID client di Azure AD:

| Parametro | DESCRIZIONE |
| --- | --- |
| AADClientID | ID client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | Segreto client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave. È possibile ottenerlo con il comando dell'interfaccia della riga di comando di Azure `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave generata. Questo parametro è facoltativo se si seleziona **nokek** dall'elenco a discesa UseExistingKek. Se si seleziona **kek** dall'elenco a discesa UseExistingKek, è necessario immettere il valore _keyEncryptionKeyURL_. |
| volumeType | Tipo del volume in cui viene eseguita l'operazione di crittografia. I valori supportati validi sono _OS_ o _All_ (vedere le distribuzioni di Linux supportate e le relative versioni per il sistema operativo e i dischi dati nella sezione precedente sui prerequisiti). |
| sequenceVersion | Versione della sequenza dell'operazione BitLocker. Incrementare questo numero di versione ogni volta che viene eseguita un'operazione di crittografia dei dischi nella stessa VM. |
| vmName | Nome della VM in cui deve essere eseguita l'operazione di crittografia. |
| passphrase | Immettere una passphrase complessa come chiave di crittografia dei dati. |



## <a name="bkmk_EFA"> </a>Usare la funzione EncryptFormatAll per i dischi dati nelle macchine virtuali IaaS Linux
Il parametro **EncryptFormatAll** riduce la durata per la crittografia dei dischi dati Linux. Le partizioni che soddisfano determinati criteri verranno formattate (con il file system corrente). Successivamente verranno rimontate nella posizione in cui si trovavano prima dell'esecuzione del comando. Se si desidera escludere un disco dati che soddisfa i criteri, è possibile smontarlo prima di eseguire il comando.

 Dopo aver eseguito questo comando, tutti i dischi precedentemente montati verranno formattati. In seguito verrà avviato il livello di crittografia sul disco ora vuoto. Quando questa opzione è selezionata, viene crittografato anche il disco risorse temporaneo collegato alla macchina virtuale. Se l'unità temporanea viene reimpostata, viene riformattata e crittografata di nuovo per la macchina virtuale dalla soluzione Crittografia dischi di Azure alla successiva opportunità.

>[!WARNING]
> EncryptFormatAll non deve essere usato quando i volumi di dati di una macchina virtuale contengono dati necessari. Per escludere i dischi dalla crittografia, è possibile smontarli. È innanzitutto necessario provare EncryptFormatAll in una macchina virtuale di test, poi comprendere il parametro della funzione e la sua implicazione prima di provarlo nella macchina virtuale di produzione. L'opzione EncryptFormatAll formatta il disco dati e tutti i dati che contiene andranno persi. Prima di procedere, verificare che i dischi da escludere siano smontati correttamente. </br></br>
 >Se si imposta questo parametro durante l'aggiornamento delle impostazioni di crittografia, si potrebbe verificare un riavvio prima che la crittografia venga effettivamente eseguita. In questo caso, è anche possibile rimuovere dal file fstab il disco da non formattare. Analogamente, è necessario aggiungere al file fstab la partizione che si desidera formattare con crittografia e poi avviare l'operazione di crittografia. 

### <a name="bkmk_EFACriteria"> </a> Criteri EncryptFormatAll
Il parametro esamina tutte le partizioni e le crittografa, a patto che soddisfino **tutti** i criteri seguenti: 
- Non è una partizione di avvio/del sistema operativo/radice
- Non è già stata crittografata
- Non è un volume BEK
- Non è un volume RAID
- Non è un volume LVM
- È montata

Crittografare i dischi che compongono il volume RAID o LVM anziché il volume stesso.

### <a name="bkmk_EFATemplate"> </a> Usare il parametro EncryptFormatAll con un modello
Per usare l'opzione EncryptFormatAll, usare qualsiasi modello di Azure Resource Manager preesistente che crittografa una macchina virtuale Linux e modificare il campo **EncryptionOperation** per la risorsa AzureDiskEncryption.

1. Ad esempio, usare il [modello di Resource Manager per crittografare una macchina virtuale IaaS Linux in esecuzione](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Fare clic su **Deploy to Azure** nel modello di avvio rapido di Azure.
3. Modificare **EncryptionOperation** da **EnableEncryption** a **EnableEncryptionFormatAl**
4. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, gli altri parametri, i termini legali e il contratto. Fare clic su **Crea** per abilitare la crittografia in una macchina IaaS esistente o in esecuzione.


### <a name="bkmk_EFAPSH"> </a> Usare il parametro EncryptFormatAll con un cmdlet di PowerShell
Usare la [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet con il `EncryptFormatAll` parametro.

**Crittografare una macchina virtuale in esecuzione usando un segreto client e EncryptFormatAll:** Ad esempio, lo script seguente consente di inizializzare le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension con il parametro EncryptFormatAll. Il gruppo di risorse, la macchina virtuale, l'insieme di credenziali delle chiavi, l'app AAD e il segreto client sono già stati creati per impostazione predefinita. Sostituire con i valori MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"> </a> Usare il parametro EncryptFormatAll con Logical Volume Manager (LVM) 
È consigliabile usare una configurazione LVM-on-crypt. Per tutti gli esempi seguenti, sostituire il percorso del dispositivo e i punti di montaggio con qualunque elemento sia appropriato al caso d'uso. La configurazione può essere eseguita nel seguente modo:

- Aggiungere i dischi dati che compongono la macchina virtuale.
- Formattare, montare e aggiungere i dischi al file fstab.

    1. Formattare il disco appena aggiunto. Qui vengono usati i collegamenti simbolici generati da Azure. L'uso di collegamenti simbolici evita i problemi legati alla modifica dei nomi dei dispositivi. Per altre informazioni, vedere l'articolo [Risolvere il problema dei nomi di dispositivo](../virtual-machines/linux/troubleshoot-device-names-problems.md).
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Montare i dischi.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Aggiungerli al file fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Eseguire il cmdlet di PowerShell Set-AzVMDiskEncryptionExtension con - EncryptFormatAll per crittografare i dischi.
         ```azurepowershell-interactive
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Configurare LVM sui nuovi dischi. Si noti che le unità crittografate vengono sbloccate dopo il termine dell'avvio della macchina virtuale. Pertanto, anche il montaggio di LVM dovrà essere posticipato.




## <a name="bkmk_VHDpre"> </a> Nuove macchine virtuali IaaS create da chiavi di crittografia e disco rigido virtuale crittografato dal cliente
In questo scenario è possibile abilitare la crittografia usando il modello di Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti descrivono in modo più dettagliato il modello di Resource Manager e i comandi dell'interfaccia della riga di comando. 

Usare le istruzioni nell'appendice per la preparazione delle immagini pre-crittografate che possono essere usate in Azure. Dopo aver creato l'immagine, è possibile usare i passaggi della sezione successiva per creare una VM di Azure crittografata.

* [Preparare un disco rigido virtuale Windows pre-crittografato](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Preparare un disco rigido virtuale Linux pre-crittografato](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >È obbligatorio catturare uno snapshot e/o eseguire il backup di un'istanza di macchina virtuale basata su un disco gestito all'esterno di Crittografia dischi di Azure e prima di abilitarla. È possibile catturare uno snapshot del disco gestito dal portale o tramite [Backup di Azure](../backup/backup-azure-vms-encryption.md). I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Una volta che viene eseguito un backup, il cmdlet Set-AzVMDiskEncryptionExtension è utilizzabile per crittografare i dischi gestiti specificando il parametro - skipVmBackup. Il comando Set-AzVMDiskEncryptionExtension avrà esito negativo nelle macchine virtuali basate su disco gestito fino a quando non è stata eseguita una copia di backup e questo parametro è stato specificato. 
>
>La crittografia o la disabilitazione della crittografia può causare il riavvio della macchina virtuale. 



### <a name="bkmk_VHDprePSH"> </a> Usare Azure PowerShell per crittografare le macchine virtuali IaaS con dischi rigidi virtuali pre-crittografati 
È possibile abilitare crittografia del disco nel disco rigido virtuale crittografato usando il cmdlet di PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). L'esempio seguente offre alcuni parametri comuni. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Abilitare la crittografia in un disco dati appena aggiunto
È possibile aggiungere un nuovo disco dati usando [az vm disk attach](../virtual-machines/linux/add-disk.md) o [tramite il portale di Azure](../virtual-machines/linux/attach-disk-portal.md). Prima che sia possibile eseguire la crittografia, è necessario montare il disco appena collegato. È necessario richiedere la crittografia del disco dati poiché l'unità non sarà utilizzabile mentre la crittografia è in corso. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Abilitare la crittografia in un disco appena aggiunto con l'interfaccia della riga di comando di Azure
 Se la macchina virtuale è stata precedentemente crittografata con "All", il parametro --volume-type deve rimanere All. Tale parametro include il disco del sistema operativo e il disco dati. Se la macchina virtuale è stata precedentemente crittografata con il tipo di volume "OS", impostare il parametro --volume-type su All, in modo che vengano inclusi sia il disco del sistema operativo che il nuovo disco dati. Se la macchina virtuale è stata crittografata solo con il tipo di volume "Data", è possibile lasciare invariato il parametro "Data", come illustrato di seguito. L'aggiunta e il collegamento di un nuovo disco dati a una macchina virtuale non sono condizioni sufficienti alla preparazione per la crittografia. Il disco appena collegato deve anche essere formattato e montato in modo corretto nella macchina virtuale prima dell'abilitazione della crittografia. In Linux il disco deve essere montato in /etc/fstab con un [nome del dispositivo a blocchi permanente](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

A differenza della sintassi di PowerShell, l'interfaccia della riga di comando non richiede all'utente di specificare una versione di sequenza univoca quando si abilita la crittografia. L'interfaccia della riga di comando genera e usa automaticamente uno specifico valore di versione di sequenza univoco.

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:** 

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave per eseguire il wrapping del segreto client:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Abilitare la crittografia in un disco appena aggiunto con Azure PowerShell
 Quando si usa PowerShell per crittografare un nuovo disco per Linux, è necessario specificare una nuova versione della sequenza. La versione della sequenza deve essere univoca. Lo script seguente genera un GUID per la versione della sequenza. 
 

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:** Lo script seguente consente di inizializzare le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale, l'insieme di credenziali delle chiavi, l'app AAD e il segreto client sono già stati creati per impostazione predefinita. Sostituire con i valori MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret. Il parametro -VolumeType è impostato sui dischi dati e non sul disco del sistema operativo. Se la macchina virtuale è stata precedentemente crittografata con un tipo di volume "OS" o "All", il parametro -VolumeType deve essere modificato in All, in modo che vengano inclusi sia il disco del sistema operativo sia il nuovo disco dati.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave per eseguire il wrapping del segreto client:** Crittografia dischi di Azure consente di specificare una chiave esistente nell'insieme di credenziali delle chiavi per eseguire il wrapping dei segreti di crittografia dei dischi generati quando la crittografia è stata abilitata. Quando viene specificata una chiave di crittografia della chiave, Crittografia dischi di Azure la usa per eseguire il wrapping dei segreti di crittografia prima di scrivere nell'insieme di credenziali delle chiavi. Il parametro -VolumeType è impostato sui dischi dati e non sul disco del sistema operativo. Se la macchina virtuale è stata precedentemente crittografata con un tipo di volume "OS" o "All", il parametro -VolumeType deve essere modificato in All, in modo che vengano inclusi sia il disco del sistema operativo sia il nuovo disco dati.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> </br>
La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

## <a name="disable-encryption-for-linux-vms"></a>Disabilitare la crittografia per le macchine virtuali Linux
È possibile disabilitare la crittografia usando Azure PowerShell, l'interfaccia della riga di comando di Azure oppure un modello di Resource Manager. 

>[!IMPORTANT]
>La disabilitazione della crittografia con Crittografia dischi di Azure nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportata nei dati o nei volumi del sistema operativo, se il volume del sistema operativo è stato crittografato.  

- **Disabilitare la crittografia dei dischi con Azure PowerShell:** per disabilitare la crittografia, usare il cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Disabilitare la crittografia con l'interfaccia della riga di comando di Azure:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Disabilitare la crittografia con un modello di Resource Manager:** Usare il modello per [disabilitare la crittografia in una macchina virtuale Linux in esecuzione](https://aka.ms/decrypt-linuxvm).
     1. Fare clic su **Distribuzione in Azure**.
     2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione, la macchina virtuale, i termini legali e il contratto.
     3.  Fare clic su **Acquista** per disabilitare la crittografia dischi in una macchina virtuale Windows in esecuzione. 


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Abilitare Crittografia dischi di Azure per Windows](azure-security-disk-encryption-windows-aad.md)
