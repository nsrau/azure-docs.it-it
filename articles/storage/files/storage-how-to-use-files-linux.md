---
title: Usare File di Azure con Linux | Microsoft Docs
description: Informazioni su come montare una condivisione file di Azure tramite SMB in Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2dc78c25c2cf63a510b9451c8d694795cd8a91eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060952"
---
# <a name="use-azure-files-with-linux"></a>Usare File di Azure con Linux
[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere montate nelle distribuzioni Linux usando il [client del kernel SMB](https://wiki.samba.org/index.php/LinuxCIFS). Questo articolo illustra due modi per montare una condivisione file di Azure: su richiesta con il comando `mount` e all'avvio creando una voce in `/etc/fstab`.

Il modo consigliato per montare una condivisione file di Azure in Linux consiste nell'usare SMB 3.0.The recommended way to mount an Azure file share on Linux is using SMB 3.0. Per impostazione predefinita, File di Azure richiede la crittografia in transito, che è supportata solo da SMB 3.0.By default, Azure Files requires encryption in transit, which is only supported by SMB 3.0. File di Azure supporta anche SMB 2.1, che non supporta la crittografia in transito, ma non è possibile montare le condivisioni file di Azure con SMB 2.1 da un'altra area di Azure o locale per motivi di sicurezza. A meno che l'applicazione non richieda specificamente SMB 2.1, non c'è motivo di usarla dal momento che le distribuzioni Linux più popolari e rilasciate di recente supportano SMB 3.0:  

| | SMB 2.1 <br>(Montaggio in macchine virtuali nella stessa area di Azure) | SMB 3.0 <br>(Montaggio in locale e tra più aree) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 10+ |
| openSUSE | 13.2+ | 42.3+ |
| SuSE Linux Enterprise Server | 12+ | 12 SP3+ |

Se si utilizza una distribuzione Linux non elencata nella tabella precedente, è possibile verificare se la distribuzione Linux supporta SMB 3.0 con crittografia controllando la versione del kernel Linux. SMB 3.0 con crittografia è stato aggiunto al kernel Linux versione 4.11. Il `uname` comando restituirà la versione del kernel Linux in uso:

```bash
uname -r
```

## <a name="prerequisites"></a>Prerequisiti
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Assicurarsi che il pacchetto cifs-utils sia installato.**  
    Il pacchetto cifs-utils può essere installato tramite l'utilità di gestione dei pacchetti nella distribuzione Linux scelta. 

    Nelle distribuzioni basate su **Ubuntu** e **Debian** usare l'utilità di gestione dei pacchetti `apt`:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    Su **Fedora**, **Red Hat Enterprise Linux 8 e** **CentOS 8 ,** utilizzare il gestore di `dnf` pacchetti:

    ```bash
    sudo dnf install cifs-utils
    ```

    Nelle versioni precedenti di **Red Hat Enterprise Linux** e **CentOS,** utilizzare il gestore di `yum` pacchetti:

    ```bash
    sudo yum install cifs-utils 
    ```

    In **openSUSE** usare l'utilità di gestione dei pacchetti `zypper`:

    ```bash
    sudo zypper install cifs-utils
    ```

    In altre distribuzioni usare l'utilità di gestione pacchetti appropriata o [compilare il codice sorgente](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **La versione più recente dell'interfaccia della riga di comando di Azure.** Per altre informazioni su come installare l'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) di Azure e selezionare il sistema operativo. Se si preferisce usare il modulo di Azure PowerShell in PowerShell 6 e versioni successive, è tuttavia possibile presentare le istruzioni riportate di seguito per l'interfaccia della riga di comando di Azure.If you prefer to use the Azure PowerShell module in PowerShell 6' , you may, however, the instructions below are presented for the Azure CLI.

* **Assicurarsi che la porta 445 sia aperta**: SMB comunica tramite la porta TCP 445. Verificare che il firewall non blocchi le porte TCP 445 dal computer client.  Sostituire<>del **gruppo di risorse** e<>**dell'account** di archiviazione
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Se la connessione ha avuto esito positivo, verrà visualizzato un output simile al seguente:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Se non è possibile aprire la porta 445 nella rete aziendale o non è possibile farlo da un ISP, è possibile utilizzare una connessione VPN o ExpressRoute per aggirare la porta 445. Per altre informazioni, vedere Considerazioni sulla rete per l'accesso diretto alla [condivisione file](storage-files-networking-overview.md)di Azure.For more information, see Networking considerations for direct Azure file share access ..

## <a name="mounting-azure-file-share"></a>Montaggio della condivisione file di AzureMounting Azure file share
Per usare una condivisione file di Azure con la distribuzione Linux, è necessario creare una directory da usare come punto di montaggio per la condivisione file di Azure.To use an Azure file share with your Linux distribution, you must create a directory to serve as the mount point for the Azure file share. Un punto di montaggio può essere creato in qualsiasi punto del sistema Linux, ma è una convenzione comune per creare questo in /mnt. Dopo il punto di `mount` montaggio, usare il comando per accedere alla condivisione file di Azure.After the mount point, you use the command to access the Azure file share.

Se lo si desidera, è possibile montare la stessa condivisione file di Azure su più punti di montaggio.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montare la condivisione file di Azure su richiesta con `mount`
1. Creare una cartella per il `<your-resource-group>` `<your-storage-account>`punto `<your-file-share>` di **montaggio**: Sostituire , e con le informazioni appropriate per l'ambiente in uso:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Usare il comando mount per montare la condivisione file**di Azure. Nell'esempio seguente, le autorizzazioni locali per file e cartelle Linux sono predefinite 0755, ovvero lettura, scrittura ed esecuzione per il proprietario (in base al proprietario del file/directory Linux), leggere ed eseguire per gli utenti del gruppo proprietario e leggere ed eseguire per altri utenti nel sistema. È possibile `uid` utilizzare `gid` le opzioni e mount per impostare l'ID utente e l'ID gruppo per il montaggio. È inoltre `dir_mode` possibile `file_mode` utilizzare e impostare autorizzazioni personalizzate come desiderato. Per ulteriori informazioni su come impostare le autorizzazioni, vedere [Notazione numerica UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) su Wikipedia. 

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > Il comando mount di cui sopra si monta con SMB 3.0. Se la distribuzione Linux non supporta SMB 3.0 con crittografia o solo SMB 2.1, è possibile eseguire il montaggio solo da una macchina virtuale di Azure all'interno della stessa area dell'account di archiviazione. Per montare la condivisione file di Azure in una distribuzione Linux che non supporta SMB 3.0 con crittografia, è necessario disabilitare la [crittografia in transito per l'account di archiviazione.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

Dopo che la condivisione file di Azure è stata completata, è possibile usare `sudo umount $mntPath` per smontare la condivisione.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Creare un punto di montaggio permanente per la condivisione file di Azure con `/etc/fstab`
1. **Creare una cartella per il punto**di montaggio : Una cartella per un punto di montaggio può essere creata in qualsiasi punto del file system, ma è una convenzione comune per creare questo in /mnt. Ad esempio, il comando seguente crea `<your-resource-group>` `<your-storage-account>`una `<your-file-share>` nuova directory, replace , , e con le informazioni appropriate per il proprio ambiente:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Creare un file di credenziali in cui archiviare nome utente (il nome dell'account di archiviazione) e password (la chiave dell'account di archiviazione) per la condivisione file.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Modificare le autorizzazioni nel file di credenziali in modo che solo l'account radice possa leggere o modificare il file della password.** Poiché la chiave dell'account di archiviazione è essenzialmente la password di un amministratore con privilegi elevati per l'account di archiviazione, è importante impostare autorizzazioni sul file che consentano l'accesso solo all'account radice al fine di evitare che utenti con privilegi inferiori possano recuperare la chiave dell'account di archiviazione.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Utilizzare il comando seguente per `/etc/fstab`aggiungere la riga seguente a **: Nell'esempio seguente, le autorizzazioni locali per file e cartelle Linux predefinite 0755, ovvero lettura, scrittura ed esecuzione per il proprietario (in base al file/proprietario Linux), leggere ed eseguire per gli utenti nel gruppo proprietario e leggere ed eseguire per gli altri nel sistema. È possibile `uid` utilizzare `gid` le opzioni e mount per impostare l'ID utente e l'ID gruppo per il montaggio. È inoltre `dir_mode` possibile `file_mode` utilizzare e impostare autorizzazioni personalizzate come desiderato. Per ulteriori informazioni su come impostare le autorizzazioni, vedere [Notazione numerica UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) su Wikipedia.

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > Il comando mount di cui sopra si monta con SMB 3.0. Se la distribuzione Linux non supporta SMB 3.0 con crittografia o solo SMB 2.1, è possibile eseguire il montaggio solo da una macchina virtuale di Azure all'interno della stessa area dell'account di archiviazione. Per montare la condivisione file di Azure in una distribuzione Linux che non supporta SMB 3.0 con crittografia, è necessario disabilitare la [crittografia in transito per l'account di archiviazione.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="securing-linux"></a>Protezione di Linux
Per montare una condivisione file di Azure in Linux, la porta 445 deve essere accessibile. Molte organizzazioni bloccano la porta 445 a causa dei rischi per la sicurezza associati a SMB 1. SMB 1, noto anche come CIFS (Common Internet File System), è un protocollo del file system legacy incluso in molte distribuzioni Linux. SMB 1 è un protocollo obsoleto, inefficiente e, soprattutto, non sicuro. La buona notizia è che file di Azure non supporta SMB 1 e a partire dalla versione kernel 4.18 di Linux, Linux consente di disabilitare SMB 1. Si [consiglia](https://aka.ms/stopusingsmb1) sempre di disabilitare sMB 1 sui client Linux prima di utilizzare le condivisioni file SMB nell'ambiente di produzione.

A partire dal kernel 4.18 di Linux, il modulo kernel SMB, chiamato `cifs` per motivi legacy, espone un nuovo parametro di modulo (spesso indicato come *parm* da varie documentazioni esterne), chiamato `disable_legacy_dialects`. Anche se introdotto nel kernel Linux 4.18, alcuni fornitori hanno riportato questa modifica ai kernel meno recenti che supportano. Per comodità, la tabella seguente descrive in dettaglio la disponibilità di questo parametro del modulo nelle distribuzioni Linux comuni.

| Distribuzione | Può disabilitare SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | No |
| Ubuntu 18.04 | Sì |
| Ubuntu 19,04 | Sì |
| Debian 8-9 | No |
| Debian 10 anni | Sì |
| Fedora 29 anni | Sì |
| CentOS 7 | No | 
| CentOS 8 o più | Sì |
| Red Hat Enterprise Linux 6.x-7.x | No |
| Red Hat Enterprise Linux 8 | Sì |
| openSUSE Leap 15.0 | No |
| openSUSE Leap 15.1 | Sì |
| openSUSE Tumbleweed | Sì |
| SUSE Linux Enterprise 11.x-12.x | No |
| SUSE Linux Enterprise 15 | No |
| SUSE Linux Enterprise 15.1 | No |

È possibile verificare se la distribuzione `disable_legacy_dialects` Linux supporta il parametro module tramite il comando seguente.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Questo comando dovrebbe restituire il seguente messaggio:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Prima di disabilitare SMB 1, è necessario verificare che il modulo SMB non sia attualmente caricato nel sistema (questo accade automaticamente se è stata montata una condivisione SMB). È possibile eseguire questa operazione con il comando seguente, che non dovrebbe restituire nulla se SMB non viene caricato:

```bash
lsmod | grep cifs
```

Per scaricare il modulo, smontare prima tutte `umount` le condivisioni SMB (utilizzando il comando come descritto in precedenza). È possibile identificare tutte le condivisioni SMB montate sul sistema con il seguente comando:

```bash
mount | grep cifs
```

Una volta smontate tutte le condivisioni file SMB, è possibile scaricare il modulo in modo sicuro. A questo scopo, usare il comando `modprobe` :

```bash
sudo modprobe -r cifs
```

È possibile caricare manualmente il modulo con `modprobe` SMB 1 scaricato utilizzando il comando:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Infine, è possibile controllare che il modulo SMB sia stato `/sys/module/cifs/parameters`caricato con il parametro esaminando i parametri caricati in :

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Per disabilitare in modo persistente SMB 1 nelle distribuzioni basate su Ubuntu e Debian, è necessario `/etc/modprobe.d/local.conf` creare un nuovo file (se non si dispone già di opzioni personalizzate per altri moduli) chiamato con l'impostazione. È possibile farlo con il seguente comando:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

È possibile verificare che questo abbia funzionato caricando il modulo SMB:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>Commenti e suggerimenti
Siamo interessati all'opinione degli utenti Linux.

Il gruppo di utenti di File di Azure per Linux mette a disposizione un forum per condividere commenti e suggerimenti durante la valutazione e l'adozione dell'archiviazione di file su Linux. Per entrare a far parte del gruppo, inviare un messaggio di posta elettronica a [Utenti di File di Azure su Linux](mailto:azurefiles@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su File di Azure, vedere i collegamenti seguenti:

* [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
* [DOMANDE FREQUENTI](../storage-files-faq.md)
* [Risoluzione dei problemi](storage-troubleshoot-linux-file-connection-problems.md)
