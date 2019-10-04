---
title: Script di esempio di crittografia dischi di Azure
description: Questo articolo è l'appendice per la crittografia del disco Microsoft Azure per le macchine virtuali Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 088ca5c20b0681cdd36da1b8a187873399aa32c6
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828453"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Script di esempio di crittografia dischi di Azure 

Questo articolo fornisce script di esempio per la preparazione dei dischi rigidi virtuali pre-crittografati e di altre attività.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Esempi di script di PowerShell per Crittografia dischi di Azure 

- **Elencare tutte le macchine virtuali crittografate nella sottoscrizione**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Elencare tutti i segreti di crittografia dischi usati per crittografare le macchine virtuali in un insieme di credenziali delle chiavi** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Uso dello script di PowerShell dei prerequisiti di crittografia dischi di Azure
Se si ha già familiarità con i prerequisiti per Crittografia dischi di Azure, è possibile usare lo [script di PowerShell per i prerequisiti di Crittografia dischi di Azure](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Per un esempio d'uso di questo script di PowerShell, vedere [Guida introduttiva alla crittografia di una macchina virtuale](disk-encryption-powershell-quickstart.md). È possibile rimuovere i commenti da una sezione dello script, a partire dalla riga 211, per crittografare tutti i dischi per le macchine virtuali esistenti in un gruppo di risorse esistente. 

La tabella seguente illustra i parametri che possono essere usati nello script di PowerShell: 


|Parametro|Descrizione|Obbligatorio?|
|------|------|------|
|$resourceGroupName| Nome del gruppo di risorse a cui appartiene l'insieme di credenziali delle chiavi.  Verrà creato un nuovo gruppo di risorse con questo nome, se non esiste già.| True|
|$keyVaultName|Nome dell'insieme di credenziali delle chiavi in cui inserire le chiavi di crittografia. Verrà creato un nuovo insieme con questo nome, se non esiste già.| True|
|$location|Percorso dell'insieme di credenziali delle chiavi. Assicurarsi che l'insieme di credenziali delle chiavi e le macchine virtuali da crittografare si trovino nello stesso percorso. Ottenere un elenco di percorsi con `Get-AzLocation`.|True|
|$subscriptionId|Identificatore della sottoscrizione di Azure da usare.  È possibile ottenere l'ID della sottoscrizione con `Get-AzSubscription`.|True|
|$aadAppName|Nome dell'applicazione Azure AD che verrà usata per scrivere segreti nell'insieme di credenziali delle chiavi. Viene creata una nuova applicazione con questo nome, se non esiste già. Se l'app esiste già, passare il parametro aadClientSecret allo script.|False|
|$aadClientSecret|Segreto client dell'applicazione Azure AD che è stato creato in precedenza.|False|
|$keyEncryptionKeyName|Nome della chiave di crittografia della chiave facoltativa nell'insieme di credenziali delle chiavi. Verrà creata una nuova chiave con questo nome, se non esiste già.|False|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Crittografare o decrittografare le macchine virtuali senza un'app Azure AD

- [Abilitare la crittografia del disco in una VM Linux esistente o in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Disabilitare la crittografia su macchine virtuali Linux in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - È possibile disabilitare la crittografia solo nei volumi di dati per macchine virtuali Linux.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Crittografare o decrittografare le macchine virtuali senza un'app Azure AD (versione precedente) 
 
- [Abilitare la crittografia del disco in una VM Linux esistente o in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Disabilitare la crittografia su macchine virtuali Linux in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - È possibile disabilitare la crittografia solo nei volumi di dati per macchine virtuali Linux. 


- [Creare un nuovo disco gestito crittografato da un BLOB di archiviazione/disco rigido virtuale pre-crittografato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Crea un nuovo disco gestito crittografato a partire da un disco rigido virtuale pre-crittografato con le corrispondenti impostazioni di crittografia





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Crittografia di un'unità del sistema operativo in una VM Linux in esecuzione

### <a name="prerequisites-for-os-disk-encryption"></a>Prerequisiti per la crittografia del disco del sistema operativo

* La macchina virtuale deve usare una distribuzione compatibile con la crittografia del disco del sistema operativo come elencato nei [sistemi operativi supportati da crittografia dischi di Azure](disk-encryption-overview.md#supported-vm-sizes) 
* La macchina virtuale deve essere creata dall'immagine del Marketplace in Azure Resource Manager.
* VM di Azure con almeno 4 GB di RAM (7 GB consigliati).
* (Per RHEL e CentOS) Disabilitare SELinux. Per disabilitare SELinux, vedere "4.4.2. Disabling SELinux" (4.4.2. Disabilitazione di SELinux) in [SELinux User's and Administrator's Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) (Manuale dell'utente e dell'amministratore di SELinux) nella VM.
* Dopo la disabilitazione di SELinux, riavviare la VM almeno una volta.

### <a name="steps"></a>Passaggi
1. Creare una macchina virtuale usando una delle distribuzioni specificate in precedenza.

   Per CentOS 7.2, la crittografia del disco del sistema operativo è supportata tramite un'immagine speciale. Per usare questa immagine, specificare "7.2n" come SKU durante la creazione della VM:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Configurare la VM in base alle esigenze. Se si intende crittografare tutte le unità (sistema operativo e dati), le unità dati dovranno essere specificate e montabili da /etc/fstab.

   > [!NOTE]
   > Usare UUID=... per specificare le unità di dati in /etc/fstab anziché specificare il nome del dispositivo a blocchi, ad esempio /dev/sdb1. L'ordine delle unità viene modificato nella macchina virtuale durante la crittografia. Se la VM si basa su un ordine specifico di dispositivi a blocchi, questi dispositivi non potranno essere montati dopo la crittografia.

3. Disconnettersi dalle sessioni SSH.

4. Per crittografare il sistema operativo, specificare il parametro volumeType come **Tutto** oppure **OS** quando si abilita la crittografia.

   > [!NOTE]
   > Tutti i processi dello spazio dell'utente non in esecuzione come servizi `systemd` devono essere terminati con `SIGKILL`. Riavviare la macchina virtuale. Quando si abilita la crittografia del disco del sistema operativo in una macchina virtuale in esecuzione, pianificare i tempi di inattività della VM.

5. Monitorare periodicamente lo stato della crittografia tramite le istruzioni indicate nella [sezione successiva](#monitoring-os-encryption-progress).

6. Dopo che Get-AzVmDiskEncryptionStatus Mostra "VMRestartPending", riavviare la macchina virtuale eseguendo l'accesso o usando il portale, PowerShell o l'interfaccia della riga di comando.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Prima di riavviare, è consigliabile salvare i dati di [diagnostica di avvio](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) della macchina virtuale.

## <a name="monitoring-os-encryption-progress"></a>Monitoraggio dello stato della crittografia del sistema operativo
Esistono tre modi per monitorare lo stato della crittografia del sistema operativo:

* Usare il cmdlet `Get-AzVmDiskEncryptionStatus` ed esaminare il campo ProgressMessage:
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

  ![Visualizzazione dell'istanza della VM](./media/disk-encryption/vm-instanceview.png)

* Esaminare la [diagnostica di avvio](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). I messaggi provenienti dall'estensione ADE hanno il prefisso `[AzureDiskEncryption]`.

* Accedere alla VM tramite SSH e ottenere il log di estensione da:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  È consigliabile non accedere alla macchina virtuale mentre è in corso la crittografia del sistema operativo. Copiare i log solo in caso di errore degli altri due metodi.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Preparare un disco rigido virtuale Linux pre-crittografato
La preparazione di dischi rigidi virtuali pre-crittografati può variare a seconda della distribuzione. Sono disponibili esempi di preparazione di Ubuntu 16, openSUSE 13,2 e CentOS 7. 

### <a name="ubuntu-16"></a>Ubuntu 16
Configurare la crittografia durante l'installazione della distribuzione seguendo questa procedura:

1. Selezionare **Configure encrypted volumes** (Configura volumi crittografati) durante il partizionamento dei dischi.

   ![Configurazione di Ubuntu 16.04 - Configurare i volumi crittografati](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Creare un'unità di avvio separata che non deve essere crittografata. Crittografare l'unità radice.

   ![Configurazione di Ubuntu 16.04 - Selezionare i dispositivi da crittografare](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Specificare una passphrase. Si tratta della passphrase caricata nell'insieme di credenziali delle chiavi.

   ![Configurazione di Ubuntu 16.04 - Specificare la passphrase](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Terminare il partizionamento.

   ![Configurazione di Ubuntu 16.04 - Terminare il partizionamento](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Quando si avvia la macchina virtuale e viene richiesta una passphrase, usare la passphrase specificata nel passaggio 3.

   ![Configurazione di Ubuntu 16.04 - Specificare la passphrase all'avvio](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

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

   ![Configurazione di Ubuntu 16.04 - update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Continuare con il passaggio successivo e caricare il disco rigido virtuale in Azure.

### <a name="opensuse-132"></a>openSUSE 13.2
Per configurare la crittografia durante l'installazione della distribuzione, eseguire i passaggi seguenti:
1. Durante il partizionamento dei dischi, selezionare **Encrypt Volume Group** (Crittografa gruppo di volumi), quindi immettere una password. Questa è la password che verrà caricata nell'insieme di credenziali delle chiavi.

   ![Configurazione di openSUSE 13.2 - Crittografare un gruppo di volumi](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Avviare la VM usando la password.

   ![Configurazione di openSUSE 13.2 - Specificare la passphrase all'avvio](./media/disk-encryption/opensuse-encrypt-fig2.png)

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

### <a name="centos-7"></a>CentOS 7
Per configurare la crittografia durante l'installazione della distribuzione, eseguire i passaggi seguenti:
1. Selezionare **Encrypt my data** (Crittografa dati personali) durante il partizionamento dei dischi.

   ![Configurazione di CentOS 7 - Destinazione di installazione](./media/disk-encryption/centos-encrypt-fig1.png)

2. Assicurarsi **Encrypt** (Crittografa) sia selezionato per la partizione radice.

   ![Configurazione di CentOS 7 - Selezionare la crittografia per la partizione radice](./media/disk-encryption/centos-encrypt-fig2.png)

3. Specificare una passphrase. Questa è la passphrase che verrà caricata nell'insieme di credenziali delle chiavi.

   ![Configurazione di CentOS 7 - Specificare la passphrase](./media/disk-encryption/centos-encrypt-fig3.png)

4. Quando si avvia la macchina virtuale e viene richiesta una passphrase, usare la passphrase specificata nel passaggio 3.

   ![Configurazione di CentOS 7 - Immettere la passphrase all'avvio](./media/disk-encryption/centos-encrypt-fig4.png)

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

    ![Configurazione di CentOS 7 - run /usr/sbin/dracut -f -v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Caricare il VHD crittografato in un account di archiviazione di Azure
Dopo l'abilitazione della crittografia DM-Crypt, il disco rigido virtuale crittografato locale dovrà essere caricato nell'account di archiviazione.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Caricare il segreto per la VM pre-crittografata nell'insieme di credenziali delle chiavi
Quando si esegue la crittografia usando un'app Azure AD, il segreto di crittografia del disco ottenuto in precedenza deve essere caricato come segreto nell'insieme di credenziali delle chiavi. L'insieme di credenziali delle chiavi deve avere la crittografia dei dischi e le autorizzazioni abilitate per il client di Azure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Segreto di crittografia del disco non crittografato con una chiave di crittografia della chiave
Per configurare il segreto nell'insieme di credenziali delle chiavi, usare [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). La passphrase viene codificata come stringa Base64 e quindi caricata nell'insieme di credenziali delle chiavi. Assicurarsi anche che i tag seguenti siano impostati quando si crea il segreto nell'insieme di credenziali delle chiavi.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Usare `$secretUrl` nel passaggio successivo per [collegare il disco del sistema operativo senza usare una chiave di crittografia della chiave](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Segreto di crittografia del disco crittografato con una chiave di crittografia della chiave
Prima di caricare il segreto nell'insieme di credenziali delle chiavi, è possibile crittografarlo usando una chiave di crittografia della chiave. Usare l'[API](https://msdn.microsoft.com/library/azure/dn878066.aspx) WRAP per crittografare prima di tutto il segreto con la chiave di crittografia della chiave. L'output di questa operazione WRAP si basa su una stringa con codifica Base 64 dell'URL che può essere quindi caricata come segreto con il cmdlet [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret).

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

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

Usare `$KeyEncryptionKey` e `$secretUrl` nel passaggio successivo per [collegare il disco del sistema operativo usando una chiave di crittografia della chiave](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Specificare un URL del segreto quando si collega un disco del sistema operativo

###  <a name="without-using-a-kek"></a>Senza l'uso di una chiave di crittografia della chiave (KEK)
Quando si collega il disco del sistema operativo è necessario passare `$secretUrl`. L'URL è stato generato nella sezione "Segreto di crittografia del disco non crittografato con una chiave di crittografia della chiave".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Uso di una chiave di crittografia della chiave (KEK)
Quando si collega il disco del sistema operativo, vedere `$KeyEncryptionKey` e `$secretUrl`. L'URL è stato generato nella sezione "Segreto di crittografia del disco crittografato con una chiave di crittografia della chiave".
```powershell
    Set-AzVMOSDisk `
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
