---
title: Appendice - Crittografia dischi di Azure per le macchine virtuali IaaS | Microsoft Docs
description: Questo articolo rappresenta un'appendice per Crittografia dischi di Azure per macchine virtuali IaaS Windows e Linux.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 01/14/2019
ms.custom: seodec18
ms.openlocfilehash: 64ae354c9233821ea7e53abfdc0dde105b22e466
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208075"
---
# <a name="appendix-for-azure-disk-encryption"></a>Appendice per Crittografia dischi di Azure 

Questo articolo rappresenta un'appendice per [Crittografia dischi di Azure per macchine virtuali IaaS](azure-security-disk-encryption-overview.md). Assicurarsi di aver letto innanzitutto gli articoli sulla Crittografia dischi di Azure per macchine virtuali per comprendere il contesto. Questo articolo descrive come preparare dischi rigidi virtuali pre-crittografati e altre attività.

## <a name="connect-to-your-subscription"></a>Eseguire la connessione alla sottoscrizione
Prima di iniziare, vedere l'articolo [Prerequisiti](azure-security-disk-encryption-prerequisites.md). Dopo che tutti i prerequisiti risultano rispettati, connettersi alla sottoscrizione eseguendo i cmdlet seguenti:

### <a name="bkmk_ConnectPSH"></a> Connettersi alla sottoscrizione con PowerShell

1. Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:

     ```powershell
     Connect-AzureRmAccount 
     ```
2. Se sono disponibili più sottoscrizioni e se ne vuole specificare una da usare, digitare quanto segue per visualizzare le sottoscrizioni per il proprio account:
     
     ```powershell
     Get-AzureRmSubscription
     ```
3. Per specificare la sottoscrizione che si vuole usare, digitare:
 
     ```powershell
      Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>
     ```
4. Per verificare che la sottoscrizione configurata sia corretta, digitare:
     
     ```powershell
     Get-AzureRmSubscription
     ```
