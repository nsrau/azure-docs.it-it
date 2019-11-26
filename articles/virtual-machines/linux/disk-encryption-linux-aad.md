---
title: Crittografia dischi di Azure con un'app Azure AD per macchine virtuali IaaS Linux (versione precedente)
description: Questo articolo offre informazioni sull'abilitazione di Crittografia dischi di Microsoft Azure per le macchine virtuali IaaS Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: d41f2138a453e4a34354c10bbebad41724a18d1d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457476"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Abilitare crittografia dischi di Azure con Azure AD nelle macchine virtuali Linux (versione precedente)

La nuova versione di crittografia dischi di Azure Elimina la necessità di fornire un parametro dell'applicazione Azure Active Directory (Azure AD) per abilitare la crittografia del disco della macchina virtuale. Con la nuova versione, non è più necessario specificare le credenziali di Azure AD durante il passaggio di abilitazione della crittografia. Tutte le nuove macchine virtuali devono essere crittografate senza i parametri dell'applicazione Azure AD usando la nuova versione. Per istruzioni su come abilitare la crittografia del disco della macchina virtuale usando la nuova versione, vedere [crittografia dischi di Azure per macchine virtuali Linux](disk-encryption-linux.md). Le macchine virtuali che sono già state crittografate con i parametri dell'applicazione Azure AD sono ancora supportate e dovrebbero continuare a essere gestite con la sintassi di AAD.

È possibile abilitare molti scenari di crittografia dei dischi e la procedura può variare in base allo scenario. Le sezioni seguenti illustrano in modo più dettagliato gli scenari per le macchine virtuali IaaS (Infrastructure as a Service) Linux. È possibile applicare la crittografia del disco solo alle macchine virtuali di [dimensioni e sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems)per le VM. È inoltre necessario soddisfare i seguenti prerequisiti:

