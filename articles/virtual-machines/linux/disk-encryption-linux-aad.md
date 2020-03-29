---
title: Crittografia dischi di Azure con un'app Azure AD per macchine virtuali IaaS Linux (versione precedente)
description: Questo articolo offre informazioni sull'abilitazione di Crittografia dischi di Microsoft Azure per le macchine virtuali IaaS Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: ee365d37a957350fa8a68da0f34149d3210d6238
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970614"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Abilitare Crittografia disco di Azure con Azure AD in macchine virtuali Linux (versione precedente)Enable Azure Disk Encryption with Azure AD on Linux VMs (previous release)

La nuova versione di Crittografia disco di Azure elimina il requisito per fornire un parametro dell'applicazione Azure Active Directory (Azure AD) per abilitare la crittografia del disco della macchina virtuale. Con la nuova versione, non è più necessario specificare le credenziali di Azure AD durante il passaggio di abilitazione della crittografia. Tutte le nuove macchine virtuali devono essere crittografate senza i parametri dell'applicazione Azure AD usando la nuova versione. Per istruzioni su come abilitare la crittografia del disco della macchina virtuale usando la nuova versione, vedere Crittografia del disco di Azure per VMS Linux.For instructions on how to enable VM disk encryption by using the new release, see [Azure Disk Encryption for Linux VMS](disk-encryption-linux.md). Le macchine virtuali che sono già state crittografate con i parametri dell'applicazione Azure AD sono ancora supportate e dovrebbero continuare a essere gestite con la sintassi di AAD.