5. Se necessario, connettersi ad Azure AD con [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```
6. Per verificare che i cmdlet di Crittografia dischi di Azure siano installati, digitare:
     
     ```powershell
     Get-command *diskencryption*
     ```
                       
7. Se necessario, vedere [Introduzione ad Azure PowerShell](/powershell/azure/get-started-azureps) e [AzureAD](/powershell/module/azuread).

### <a name="bkmk_ConnectCLI"></a> Connettersi alla sottoscrizione di Azure tramite l'Interfaccia della riga di comando di Azure

1. Accedere ad Azure con [az login](/cli/azure/authenticate-azure-cli#sign-in-interactively). 
     
     ```azurecli
     az login
     ```

2. Per selezionare un tenant per l'accesso, usare:
    
     ```azurecli
     az login --tenant <tenant>
     ```

3. In caso di più sottoscrizioni, per specificarne una in particolare è necessario ottenere l'elenco di sottoscrizioni tramite [az account list](/cli/azure/account#az-account-list) (elenco account di accesso di AZ) e specificarla da [az account set](/cli/azure/account#az-account-set) (imposta account di accesso di AZ).
     
     ```azurecli
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Verificare la versione installata.
     
     ```azurecli
        az --version
     ``` 

5. Se necessario, vedere [Introduzione all'interfaccia della riga di comando di Azure 2.0](/cli/azure/get-started-with-azure-cli). 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Esempi di script di PowerShell per Crittografia dischi di Azure 

- **Elencare tutte le macchine virtuali crittografate nella sottoscrizione**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzureRmVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Elencare tutti i segreti di crittografia dischi usati per crittografare le macchine virtuali in un insieme di credenziali delle chiavi** 

     ```azurepowershell-interactive
     Get-AzureKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="bkmk_prereq-script"></a> Uso dello script di PowerShell per i prerequisiti di Crittografia dischi di Azure
Se si ha già familiarità con i prerequisiti per Crittografia dischi di Azure, è possibile usare lo [script di PowerShell per i prerequisiti di Crittografia dischi di Azure](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Per un esempio d'uso di questo script di PowerShell, vedere [Guida introduttiva alla crittografia di una macchina virtuale](quick-encrypt-vm-powershell.md). È possibile rimuovere i commenti da una sezione dello script, a partire dalla riga 211, per crittografare tutti i dischi per le macchine virtuali esistenti in un gruppo di risorse esistente. 

La tabella seguente illustra i parametri che possono essere usati nello script di PowerShell: 


|Parametro|DESCRIZIONE|È obbligatorio|
|------|------|------|
|$resourceGroupName| Nome del gruppo di risorse a cui appartiene l'insieme di credenziali delle chiavi.  Verrà creato un nuovo gruppo di risorse con questo nome, se non esiste già.| True |
|$keyVaultName|Nome dell'insieme di credenziali delle chiavi in cui inserire le chiavi di crittografia. Verrà creato un nuovo insieme con questo nome, se non esiste già.| True |
|$location|Percorso dell'insieme di credenziali delle chiavi. Assicurarsi che l'insieme di credenziali delle chiavi e le macchine virtuali da crittografare si trovino nello stesso percorso. Ottenere un elenco di percorsi con `Get-AzureRMLocation`.|True |
|$subscriptionId|Identificatore della sottoscrizione di Azure da usare.  È possibile ottenere l'ID della sottoscrizione con `Get-AzureRMSubscription`.|True |
|$aadAppName|Nome dell'applicazione Azure AD che verrà usata per scrivere segreti nell'insieme di credenziali delle chiavi. Viene creata una nuova applicazione con questo nome, se non esiste già. Se l'app esiste già, passare il parametro aadClientSecret allo script.|False|
|$aadClientSecret|Segreto client dell'applicazione Azure AD che è stato creato in precedenza.|False|
|$keyEncryptionKeyName|Nome della chiave di crittografia della chiave facoltativa nell'insieme di credenziali delle chiavi. Verrà creata una nuova chiave con questo nome, se non esiste già.|False|


## <a name="resource-manager-templates"></a>Modelli di Gestione risorse

<!--   - [Create a key vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) -->

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Crittografare o decrittografare le macchine virtuali senza un'app Azure AD


- [Abilitare la crittografia del disco su macchine virtuali IaaS Windows esistenti](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)
- [Disabilitare la crittografia dei dischi su macchine virtuali IaaS Windows esistenti o in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
- [Abilitare la crittografia del disco su macchine virtuali IaaS Linux esistenti](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
 -  [Disabilitare la crittografia su macchine virtuali Linux in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - È possibile disabilitare la crittografia solo nei volumi di dati per macchine virtuali Linux.  

### <a name="encrypt-or-decrypt-vm-scale-sets"></a>Crittografare o decrittografare set di scalabilità di macchine virtuali

- [Abilitare la crittografia dei dischi in un set di scalabilità di macchine virtuali Linux in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Abilitare la crittografia dei dischi in un set di scalabilità di macchine virtuali Windows in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

 - [Distribuire un set di scalabilità di macchine virtuali Linux con una jumpbox e abilitare la crittografia nel set di scalabilità](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

 - [Distribuire un set di scalabilità di macchine virtuali Windows con una jumpbox e abilitare la crittografia nel set di scalabilità](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Disabilitare la crittografia dei dischi in un set di scalabilità di macchine virtuali Linux in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Disabilitare la crittografia dei dischi in un set di scalabilità di macchine virtuali Windows in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Crittografare o decrittografare le macchine virtuali senza un'app Azure AD (versione precedente) 
 
- [Abilitare la crittografia del disco su macchine virtuali IaaS Windows esistenti](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

- [Abilitare la crittografia del disco su macchine virtuali IaaS Linux esistenti](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    

- [Disabilitare la crittografia del disco su macchine virtuali IaaS Windows in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 

-  [Disabilitare la crittografia su macchine virtuali Linux in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - È possibile disabilitare la crittografia solo nei volumi di dati per macchine virtuali Linux. 

- [Abilitare la crittografia dischi nella nuova macchina virtuale IaaS Windows da Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)
    - Questo modello crea una nuova VM Windows crittografata che usa l'immagine della raccolta di Windows Server 2012.

- [Creare una nuova macchina virtuale crittografata con disco gestito IaaS Windows da un'immagine della raccolta](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)
    - Questo modello crea una nuova macchina virtuale Windows crittografata con dischi gestiti usando l'immagine della raccolta di Windows Server 2012.

- [Distribuzione di RHEL 7.2 con crittografia dei dischi completa usando dischi gestiti](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)
    - Questo modello crea in Azure una macchina virtuale RHEL 7.2 con crittografia completa usando dischi gestiti. Include un'unità di sistema operativo da 30 GB crittografata e una matrice da 200 GB (RAID-0) crittografata montata su /mnt/raidencrypted. Per le distribuzioni di server Linux supportate, vedere l'articolo [Domande frequenti](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport). 

- [Distribuzione di RHEL 7.2 con crittografia dei dischi completa usando dischi non gestiti](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel-unmanaged)
    - Questo modello crea in Azure una macchina virtuale RHEL 7.2 con crittografia completa con un'unità di sistema operativo da 30 GB crittografata e una matrice da 200 GB (RAID-0) crittografata montata su /mnt/raidencrypted. Per le distribuzioni di server Linux supportate, vedere l'articolo [Domande frequenti](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport). 

- [Abilitare la crittografia del disco in un disco rigido virtuale per Windows o Linux pre-crittografato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)

- [Creare un nuovo disco gestito crittografato da un BLOB di archiviazione/disco rigido virtuale pre-crittografato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Crea un nuovo disco gestito crittografato a partire da un disco rigido virtuale pre-crittografato con le corrispondenti impostazioni di crittografia

- [Abilitare la crittografia dei dischi in una VM Windows in esecuzione con un'identificazione personale del certificato client di Azure AD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-aad-client-cert)
    


## <a name="bkmk_preWin"></a> Preparare un disco rigido virtuale Windows pre-crittografato
Le sezioni seguenti sono necessarie per preparare un disco rigido virtuale Windows pre-crittografato per la distribuzione come disco rigido virtuale crittografato in Azure IaaS. Usare le informazioni per preparare e avviare una nuova macchina virtuale Windows VM (disco rigido virtuale) in Azure Site Recovery o Azure. Per altre informazioni su come preparare e caricare un disco rigido virtuale, vedere [Caricare un disco rigido virtuale generalizzato e usarlo per creare nuove macchine virtuali in Azure](../virtual-machines/windows/upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aggiornare i criteri di gruppo per consentire la protezione non TPM del sistema operativo
Configurare l'impostazione **Crittografia unità BitLocker** di Criteri di gruppo per BitLocker, disponibile in **Criteri del computer locale** > **Configurazione computer** > **Modelli amministrativi** > **Componenti di Windows**. Cambiare questa impostazione in **Unità del sistema operativo** > **Richiedi autenticazione aggiuntiva all'avvio** > **Consenti BitLocker senza un TPM compatibile**, come illustrato nella figura seguente:

![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Installare i componenti della funzionalità BitLocker
Per Windows Server 2012 e versioni successive, usare il comando seguente:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Per Windows Server 2008 R2, usare il comando seguente:

    ServerManagerCmd -install BitLockers
### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Preparare il volume del sistema operativo per BitLocker tramite `bdehdcfg`
Per comprimere la partizione del sistema operativo e preparare il computer per BitLocker, eseguire il comando [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment), se necessario:

    bdehdcfg -target c: shrink -quiet 


### <a name="protect-the-os-volume-by-using-bitlocker"></a>Proteggere il volume del sistema operativo usando BitLocker
Usare il comando [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) per abilitare la crittografia sul volume di avvio usando una protezione con chiave esterna. Salvare anche la chiave esterna (file con estensione bek) nell'unità o nel volume esterno. La crittografia viene abilitata nel volume di sistema/di avvio al riavvio successivo.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Preparare la macchina virtuale con un disco rigido virtuale dati/di risorse separato per recuperare la chiave esterna usando BitLocker.

## <a name="bkmk_LinuxRunning"></a> Crittografia di un'unità del sistema operativo su una macchina virtuale Linux in esecuzione

### <a name="prerequisites-for-os-disk-encryption"></a>Prerequisiti per la crittografia del disco del sistema operativo

* La macchina virtuale deve usare una distribuzione compatibile con la crittografia del disco del sistema operativo, come descritto nelle [domande frequenti su Crittografia dischi di Azure](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 
* La macchina virtuale deve essere creata dall'immagine del Marketplace in Azure Resource Manager.
* VM di Azure con almeno 4 GB di RAM (7 GB consigliati).
* (Per RHEL e CentOS) Disabilitare SELinux. Per disabilitare SELinux, vedere "4.4.2. Disabling SELinux" (4.4.2. Disabilitazione di SELinux) in [SELinux User's and Administrator's Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) (Manuale dell'utente e dell'amministratore di SELinux) nella VM.
* Dopo la disabilitazione di SELinux, riavviare la VM almeno una volta.

### <a name="steps"></a>Passaggi
1. Creare una macchina virtuale usando una delle distribuzioni specificate in precedenza.

 Per CentOS 7.2, la crittografia del disco del sistema operativo è supportata tramite un'immagine speciale. Per usare questa immagine, specificare "7.2n" come SKU durante la creazione della VM:

 ```powershell
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Configurare la VM in base alle esigenze. Se si intende crittografare tutte le unità (sistema operativo e dati), le unità dati dovranno essere specificate e montabili da /etc/fstab.

 > [!NOTE]
 > Usare UUID=... per specificare le unità di dati in /etc/fstab anziché specificare il nome del dispositivo a blocchi, ad esempio /dev/sdb1. L'ordine delle unità viene modificato nella macchina virtuale durante la crittografia. Se la VM si basa su un ordine specifico di dispositivi a blocchi, questi dispositivi non potranno essere montati dopo la crittografia.

3. Disconnettersi dalle sessioni SSH.

4. Per crittografare il sistema operativo, specificare il parametro volumeType come **Tutto** oppure **OS** quando si abilita la crittografia.

 > [!NOTE]
 > Tutti i processi dello spazio dell'utente non in esecuzione come servizi `systemd` devono essere terminati con `SIGKILL`. Riavviare la macchina virtuale. Quando si abilita la crittografia del disco del sistema operativo in una macchina virtuale in esecuzione, pianificare i tempi di inattività della VM.

5. Monitorare periodicamente lo stato della crittografia tramite le istruzioni indicate nella [sezione successiva](#monitoring-os-encryption-progress).

6. Quando Get-AzureRmVmDiskEncryptionStatus indica "VMRestartPending", riavviare la macchina virtuale eseguendo l'accesso alla macchina oppure usando il portale, PowerShell o l'interfaccia della riga di comando.
    ```powershell
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Prima di riavviare, è consigliabile salvare i dati di [diagnostica di avvio](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) della macchina virtuale.

## <a name="monitoring-os-encryption-progress"></a>Monitoraggio dello stato della crittografia del sistema operativo
Esistono tre modi per monitorare lo stato della crittografia del sistema operativo:

* Usare il cmdlet `Get-AzureRmVmDiskEncryptionStatus` ed esaminare il campo ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Quando la macchina virtuale raggiunge lo stato "OS disk encryption started", sono necessari circa 40 o 50 minuti in una macchina virtuale con archiviazione Premium.

 A causa dell'[errore #388](https://github.com/Azure/WALinuxAgent/issues/388) in WALinuxAgent, `OsVolumeEncrypted` e `DataVolumesEncrypted`vengono visualizzati come `Unknown` in alcune distribuzioni. Con WALinuxAgent 2.1.5 e versioni successive questo errore viene risolto automaticamente. Se viene visualizzato `Unknown` nell'output, è possibile verificare lo stato della crittografia del disco usando Esplora risorse di Azure.

 Passare a [Esplora risorse di Azure](https://resources.azure.com/), quindi espandere questa gerarchia nel pannello di selezione a sinistra:

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 In InstanceView scorrere verso il basso per visualizzare lo stato della crittografia delle unità.

 ![Visualizzazione dell'istanza della VM](./media/azure-security-disk-encryption/vm-instanceview.png)

* Esaminare la [diagnostica di avvio](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). I messaggi provenienti dall'estensione ADE hanno il prefisso `[AzureDiskEncryption]`.

* Accedere alla VM tramite SSH e ottenere il log di estensione da:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 È consigliabile non accedere alla macchina virtuale mentre è in corso la crittografia del sistema operativo. Copiare i log solo in caso di errore degli altri due metodi.

## <a name="bkmk_preLinux"></a> Preparare un disco rigido virtuale Linux pre-crittografato
La preparazione di dischi rigidi virtuali pre-crittografati può variare a seconda della distribuzione. Sono disponibili esempi di preparazione [Ubuntu 16](#bkmk_Ubuntu), [openSUSE 13.2](#bkmk_openSUSE) e [CentOS 7](#bkmk_CentOS). 

### <a name="bkmk_Ubuntu"></a> Ubuntu 16
Configurare la crittografia durante l'installazione della distribuzione seguendo questa procedura:

1. Selezionare **Configure encrypted volumes** (Configura volumi crittografati) durante il partizionamento dei dischi.

 ![Configurazione di Ubuntu 16.04 - Configurare i volumi crittografati](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Creare un'unità di avvio separata che non deve essere crittografata. Crittografare l'unità radice.

 ![Configurazione di Ubuntu 16.04 - Selezionare i dispositivi da crittografare](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Specificare una passphrase. Si tratta della passphrase caricata nell'insieme di credenziali delle chiavi.

 ![Configurazione di Ubuntu 16.04 - Specificare la passphrase](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Terminare il partizionamento.

 ![Configurazione di Ubuntu 16.04 - Terminare il partizionamento](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Quando si avvia la macchina virtuale e viene richiesta una passphrase, usare la passphrase specificata nel passaggio 3.

 ![Configurazione di Ubuntu 16.04 - Specificare la passphrase all'avvio](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Preparare la macchina virtuale per il caricamento in Azure seguendo [queste istruzioni](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Non eseguire ancora l'ultimo passaggio, ovvero il deprovisioning della VM.

Configurare la crittografia per l'uso in Azure eseguendo i passaggi seguenti:

1. Creare un file in /usr/local/sbin/azure_crypt_key.sh, con il contenuto dello script seguente. Prestare attenzione a KeyFileName perché è il nome file della passphrase usato da Azure.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. Modificare la configurazione di crittografia in */etc/crypttab*. L'aspetto dovrebbe risultare simile al seguente:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Se si modifica *azure_crypt_key.sh* in Windows e questo valore è stato copiato in Linux, eseguire `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Aggiungere autorizzazioni di esecuzione allo script:
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Modificare */etc/initramfs-tools/modules* accodando le righe:
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Eseguire `update-initramfs -u -k all` per aggiornare initramfs e rendere operativo il `keyscript`.

7. È ora possibile effettuare il deprovisioning della VM.

 ![Configurazione di Ubuntu 16.04 - update-initramfs](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Continuare con il passaggio successivo e caricare il disco rigido virtuale in Azure.

### <a name="bkmk_openSUSE"></a> openSUSE 13.2
Per configurare la crittografia durante l'installazione della distribuzione, eseguire i passaggi seguenti:
1. Durante il partizionamento dei dischi, selezionare **Encrypt Volume Group** (Crittografa gruppo di volumi), quindi immettere una password. Questa è la password che verrà caricata nell'insieme di credenziali delle chiavi.

 ![Configurazione di openSUSE 13.2 - Crittografare un gruppo di volumi](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Avviare la VM usando la password.

 ![Configurazione di openSUSE 13.2 - Specificare la passphrase all'avvio](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Preparare la macchina virtuale per il caricamento in Azure seguendo le istruzioni disponibili in [Preparare una macchina virtuale SLES oppure openSUSE per Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Non eseguire ancora l'ultimo passaggio, ovvero il deprovisioning della VM.

Per configurare la crittografia per l'uso in Azure, eseguire i passaggi seguenti:
1. Modificare il file /etc/dracut.conf e aggiungere la riga seguente:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Impostare come commento queste righe verso la fine del file /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
 ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. Aggiungere la riga seguente all'inizio del file /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
 ```bash
    DRACUT_SYSTEMD=0
 ```
Modificare quindi tutte le occorrenze di:
 ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
in:
```bash
    if [ 1 ]; then
```
4. Modificare /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e aggiungere questo codice dopo "# Open LUKS device":

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Eseguire `/usr/sbin/dracut -f -v` per aggiornare initrd.

6. È ora possibile effettuare il deprovisioning della macchina virtuale e caricare il disco rigido virtuale in Azure.

### <a name="bkmk_CentOS"></a> CentOS 7
Per configurare la crittografia durante l'installazione della distribuzione, eseguire i passaggi seguenti:
1. Selezionare **Encrypt my data** (Crittografa dati personali) durante il partizionamento dei dischi.

 ![Configurazione di CentOS 7 - Destinazione di installazione](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Assicurarsi **Encrypt** (Crittografa) sia selezionato per la partizione radice.

 ![Configurazione di CentOS 7 - Selezionare la crittografia per la partizione radice](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Specificare una passphrase. Questa è la passphrase che verrà caricata nell'insieme di credenziali delle chiavi.

 ![Configurazione di CentOS 7 - Specificare la passphrase](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Quando si avvia la macchina virtuale e viene richiesta una passphrase, usare la passphrase specificata nel passaggio 3.

 ![Configurazione di CentOS 7 - Immettere la passphrase all'avvio](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Preparare la macchina virtuale per il caricamento in Azure usando le istruzioni relative a "CentOS 7.0+" disponibili in [Preparare una macchina virtuale basata su CentOS per Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Non eseguire ancora l'ultimo passaggio, ovvero il deprovisioning della VM.

6. È ora possibile effettuare il deprovisioning della macchina virtuale e caricare il disco rigido virtuale in Azure.

Per configurare la crittografia per l'uso in Azure, eseguire i passaggi seguenti:

1. Modificare il file /etc/dracut.conf e aggiungere la riga seguente:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Impostare come commento queste righe verso la fine del file /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. Aggiungere la riga seguente all'inizio del file /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
```bash
    DRACUT_SYSTEMD=0
```
Modificare quindi tutte le occorrenze di:
```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
to
```bash
    if [ 1 ]; then
```
4. Modificare /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e aggiungere il codice seguente dopo "# Open LUKS device":
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Eseguire "/usr/sbin/dracut -f -v" per aggiornare initrd.

![Configurazione di CentOS 7 - run /usr/sbin/dracut -f -v](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

## <a name="bkmk_UploadVHD"></a> Caricare il disco rigido virtuale crittografato in un account di archiviazione di Azure
Dopo aver abilitato la crittografia BitLocker o la crittografia DM-Crypt, il disco rigido virtuale crittografato locale dovrà essere caricato nell'account di archiviazione.
```powershell
    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="bkmk_UploadSecret"></a> Caricare il segreto per la macchina virtuale pre-crittografata nell'insieme di credenziali delle chiavi
Quando si esegue la crittografia usando un'app Azure AD, il segreto di crittografia del disco ottenuto in precedenza deve essere caricato come segreto nell'insieme di credenziali delle chiavi. L'insieme di credenziali delle chiavi deve avere la crittografia dei dischi e le autorizzazioni abilitate per il client di Azure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="bkmk_SecretnoKEK"></a> Segreto di crittografia del disco non crittografato con una chiave di crittografia della chiave
Per configurare il segreto nell'insieme di credenziali delle chiavi, usare [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Se si ha una macchina virtuale Windows, il file con estensione bek viene codificato come stringa base64, quindi viene caricato nell'insieme di credenziali delle chiavi usando il cmdlet `Set-AzureKeyVaultSecret`. Per Linux la passphrase viene codificata come stringa Base 64 e quindi caricata nell'insieme di credenziali delle chiavi. Assicurarsi anche che i tag seguenti siano impostati quando si crea il segreto nell'insieme di credenziali delle chiavi.

#### <a name="windows-bek-file"></a>File Windows con estensione bek
```powershell
# Change the VM Name, key vault name, and specify the path to the BEK file.
$VMName ="MySecureVM"
$BEKFilepath = "C:\test\BEK\12345678-90AB-CDEF-A1B2-C3D4E5F67890A.BEK"
$VeyVaultName ="MySecureVault"

# Get the name of the BEK file from the BEK file path. This will be a tag for the key vault secret.
$BEKFileName =  Split-Path $BEKFilepath -Leaf

# These tags will be added to the key vault secret so you can easily see which BEK file belongs to which VM.
$tags = @{“MachineName” = “$VMName”;"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "$BEKFileName"}

# Convert the BEK file to a Base64 string.
$FileContentEncoded = [System.convert]::ToBase64String((Get-Content -Path $BEKFilepath -Encoding Byte))

# Create a new secret in the vault from the converted BEK file. 
# The file is converted to a secure string before import into the key vault

$SecretName = [guid]::NewGuid().ToString()
$SecureSecretValue = ConvertTo-SecureString $FileContentEncoded -AsPlainText -Force
$Secret = Set-AzureKeyVaultSecret -VaultName $VeyVaultName -Name $SecretName -SecretValue $SecureSecretValue -tags $tags

# Show the secret's URL and store it as a variable. This is used as -DiskEncryptionKeyUrl in Set-AzureRmVMOSDisk when you attach your OS disk. 
$SecretUrl=$secret.Id
$SecretUrl
```

#### <a name="linux"></a>Linux
```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Usare `$secretUrl` nel passaggio successivo per [collegare il disco del sistema operativo senza usare una chiave di crittografia della chiave](#bkmk_URLnoKEK).

### <a name="bkmk_SecretKEK"></a> Segreto di crittografia del disco crittografato con una chiave di crittografia della chiave (KEK)
Prima di caricare il segreto nell'insieme di credenziali delle chiavi, è possibile crittografarlo usando una chiave di crittografia della chiave. Usare l'[API](https://msdn.microsoft.com/library/azure/dn878066.aspx) WRAP per crittografare prima di tutto il segreto con la chiave di crittografia della chiave. L'output di questa operazione WRAP si basa su una stringa con codifica Base 64 dell'URL che può essere quindi caricata come segreto con il cmdlet [`Set-AzureKeyVaultSecret`](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret).

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Usare `$KeyEncryptionKey` e `$secretUrl` nel passaggio successivo per [collegare il disco del sistema operativo usando una chiave di crittografia della chiave](#BKMK_URLKEK).

##  <a name="bkmk_SecretURL"></a> Specificare un URL del segreto quando si collega un disco del sistema operativo

###  <a name="bkmk_URLnoKEK"></a>Senza l'uso di una chiave di crittografia della chiave (KEK)
Quando si collega il disco del sistema operativo è necessario passare `$secretUrl`. L'URL è stato generato nella sezione "Segreto di crittografia del disco non crittografato con una chiave di crittografia della chiave".
```powershell
    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="bkmk_URLKEK"></a>Con l'uso di una chiave di crittografia della chiave (KEK)
Quando si collega il disco del sistema operativo, vedere `$KeyEncryptionKey` e `$secretUrl`. L'URL è stato generato nella sezione "Segreto di crittografia del disco crittografato con una chiave di crittografia della chiave".
```powershell
    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