- [Requisiti aggiuntivi per le macchine virtuali](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Rete e Criteri di gruppo](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Requisiti di archiviazione delle chiavi di crittografia](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Eseguire uno [snapshot](snapshot-copy-managed-disk.md), eseguire un backup o entrambi prima di crittografare i dischi. I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Le macchine virtuali con dischi gestiti richiedono il backup prima della crittografia. Dopo aver eseguito un backup, è possibile usare il cmdlet Set-AzVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro-skipVmBackup. Per altre informazioni su come eseguire il backup e il ripristino di macchine virtuali crittografate, vedere [backup di Azure](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Se in precedenza è stato usato [crittografia dischi di Azure con l'app Azure ad](disk-encryption-overview-aad.md) per crittografare questa macchina virtuale, è necessario continuare a usare questa opzione per crittografare la macchina virtuale. Non è possibile usare [crittografia dischi di Azure](disk-encryption-overview.md) in questa macchina virtuale crittografata perché non è uno scenario supportato, il che significa che il trasferimento dall'applicazione Azure ad per questa macchina virtuale crittografata non è ancora supportato.
 > - Per assicurarsi che i segreti di crittografia non superino i limiti regionali, crittografia dischi di Azure richiede l'insieme di credenziali delle chiavi e le macchine virtuali da condividere nella stessa area. Creare e usare un insieme di credenziali delle chiavi che si trova nella stessa area della macchina virtuale da crittografare.
 > - Quando si crittografano i volumi del sistema operativo Linux, il processo può richiedere alcune ore. Per i volumi del sistema operativo Linux è normale richiedere più tempo dei volumi di dati da crittografare.
> - Quando si crittografano i volumi del sistema operativo Linux, la macchina virtuale deve essere considerata non disponibile. È consigliabile evitare gli accessi SSH mentre è in corso la crittografia per evitare di bloccare i file aperti a cui è necessario accedere durante il processo di crittografia. Per controllare lo stato di avanzamento, usare i comandi [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) o [VM Encryption Show](/cli/azure/vm/encryption#az-vm-encryption-show) . È possibile prevedere che questo processo imprenda alcune ore per un volume del sistema operativo da 30 GB, oltre a ulteriore tempo per la crittografia dei volumi di dati. Il tempo di crittografia del volume dei dati è proporzionale alle dimensioni e alla quantità dei volumi di dati, a meno che non venga utilizzata l'opzione **Encrypt Format all** . 
 > - La disabilitazione della crittografia nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportata nei volumi di dati o sistemi operativi se il volume del sistema operativo è stato crittografato. 

 

## <a name="bkmk_RunningLinux"> </a> Abilitare la crittografia in una macchina virtuale IaaS Linux esistente o in esecuzione

In questo scenario è possibile abilitare la crittografia usando il modello di Azure Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando di Azure. 

>[!IMPORTANT]
 >È obbligatorio eseguire uno snapshot o eseguire il backup di un'istanza di macchina virtuale basata su disco gestito all'esterno di e prima di abilitare crittografia dischi di Azure. È possibile creare uno snapshot del disco gestito dalla portale di Azure oppure è possibile usare [backup di Azure](../../backup/backup-azure-vms-encryption.md). I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Dopo aver eseguito un backup, usare il cmdlet Set-AzVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro-skipVmBackup. Il comando set-AzVMDiskEncryptionExtension ha esito negativo rispetto alle VM basate su disco gestite fino a quando non viene eseguito un backup e questo parametro viene specificato. 
>
>La crittografia o la disabilitazione della crittografia potrebbe causare il riavvio della macchina virtuale. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Abilitare la crittografia in una VM Linux esistente o in esecuzione usando l'interfaccia della riga di comando di Azure 
È possibile abilitare la crittografia del disco nel VHD crittografato installando e usando lo strumento da riga di comando [2,0 dell'interfaccia](/cli/azure) della riga di comando di Azure. È possibile usarlo nel browser con [Azure Cloud Shell](../../cloud-shell/overview.md) oppure installarlo nel computer locale e usarlo in una sessione di PowerShell. Per abilitare la crittografia in macchine virtuali IaaS Linux esistenti o in esecuzione in Azure, usare i comandi seguenti dell'interfaccia della riga di comando:

Usare il comando [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure.

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Crittografare una macchina virtuale in esecuzione usando KEK per eseguire il wrapping del segreto client:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > La sintassi per il valore del parametro Disk-Encryption-Vault è la stringa dell'identificatore completo:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nome-insieme-credenziali].</br> </br> La sintassi per il valore del parametro Key-Encryption-Key è l'URI completo di KEK come in: https://[nome-insieme di credenziali delle chiavi]. Vault. Azure. NET/Keys/[kekname]/[KEK-Unique-ID].

- **Verificare che i dischi siano crittografati:** Per verificare lo stato della crittografia di una VM IaaS, usare il comando [AZ VM Encryption Show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Disabilitare la crittografia:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). È possibile disabilitare la crittografia solo nei volumi di dati per le macchine virtuali Linux.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"></a> Abilitare la crittografia in una VM Linux esistente o in esecuzione usando PowerShell
Usare il cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure. Eseguire uno [snapshot](snapshot-copy-managed-disk.md) o eseguire un backup della macchina virtuale con [backup di Azure](../../backup/backup-azure-vms-encryption.md) prima di crittografare i dischi. Il parametro-skipVmBackup è già specificato negli script di PowerShell per crittografare una VM Linux in esecuzione.

- **Crittografare una macchina virtuale in esecuzione usando un segreto client:** Lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale, l'insieme di credenziali delle chiavi, l'app Azure AD e il segreto client devono essere già stati creati come prerequisiti. Sostituire MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-Secret con i valori. Modificare il parametro-VolumeType per specificare i dischi da crittografare.

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
- **Crittografare una macchina virtuale in esecuzione usando KEK per eseguire il wrapping del segreto client:** Crittografia dischi di Azure consente di specificare una chiave esistente nell'insieme di credenziali delle chiavi per eseguire il wrapping dei segreti di crittografia del disco generati durante l'abilitazione della crittografia. Quando si specifica una chiave di crittografia della chiave, crittografia dischi di Azure usa tale chiave per eseguire il wrapping dei segreti di crittografia prima di scrivere nell'insieme di credenziali delle chiavi. Modificare il parametro-VolumeType per specificare i dischi da crittografare. 

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
  > La sintassi per il valore del parametro Disk-Encryption-Vault è la stringa dell'identificatore completo:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[KVresource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nome-insieme di credenziali].</br> </br>
  La sintassi per il valore del parametro Key-Encryption-Key è l'URI completo di KEK come in: https://[nome-insieme di credenziali delle chiavi]. Vault. Azure. NET/Keys/[kekname]/[KEK-Unique-ID]. 
    
- **Verificare che i dischi siano crittografati:** Per verificare lo stato della crittografia di una VM IaaS, usare il cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Disabilitare la crittografia del disco:** per disabilitare la crittografia, usare il cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). È possibile disabilitare la crittografia solo nei volumi di dati per le macchine virtuali Linux.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Abilitare la crittografia in una macchina virtuale IaaS Linux esistente o in esecuzione con un modello

È possibile abilitare la crittografia dei dischi nelle macchine virtuali IaaS Linux esistenti o in esecuzione usando il modello di [Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Selezionare **Distribuisci in Azure** nel modello di avvio rapido di Azure.

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, i parametri, i termini legali e il contratto. Selezionare **Crea** per abilitare la crittografia nella macchina virtuale IaaS esistente o in esecuzione.

La tabella seguente elenca i parametri del modello di Resource Manager per macchine virtuali esistenti o in esecuzione che usano un ID client di Azure AD:

| . | DESCRIZIONE |
| --- | --- |
| AADClientID | ID client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | Segreto client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave. È possibile ottenerlo con il comando dell'interfaccia della riga di comando di Azure `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave generata. Questo parametro è facoltativo se si seleziona **nokek** nell'elenco a discesa **UseExistingKek** . Se si seleziona **KEK** nell'elenco a discesa **UseExistingKek** , è necessario immettere il valore _keyEncryptionKeyURL_ . |
| volumeType | Tipo del volume in cui viene eseguita l'operazione di crittografia. I valori supportati validi sono _OS_ o _All_. (Vedere le distribuzioni Linux supportate e le relative versioni per i dischi dati e del sistema operativo nella sezione Prerequisiti precedente). |
| sequenceVersion | Versione della sequenza dell'operazione BitLocker. Incrementare questo numero di versione ogni volta che viene eseguita un'operazione di crittografia dei dischi nella stessa VM. |
| vmName | Nome della VM in cui deve essere eseguita l'operazione di crittografia. |
| passphrase | Immettere una passphrase complessa come chiave di crittografia dei dati. |



## <a name="bkmk_EFA"> </a>Usare la funzionalità EncryptFormatAll per i dischi dati in macchine virtuali IaaS Linux
Il parametro EncryptFormatAll riduce il tempo per la crittografia dei dischi dati Linux. Le partizioni che soddisfano determinati criteri vengono formattate (con la file system corrente). Quindi vengono rimontati nella posizione in cui si trovavano prima dell'esecuzione del comando. Se si vuole escludere un disco dati che soddisfa i criteri, è possibile smontarlo prima di eseguire il comando.

 Dopo aver eseguito questo comando, tutte le unità montate in precedenza vengono formattate. Quindi, il livello di crittografia viene avviato sopra l'unità ora vuota. Quando questa opzione è selezionata, viene crittografato anche il disco della risorsa temporanea collegato alla macchina virtuale. Se l'unità temporanea viene reimpostata, viene riformattata e ricrittografata per la macchina virtuale dalla soluzione crittografia dischi di Azure alla prossima opportunità.

>[!WARNING]
> EncryptFormatAll non deve essere usato quando sono presenti dati necessari nei volumi di dati di una macchina virtuale. È possibile escludere i dischi dalla crittografia smontando i dischi. Provare prima di tutto il parametro EncryptFormatAll in una macchina virtuale di test per comprendere il parametro della funzionalità e le relative implicazioni prima di provarlo nella macchina virtuale di produzione. L'opzione EncryptFormatAll formatta il disco dati, in modo che tutti i dati in esso contenuti andranno persi. Prima di procedere, verificare che tutti i dischi che si vuole escludere siano correttamente smontati. </br></br>
 >Se si imposta questo parametro durante l'aggiornamento delle impostazioni di crittografia, potrebbe verificarsi un riavvio prima della crittografia effettiva. In questo caso, si vuole anche rimuovere il disco che non si vuole formattare dal file fstab. Analogamente, è necessario aggiungere la partizione che si desidera crittografare nel file fstab prima di avviare l'operazione di crittografia. 

### <a name="bkmk_EFACriteria"> </a> Criteri EncryptFormatAll
Il parametro passa attraverso tutte le partizioni e le crittografa fino a quando soddisfano *tutti* i criteri seguenti: 
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
2. Selezionare **Distribuisci in Azure** nel modello di avvio rapido di Azure.
3. Modificare il campo **EncryptionOperation** da **EnableEncryption** a **EnableEncryptionFormatAl**.
4. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, gli altri parametri, i termini legali e il contratto. Selezionare **Crea** per abilitare la crittografia nella macchina virtuale IaaS esistente o in esecuzione.


### <a name="bkmk_EFAPSH"> </a> Usare il parametro EncryptFormatAll con un cmdlet di PowerShell
Usare il cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) con il parametro EncryptFormatAll.

**Crittografare una macchina virtuale in esecuzione usando un segreto client e EncryptFormatAll:** Ad esempio, lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension con il parametro EncryptFormatAll. Il gruppo di risorse, la macchina virtuale, l'insieme di credenziali delle chiavi, l'app Azure AD e il segreto client devono essere già stati creati come prerequisiti. Sostituire MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-Secret con i valori.
  
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
È consigliabile usare una configurazione LVM-on-crypt. Per tutti gli esempi seguenti, sostituire il percorso del dispositivo e montaggio con quello che soddisfa il caso d'uso. La configurazione può essere eseguita nel seguente modo:

- Aggiungere i dischi dati che compongono la macchina virtuale.
- Formattare, montare e aggiungere i dischi al file fstab.

    1. Formattare il disco appena aggiunto. Qui vengono usati i collegamenti simbolici generati da Azure. L'uso di collegamenti simbolici evita i problemi legati alla modifica dei nomi dei dispositivi. Per ulteriori informazioni, vedere [risolvere i problemi relativi ai nomi dei dispositivi](troubleshoot-device-names-problems.md).
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. Montare i dischi.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. Aggiungerli al file fstab.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. Eseguire il cmdlet di PowerShell set-AzVMDiskEncryptionExtension con-EncryptFormatAll per crittografare questi dischi.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. Configurare LVM sui nuovi dischi. Si noti che le unità crittografate vengono sbloccate dopo il termine dell'avvio della macchina virtuale. Pertanto, anche il montaggio di LVM dovrà essere posticipato.




## <a name="bkmk_VHDpre"> </a> Nuove macchine virtuali IaaS create da chiavi di crittografia e disco rigido virtuale crittografato dal cliente
In questo scenario è possibile abilitare la crittografia usando il modello di Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti descrivono in modo più dettagliato il modello di Resource Manager e i comandi dell'interfaccia della riga di comando. 

Usare le istruzioni nell'appendice per la preparazione delle immagini pre-crittografate che possono essere usate in Azure. Dopo aver creato l'immagine, è possibile usare i passaggi della sezione successiva per creare una VM di Azure crittografata.

* [Preparare un disco rigido virtuale Linux pre-crittografato](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >È obbligatorio eseguire uno snapshot o eseguire il backup di un'istanza di macchina virtuale basata su disco gestito all'esterno di e prima di abilitare crittografia dischi di Azure. È possibile creare uno snapshot del disco gestito dal portale oppure è possibile usare [backup di Azure](../../backup/backup-azure-vms-encryption.md). I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Dopo aver eseguito un backup, usare il cmdlet Set-AzVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro-skipVmBackup. Il comando set-AzVMDiskEncryptionExtension ha esito negativo rispetto alle VM basate su disco gestite fino a quando non viene eseguito un backup e questo parametro viene specificato. 
>
>La crittografia o la disabilitazione della crittografia potrebbe causare il riavvio della macchina virtuale.



### <a name="bkmk_VHDprePSH"> </a> Usare Azure PowerShell per crittografare le macchine virtuali IaaS con dischi rigidi virtuali pre-crittografati 
È possibile abilitare la crittografia dei dischi nel disco rigido virtuale crittografato usando il cmdlet di PowerShell [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). Nell'esempio seguente vengono riportati alcuni parametri comuni. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Abilitare la crittografia in un disco dati appena aggiunto
È possibile aggiungere un nuovo disco dati usando il comando [AZ VM disk collegato](add-disk.md) o [tramite il portale di Azure](attach-disk-portal.md). Prima che sia possibile eseguire la crittografia, è necessario montare il disco appena collegato. È necessario richiedere la crittografia dell'unità dati perché l'unità non sarà utilizzabile mentre è in corso la crittografia. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Abilitare la crittografia in un disco appena aggiunto con l'interfaccia della riga di comando di Azure
 Se la macchina virtuale è stata precedentemente crittografata con "All", il parametro--volume-Type deve rimanere tutti. Tale parametro include il disco del sistema operativo e il disco dati. Se la macchina virtuale è stata precedentemente crittografata con un tipo di volume "sistema operativo", il parametro--volume-Type deve essere modificato in modo che sia il sistema operativo che il nuovo disco dati verranno inclusi. Se la macchina virtuale è stata crittografata solo con il tipo di volume "data", può rimanere dati come illustrato qui. L'aggiunta e il fissaggio di un nuovo disco dati a una macchina virtuale non è una preparazione sufficiente per la crittografia. Il disco appena collegato deve essere formattato e montato correttamente all'interno della macchina virtuale prima di abilitare la crittografia. In Linux è necessario montare il disco in/etc/fstab con un [nome di dispositivo a blocchi permanente](troubleshoot-device-names-problems.md). 

Diversamente dalla sintassi di PowerShell, l'interfaccia della riga di comando non richiede di specificare una versione di sequenza univoca quando si Abilita la crittografia. L'interfaccia della riga di comando genera e usa automaticamente uno specifico valore di versione di sequenza univoco.

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Crittografare una macchina virtuale in esecuzione usando KEK per eseguire il wrapping del segreto client:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Abilitare la crittografia in un disco appena aggiunto con Azure PowerShell
 Quando si usa PowerShell per crittografare un nuovo disco per Linux, è necessario specificare una nuova versione della sequenza. La versione della sequenza deve essere univoca. Lo script seguente genera un GUID per la versione della sequenza. 
 

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:** Lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale, l'insieme di credenziali delle chiavi, l'app Azure AD e il segreto client devono essere già stati creati come prerequisiti. Sostituire MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-Secret con i valori. Il parametro -VolumeType è impostato sui dischi dati e non sul disco del sistema operativo. Se la macchina virtuale è stata precedentemente crittografata con un tipo di volume "sistema operativo" o "tutti", sarà necessario modificare il parametro-VolumeType in modo che sia il sistema operativo che il nuovo disco dati verranno inclusi.

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
- **Crittografare una macchina virtuale in esecuzione usando KEK per eseguire il wrapping del segreto client:** Crittografia dischi di Azure consente di specificare una chiave esistente nell'insieme di credenziali delle chiavi per eseguire il wrapping dei segreti di crittografia del disco generati durante l'abilitazione della crittografia. Quando si specifica una chiave di crittografia della chiave, crittografia dischi di Azure usa tale chiave per eseguire il wrapping dei segreti di crittografia prima di scrivere nell'insieme di credenziali delle chiavi. Il parametro -VolumeType è impostato sui dischi dati e non sul disco del sistema operativo. Se la macchina virtuale è stata precedentemente crittografata con un tipo di volume "sistema operativo" o "tutti", sarà necessario modificare il parametro-VolumeType in modo che sia il sistema operativo che il nuovo disco dati verranno inclusi.

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
> La sintassi per il valore del parametro Disk-Encryption-Vault è la stringa dell'identificatore completo:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nome-insieme-credenziali]. </br> </br>
La sintassi per il valore del parametro Key-Encryption-Key è l'URI completo di KEK come in: https://[nome-insieme di credenziali delle chiavi]. Vault. Azure. NET/Keys/[kekname]/[KEK-Unique-ID].

## <a name="disable-encryption-for-linux-vms"></a>Disabilitare la crittografia per le macchine virtuali Linux
È possibile disabilitare la crittografia usando Azure PowerShell, l'interfaccia della riga di comando di Azure o un modello di Gestione risorse. 

>[!IMPORTANT]
>La disabilitazione della crittografia con Crittografia dischi di Azure nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportata nei volumi di dati o sistemi operativi se il volume del sistema operativo è stato crittografato. 

- **Disabilitare la crittografia del disco con Azure PowerShell:** Per disabilitare la crittografia, usare il cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Disabilitare la crittografia con l'interfaccia della riga di comando di Azure:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Disabilitare la crittografia con un modello di gestione risorse:** Per disabilitare la crittografia, usare il modello [disabilitare la crittografia in un modello di macchina virtuale Linux in esecuzione](https://aka.ms/decrypt-linuxvm) .
     1. Selezionare **Distribuisci in Azure**.
     2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione, la macchina virtuale, i termini legali e il contratto.
     3. Selezionare **Acquista** per disabilitare la crittografia del disco in una macchina virtuale Windows in esecuzione. 


## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di crittografia dischi di Azure per Linux](disk-encryption-overview-aad.md)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure con Azure AD (versione precedente)](disk-encryption-key-vault-aad.md)