È possibile abilitare molti scenari di crittografia del disco e i passaggi possono variare in base allo scenario. Le sezioni seguenti illustrano gli scenari in modo più dettagliato per le macchine virtuali Linux infrastructure as a service (IaaS). È possibile applicare la crittografia del disco solo alle macchine virtuali con [dimensioni vme e sistemi operativi supportati.](disk-encryption-overview.md#supported-vms-and-operating-systems) È inoltre necessario soddisfare i prerequisiti seguenti:You must also meet the following prerequisites:

- [Requisiti aggiuntivi per le macchine virtualiAdditional requirements for VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Rete e Criteri di gruppo](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Requisiti di archiviazione delle chiavi di crittografiaEncryption key storage requirements](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Creare uno [snapshot,](snapshot-copy-managed-disk.md)eseguire un backup o entrambi prima di crittografare i dischi. I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Le macchine virtuali con dischi gestiti richiedono il backup prima della crittografia. Dopo aver eseguito un backup, è possibile utilizzare il cmdlet Set-AzVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Per altre informazioni su come eseguire il backup e il ripristino di macchine virtuali crittografate, vedere Backup di Azure.For more information about how to back up and restore encrypted VMs, see [Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Se in precedenza è stato usato Crittografia disco di [Azure con l'app Azure AD](disk-encryption-overview-aad.md) per crittografare questa macchina virtuale, è necessario continuare a usare questa opzione per crittografare la macchina virtuale. Non è possibile usare [Crittografia disco](disk-encryption-overview.md) di Azure in questa macchina virtuale crittografata perché non si tratta di uno scenario supportato, ovvero il passaggio dall'applicazione Azure AD per questa macchina virtuale crittografata non è ancora supportato.
 > - Per assicurarsi che i segreti di crittografia non superino i limiti regionali, Crittografia disco di Azure richiede che l'insieme di credenziali delle chiavi e le macchine virtuali si trovino nella stessa area. Creare e usare un insieme di credenziali delle chiavi che si trovare nella stessa area della macchina virtuale da crittografare.
 > - Quando si crittografano i volumi del sistema operativo Linux, il processo può richiedere alcune ore. È normale che i volumi del sistema operativo Linux risultino più tempo rispetto ai volumi di dati da crittografare.
> - Quando si crittografano i volumi del sistema operativo Linux, la macchina virtuale deve essere considerata non disponibile. Si consiglia di evitare gli accessi SSH mentre la crittografia è in corso per evitare di bloccare tutti i file aperti a cui è necessario accedere durante il processo di crittografia. Per controllare lo stato di avanzamento, usare i comandi [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) o [vm encryption show.](/cli/azure/vm/encryption#az-vm-encryption-show) È possibile che questo processo richiederà alcune ore per un volume del sistema operativo da 30 GB, oltre a tempo aggiuntivo per la crittografia dei volumi di dati. Il tempo di crittografia del volume di dati è proporzionale alle dimensioni e alla quantità dei volumi di dati, a meno che non venga utilizzata l'opzione crittografa tutto il **formato.** 
 > - La disabilitazione della crittografia nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportato sui volumi di dati o sistemi di utilizzo del sistema operativo se il volume del sistema operativo è stato crittografato. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> Abilitare la crittografia in una macchina virtuale IaaS Linux esistente o in esecuzione

In questo scenario è possibile abilitare la crittografia usando il modello di Azure Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando di Azure.In this scenario, you can enable encryption by using the Azure Resource Manager template, PowerShell cmdlets, or Azure CLI commands. 

>[!IMPORTANT]
 >È obbligatorio creare uno snapshot o eseguire il backup di un'istanza di una macchina virtuale basata su disco gestito all'esterno e prima di abilitare Crittografia disco di Azure.It's mandatory to take a snapshot or back up a managed disk-based VM instance outside of e before enabling Azure Disk Encryption. È possibile creare uno snapshot del disco gestito dal portale di Azure oppure usare Backup di [Azure.](../../backup/backup-azure-vms-encryption.md) I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Dopo aver eseguito un backup, utilizzare il cmdlet Set-AzVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Il comando Set-AzVMDiskEncryptionExtension ha esito negativo nelle macchine virtuali basate su disco gestite finché non viene eseguito un backup e non viene specificato questo parametro. 
>
>La crittografia o la disabilitazione della crittografia potrebbe causare il riavvio della macchina virtuale. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Abilitare la crittografia in una macchina virtuale Linux esistente o in esecuzione usando l'interfaccia della riga di comando di AzureEnable encryption on an existing or running Linux VM by using the Azure CLI 
È possibile abilitare la crittografia del disco nel disco rigido virtuale crittografato installando e usando lo strumento da riga di comando dell'interfaccia della riga di comando dell'interfaccia della riga di comando di [Azure CLI 2.0.You](/cli/azure) can enable disk encryption on your encrypted VHD by installing and using the Azure CLI 2.0 command-line tool. È possibile usarlo nel browser con [Azure Cloud Shell](../../cloud-shell/overview.md) oppure installarlo nel computer locale e usarlo in una sessione di PowerShell. Per abilitare la crittografia in macchine virtuali IaaS Linux esistenti o in esecuzione in Azure, usare i comandi seguenti dell'interfaccia della riga di comando:

Usare il comando [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure.

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:Encrypt a running VM by using a client secret:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Crittografare una macchina virtuale in esecuzione usando KEK per eseguire il wrapping del segreto client:Encrypt a running VM by using KEK to wrap the client secret:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa dell'identificatore completo: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br> La sintassi per il valore del parametro key-encryption-key è l'URI completo per la chiave KEK come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Verificare che i dischi siano crittografati:** Per verificare lo stato di crittografia di una macchina virtuale IaaS, usare il comando [az vm encryption show.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Disabilitare la crittografia:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). È possibile disabilitare la crittografia solo nei volumi di dati per le macchine virtuali Linux.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a> Abilitare la crittografia in una macchina virtuale Linux esistente o in esecuzione tramite PowerShellEnable encryption on an existing or running Linux VM by using PowerShell
Usare il cmdlet Set-AzVMDiskEncryptionExtension per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure.Use the [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet to enable encryption on a running IaaS virtual machine in Azure. Creare uno [snapshot](snapshot-copy-managed-disk.md) o eseguire un backup della macchina virtuale con Backup di [Azure](../../backup/backup-azure-vms-encryption.md) prima che i dischi vengano crittografati. Il parametro -skipVmBackup è già specificato negli script di PowerShell per crittografare una macchina virtuale Linux in esecuzione.

- **Crittografare una macchina virtuale in esecuzione usando un segreto client:Encrypt** a running VM by using a client secret: Lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale, l'insieme di credenziali delle chiavi, l'app Azure AD e il segreto client devono essere già stati creati come prerequisiti. Sostituire MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret con i valori specificati. Modificare il parametro -VolumeType per specificare i dischi da crittografare.

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
- **Crittografare una macchina virtuale in esecuzione usando KEK per eseguire il wrapping del segreto client:Encrypt a running VM by using KEK to wrap the client secret:** Crittografia disco di Azure consente di specificare una chiave esistente nell'insieme di credenziali delle chiavi per eseguire il wrapping dei segreti di crittografia del disco generati durante l'abilitazione della crittografia. Quando viene specificata una chiave di crittografia della chiave, Crittografia disco di Azure usa tale chiave per eseguire il wrapping dei segreti di crittografia prima di scrivere nell'insieme di credenziali delle chiavi. Modificare il parametro -VolumeType per specificare i dischi da crittografare. 

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
  > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa dell'identificatore completo: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  La sintassi per il valore del parametro key-encryption-key è l'URI completo per la chiave KEK come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Verificare che i dischi siano crittografati:** Per verificare lo stato di crittografia di una macchina virtuale IaaS, utilizzare il cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Disabilitare la crittografia del disco:** per disabilitare la crittografia, usare il cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). È possibile disabilitare la crittografia solo nei volumi di dati per le macchine virtuali Linux.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a> Abilitare la crittografia in una macchina virtuale IaaS Linux esistente o in esecuzione con un modello

È possibile abilitare la crittografia dei dischi nelle macchine virtuali IaaS Linux esistenti o in esecuzione usando il modello di [Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Selezionare **Distribuisci in Azure** nel modello di avvio rapido di Azure.Select Deploy to Azure on the Azure quickstart template.

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, i parametri, i termini legali e il contratto. Selezionare **Crea** per abilitare la crittografia nella macchina virtuale IaaS esistente o in esecuzione.

La tabella seguente elenca i parametri del modello di Resource Manager per macchine virtuali esistenti o in esecuzione che usano un ID client di Azure AD:

| Parametro | Descrizione |
| --- | --- |
| AADClientID | ID client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | Segreto client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave. È possibile ottenerlo con il comando dell'interfaccia della riga di comando di Azure `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave generata. Questo parametro è facoltativo se si seleziona **nokek** nell'elenco a discesa **UseExistingKek.** Se si seleziona **kek** nell'elenco a discesa **UseExistingKek,** è necessario immettere il valore _keyEncryptionKeyURL._ |
| volumeType | Tipo del volume in cui viene eseguita l'operazione di crittografia. I valori supportati validi sono _OS_ o _All_. Vedere distribuzioni Linux supportate e le relative versioni per il sistema operativo e i dischi dati nella sezione Prerequisiti in precedenza. |
| sequenceVersion | Versione della sequenza dell'operazione BitLocker. Incrementare questo numero di versione ogni volta che viene eseguita un'operazione di crittografia dei dischi nella stessa VM. |
| vmName | Nome della VM in cui deve essere eseguita l'operazione di crittografia. |
| passphrase | Immettere una passphrase complessa come chiave di crittografia dei dati. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Usare la funzionalità EncryptFormatAll per i dischi dati nelle macchine virtuali Linux IaaSUse the EncryptFormatAll feature for data disks on Linux IaaS VMs
Il parametro EncryptFormatAll riduce la durata per la crittografia dei dischi dati Linux. Le partizioni che soddisfano determinati criteri vengono formattate (con il file system corrente). Poi vengono rimontati dove erano prima dell'esecuzione del comando. Se si desidera escludere un disco dati che soddisfa i criteri, è possibile smontarlo prima di eseguire il comando.

 Dopo aver eseguito questo comando, tutte le unità montate in precedenza vengono formattate. Quindi il livello di crittografia inizia sopra l'unità ora vuota. Quando questa opzione è selezionata, viene crittografato anche il disco di risorse effimere collegato alla macchina virtuale. Se l'unità effimera viene reimpostata, viene riformattata e crittografata nuovamente per la macchina virtuale dalla soluzione Crittografia disco di Azure all'opportunità successiva.

>[!WARNING]
> EncryptFormatAll non deve essere usato quando sono presenti dati necessari nei volumi di dati di una macchina virtuale. È possibile escludere i dischi dalla crittografia smontandoli. Provare prima il parametro EncryptFormatAll in una macchina virtuale di test per comprendere il parametro della funzionalità e le relative implicazioni prima di provarlo nella macchina virtuale di produzione. L'opzione EncryptFormatAll formatta il disco dati, in modo che tutti i dati su di esso andranno persi. Prima di procedere, verificare che tutti i dischi che si desidera escludere siano correttamente smontati. </br></br>
 >Se si imposta questo parametro durante l'aggiornamento delle impostazioni di crittografia, potrebbe verificarsi un riavvio prima della crittografia effettiva. In questo caso, si desidera rimuovere anche il disco che non si desidera formattare dal file fstab. Analogamente, è necessario aggiungere la partizione che si desidera crittografare in formato crittografato al file fstab prima di avviare l'operazione di crittografia. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> Criteri EncryptFormatAll
Il parametro passa attraverso tutte le partizioni e le crittografa purché soddisfino *tutti i* criteri seguenti: 
- Non è una partizione di avvio/del sistema operativo/radice
- Non è già stata crittografata
- Non è un volume BEK
- Non è un volume RAID
- Non è un volume LVM
- È montata

Crittografare i dischi che compongono il volume RAID o LVM anziché il volume stesso.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> Usare il parametro EncryptFormatAll con un modello
Per usare l'opzione EncryptFormatAll, usare qualsiasi modello di Azure Resource Manager preesistente che crittografa una macchina virtuale Linux e modificare il campo EncryptionOperation per la risorsa AzureDiskEncryption.To use the EncryptFormatAll option, use any preexisting Azure Resource Manager template that encrypts a Linux VM and change the **EncryptionOperation** field for the AzureDiskEncryption resource.

1. Ad esempio, usare il [modello di Resource Manager per crittografare una macchina virtuale IaaS Linux in esecuzione](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Selezionare **Distribuisci in Azure** nel modello di avvio rapido di Azure.Select Deploy to Azure on the Azure quickstart template.
3. Modificare il campo **EncryptionOperation** da **EnableEncryption** a **EnableEncryptionFormatAl**.
4. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, gli altri parametri, i termini legali e il contratto. Selezionare **Crea** per abilitare la crittografia nella macchina virtuale IaaS esistente o in esecuzione.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> Usare il parametro EncryptFormatAll con un cmdlet di PowerShell
Utilizzare il cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) con il parametro EncryptFormatAll.

**Crittografare una macchina virtuale in esecuzione usando un segreto client e EncryptFormatAll:Encrypt a running VM by using a client secret and EncryptFormatAll:** Ad esempio, lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension con il parametro EncryptFormatAll. Il gruppo di risorse, la macchina virtuale, l'insieme di credenziali delle chiavi, l'app Azure AD e il segreto client devono essere già stati creati come prerequisiti. Sostituire MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret con i valori specificati.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a> Usare il parametro EncryptFormatAll con Logical Volume Manager (LVM) 
È consigliabile usare una configurazione LVM-on-crypt. Per tutti gli esempi seguenti, sostituire il percorso del dispositivo e i punti di montaggio con qualsiasi sia il tuo caso d'uso. La configurazione può essere eseguita nel seguente modo:

- Aggiungere i dischi dati che compongono la macchina virtuale.
- Formattare, montare e aggiungere i dischi al file fstab.

    1. Formattare il disco appena aggiunto. Qui vengono usati i collegamenti simbolici generati da Azure. L'uso di collegamenti simbolici evita i problemi legati alla modifica dei nomi dei dispositivi. Per ulteriori informazioni, consultate [Risoluzione dei problemi relativi ai nomi](troubleshoot-device-names-problems.md)dei dispositivi.
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. Montare i dischi.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. Aggiungerli al file fstab.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. Eseguire il cmdlet PowerShell Set-AzVMDiskEncryptionExtension con -EncryptFormatAll per crittografare questi dischi.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. Configurare LVM sui nuovi dischi. Si noti che le unità crittografate vengono sbloccate dopo il termine dell'avvio della macchina virtuale. Pertanto, anche il montaggio di LVM dovrà essere posticipato.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a> Nuove macchine virtuali IaaS create da chiavi di crittografia e disco rigido virtuale crittografato dal cliente
In questo scenario è possibile abilitare la crittografia usando il modello di Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti descrivono in modo più dettagliato il modello di Resource Manager e i comandi dell'interfaccia della riga di comando. 

Usare le istruzioni nell'appendice per la preparazione delle immagini pre-crittografate che possono essere usate in Azure. Dopo aver creato l'immagine, è possibile usare i passaggi della sezione successiva per creare una VM di Azure crittografata.

* [Preparare un disco rigido virtuale Linux pre-crittografato](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >È obbligatorio creare uno snapshot o eseguire il backup di un'istanza di una macchina virtuale basata su disco gestito all'esterno e prima di abilitare Crittografia disco di Azure.It's mandatory to take a snapshot or back up a managed disk-based VM instance outside of e before enabling Azure Disk Encryption. È possibile creare uno snapshot del disco gestito dal portale oppure usare Backup di [Azure.](../../backup/backup-azure-vms-encryption.md) I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Dopo aver eseguito un backup, utilizzare il cmdlet Set-AzVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Il comando Set-AzVMDiskEncryptionExtension ha esito negativo nelle macchine virtuali basate su disco gestite finché non viene eseguito un backup e non viene specificato questo parametro. 
>
>La crittografia o la disabilitazione della crittografia potrebbe causare il riavvio della macchina virtuale.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> Usare Azure PowerShell per crittografare le macchine virtuali IaaS con dischi rigidi virtuali pre-crittografati 
È possibile abilitare la crittografia del disco nel disco rigido virtuale crittografato utilizzando il cmdlet di PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). Nell'esempio seguente vengono fornite alcune opzioni comuni. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Abilitare la crittografia in un disco dati appena aggiunto
È possibile aggiungere un nuovo disco dati usando [az vm disk attach](add-disk.md) o tramite il portale di [Azure.](attach-disk-portal.md) Prima che sia possibile eseguire la crittografia, è necessario montare il disco appena collegato. È necessario richiedere la crittografia dell'unità dati perché l'unità sarà inutilizzabile mentre è in corso la crittografia. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Abilitare la crittografia in un disco appena aggiunto con l'interfaccia della riga di comando di AzureEnable encryption on a newly added disk with the Azure CLI
 Se la macchina virtuale è stata crittografata in precedenza con "Tutti", il parametro --volume-type deve rimanere All. Tale parametro include il disco del sistema operativo e il disco dati. Se la macchina virtuale è stata precedentemente crittografata con un tipo di volume "OS", il parametro --volume-type deve essere modificato in All in modo che vengano inclusi sia il sistema operativo che il nuovo disco dati. Se la macchina virtuale è stata crittografata solo con il tipo di volume "Dati", può rimanere dati come illustrato di seguito. L'aggiunta e il collegamento di un nuovo disco dati a una macchina virtuale non è una preparazione sufficiente per la crittografia. Il disco appena collegato deve inoltre essere formattato e montato correttamente all'interno della macchina virtuale prima di abilitare la crittografia. In Linux, il disco deve essere montato in /etc/fstab con un nome di [dispositivo a blocchi persistente](troubleshoot-device-names-problems.md). 

A differenza della sintassi di PowerShell, l'interfaccia della riga di comando non richiede di fornire una versione di sequenza univoca quando si abilita la crittografia. L'interfaccia della riga di comando genera e usa automaticamente uno specifico valore di versione di sequenza univoco.

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:Encrypt a running VM by using a client secret:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Crittografare una macchina virtuale in esecuzione usando KEK per eseguire il wrapping del segreto client:Encrypt a running VM by using KEK to wrap the client secret:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Abilitare la crittografia in un disco appena aggiunto con Azure PowerShell
 Quando si usa Powershell per crittografare un nuovo disco per Linux, è necessario specificare una nuova versione della sequenza. La versione della sequenza deve essere univoca. Lo script seguente genera un GUID per la versione della sequenza. 
 

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:Encrypt** a running VM by using a client secret: Lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale, l'insieme di credenziali delle chiavi, l'app Azure AD e il segreto client devono essere già stati creati come prerequisiti. Sostituire MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret con i valori specificati. Il parametro -VolumeType è impostato sui dischi dati e non sul disco del sistema operativo. Se la macchina virtuale è stata precedentemente crittografata con un tipo di volume "OS" o "Tutti", il parametro -VolumeType deve essere modificato in All in modo che vengano inclusi sia il sistema operativo che il nuovo disco dati.

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
- **Crittografare una macchina virtuale in esecuzione usando KEK per eseguire il wrapping del segreto client:Encrypt a running VM by using KEK to wrap the client secret:** Crittografia disco di Azure consente di specificare una chiave esistente nell'insieme di credenziali delle chiavi per eseguire il wrapping dei segreti di crittografia del disco generati durante l'abilitazione della crittografia. Quando viene specificata una chiave di crittografia della chiave, Crittografia disco di Azure usa tale chiave per eseguire il wrapping dei segreti di crittografia prima di scrivere nell'insieme di credenziali delle chiavi. Il parametro -VolumeType è impostato sui dischi dati e non sul disco del sistema operativo. Se la macchina virtuale è stata precedentemente crittografata con un tipo di volume "OS" o "Tutti", il parametro -VolumeType deve essere modificato in All in modo che vengano inclusi sia il sistema operativo che il nuovo disco dati.

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
> La sintassi per il valore del parametro disk-encryption-keyvault è la stringa dell'identificatore completo: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]. </br> </br>
La sintassi per il valore del parametro key-encryption-key è l'URI completo per la chiave KEK come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Disabilitare la crittografia per le macchine virtuali Linux
È possibile disabilitare la crittografia usando Azure PowerShell, l'interfaccia della riga di comando di Azure o un modello di Resource Manager.You can disable encryption by using Azure PowerShell, the Azure CLI, or a Resource Manager template. 

>[!IMPORTANT]
>La disabilitazione della crittografia con Crittografia dischi di Azure nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportato sui volumi di dati o sistemi di utilizzo del sistema operativo se il volume del sistema operativo è stato crittografato. 

- **Disabilitare la crittografia del disco con Azure PowerShell:Disable disk encryption with Azure PowerShell:** Per disabilitare la crittografia, usare il cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Disabilitare la crittografia con l'interfaccia della riga di comando di Azure:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Disabilitare la crittografia con un modello di Resource Manager:Disable encryption with a Resource Manager template:** Per disabilitare la crittografia, usare il modello Disabilita crittografia in un modello [di macchina virtuale Linux in esecuzione.](https://aka.ms/decrypt-linuxvm)
     1. Selezionare **Distribuisci in Azure**.
     2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione, la macchina virtuale, i termini legali e il contratto.
     3. Selezionare **Acquista** per disabilitare la crittografia del disco in una macchina virtuale Windows in esecuzione. 


## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Crittografia disco di Azure per LinuxAzure Disk Encryption for Linux overview](disk-encryption-overview-aad.md)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di Azure con Azure AD (versione precedente)Creating and configuring a key vault for Azure Disk Encryption with Azure AD (previous release)](disk-encryption-key-vault-aad.md)
