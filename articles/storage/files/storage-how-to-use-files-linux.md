---
title: Usare File di Azure con Linux | Microsoft Docs
description: Informazioni su come montare una condivisione file di Azure tramite SMB in Linux. Vedere l'elenco dei prerequisiti. Esaminare le considerazioni sulla sicurezza di SMB nei client Linux.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c271107b85e4903153c29b58aadadd37fb051b76
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626742"
---
# <a name="use-azure-files-with-linux"></a>Usare File di Azure con Linux
[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere montate nelle distribuzioni Linux usando il [client del kernel SMB](https://wiki.samba.org/index.php/LinuxCIFS). Questo articolo illustra due modi per montare una condivisione file di Azure: su richiesta con il comando `mount` e all'avvio creando una voce in `/etc/fstab`.

Il modo consigliato per montare una condivisione file di Azure in Linux è l'uso di SMB 3,0. Per impostazione predefinita, File di Azure richiede la crittografia in transito, supportata solo da SMB 3,0. File di Azure supporta anche SMB 2,1, che non supporta la crittografia in transito, ma non è possibile montare condivisioni file di Azure con SMB 2,1 da un'altra area di Azure o in locale per motivi di sicurezza. A meno che l'applicazione non richieda specificamente SMB 2,1, non è necessario usarla perché le distribuzioni di Linux più diffuse e di recente supportano SMB 3,0:  

| Distribuzione di Linux | SMB 2.1 <br>(Montaggio in macchine virtuali nella stessa area di Azure) | SMB 3.0 <br>(Montaggio in locale e tra più aree) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 10+ |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP2+ |

Se si usa una distribuzione Linux non elencata nella tabella precedente, è possibile verificare se la distribuzione Linux supporta SMB 3,0 con crittografia controllando la versione del kernel Linux. SMB 3,0 con crittografia è stato aggiunto alla versione 4,11 del kernel Linux. Il `uname` comando restituirà la versione del kernel Linux in uso:

```bash
uname -r
```

## <a name="prerequisites"></a>Prerequisiti
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Verificare che il pacchetto cifs-utils sia installato.**  
    Il pacchetto cifs-utils può essere installato tramite l'utilità di gestione dei pacchetti nella distribuzione Linux scelta. 

    Nelle distribuzioni basate su **Ubuntu** e **Debian** usare l'utilità di gestione dei pacchetti `apt`:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    In **Fedora** **Red Hat Enterprise Linux 8 +** e **CentOS 8 +** usare `dnf` Gestione pacchetti:

    ```bash
    sudo dnf install cifs-utils
    ```

    Nelle versioni precedenti di **Red Hat Enterprise Linux** e **CentOS** usare `yum` Gestione pacchetti:

    ```bash
    sudo yum install cifs-utils 
    ```

    In **openSUSE** usare l'utilità di gestione dei pacchetti `zypper`:

    ```bash
    sudo zypper install cifs-utils
    ```

    In altre distribuzioni usare l'utilità di gestione pacchetti appropriata o [compilare il codice sorgente](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **La versione più recente dell'interfaccia della riga di comando di Azure.** Per altre informazioni su come installare l'interfaccia della riga di comando di Azure, vedere [installare l'interfaccia della](/cli/azure/install-azure-cli?view=azure-cli-latest) riga di comando di Azure e selezionare il sistema operativo. Se si preferisce usare il modulo Azure PowerShell in PowerShell 6 +, è possibile, tuttavia, le istruzioni riportate di seguito vengono presentate per l'interfaccia della riga di comando di Azure.

* **Assicurarsi che la porta 445 sia aperta** : SMB comunica tramite la porta TCP 445. Verificare che il firewall non blocchi le porte TCP 445 dal computer client.  Sostituire `<your-resource-group>` e `<your-storage-account>` quindi eseguire lo script seguente:
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

    Se non si è in grado di aprire la porta 445 nella rete aziendale o se questa operazione non viene eseguita da un ISP, è possibile usare una connessione VPN o ExpressRoute per aggirare la porta 445. Per altre informazioni, vedere [considerazioni sulla rete per l'accesso diretto alla condivisione file di Azure](storage-files-networking-overview.md).

## <a name="mounting-azure-file-share"></a>Montaggio della condivisione file di Azure
Per usare una condivisione file di Azure con la distribuzione Linux, è necessario creare una directory che funga da punto di montaggio per la condivisione file di Azure. Un punto di montaggio può essere creato in qualsiasi punto del sistema Linux, ma è una convenzione comune per crearlo in/mnt. Dopo il punto di montaggio, è possibile usare il `mount` comando per accedere alla condivisione file di Azure.

Se lo si desidera, è possibile montare la stessa condivisione file di Azure in più punti di montaggio.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montare la condivisione file di Azure su richiesta con `mount`
1. **Creare una cartella per il punto di montaggio** : sostituire `<your-resource-group>` , `<your-storage-account>` e `<your-file-share>` con le informazioni appropriate per l'ambiente:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Usare il comando di montaggio per montare la condivisione file di Azure**. Nell'esempio seguente, le autorizzazioni per file e cartelle di Linux locale sono predefinite 0755, ovvero di lettura, scrittura ed esecuzione per il proprietario (in base al proprietario Linux di file/directory), lettura ed esecuzione per gli utenti nel gruppo proprietario e lettura ed esecuzione per altri utenti nel sistema. È possibile utilizzare le `uid` `gid` Opzioni di montaggio e per impostare l'ID utente e l'ID gruppo per il montaggio. È anche possibile usare `dir_mode` e `file_mode` per impostare le autorizzazioni personalizzate nel modo desiderato. Per ulteriori informazioni su come impostare le autorizzazioni, vedere [notazione numerica UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) in Wikipedia. 

    ```bash
    # This command assumes you have logged in with az login
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
    > Il comando di montaggio precedente viene montato con SMB 3,0. Se la distribuzione di Linux non supporta SMB 3,0 con crittografia o se supporta solo SMB 2,1, è possibile montare solo da una macchina virtuale di Azure nella stessa area dell'account di archiviazione. Per montare la condivisione file di Azure in una distribuzione Linux che non supporta SMB 3,0 con crittografia, sarà necessario [disabilitare la crittografia in transito per l'account di archiviazione](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Dopo che la condivisione file di Azure è stata completata, è possibile usare `sudo umount $mntPath` per smontare la condivisione.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Creare un punto di montaggio permanente per la condivisione file di Azure con `/etc/fstab`
1. **Creare una cartella per il punto di montaggio** : è possibile creare una cartella per un punto di montaggio in un punto qualsiasi dell'file System, ma è la convenzione comune per crearla in/mnt. Ad esempio, il comando seguente crea una nuova directory, sostituisce `<your-resource-group>` , `<your-storage-account>` e `<your-file-share>` con le informazioni appropriate per l'ambiente:

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

1. **Usare il comando seguente per aggiungere la riga seguente a `/etc/fstab`** : nell'esempio riportato di seguito, le autorizzazioni per file e cartella di Linux locale sono predefinite 0755, ovvero la lettura, la scrittura e l'esecuzione del proprietario (in base al proprietario Linux del file/directory), la lettura e l'esecuzione per gli utenti nel gruppo proprietario e la lettura e l'esecuzione per altri utenti nel sistema. È possibile utilizzare le `uid` `gid` Opzioni di montaggio e per impostare l'ID utente e l'ID gruppo per il montaggio. È anche possibile usare `dir_mode` e `file_mode` per impostare le autorizzazioni personalizzate nel modo desiderato. Per ulteriori informazioni su come impostare le autorizzazioni, vedere [notazione numerica UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) in Wikipedia.

    ```bash
    # This command assumes you have logged in with az login
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
    > Il comando di montaggio precedente viene montato con SMB 3,0. Se la distribuzione di Linux non supporta SMB 3,0 con crittografia o se supporta solo SMB 2,1, è possibile montare solo da una macchina virtuale di Azure nella stessa area dell'account di archiviazione. Per montare la condivisione file di Azure in una distribuzione Linux che non supporta SMB 3,0 con crittografia, sarà necessario [disabilitare la crittografia in transito per l'account di archiviazione](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>Uso di autofs per montare automaticamente le condivisioni file di Azure

1. **Verificare che il pacchetto autofs sia installato.**  

    Il pacchetto autofs può essere installato usando Gestione pacchetti nella distribuzione Linux di propria scelta. 

    Nelle distribuzioni basate su **Ubuntu** e **Debian** usare l'utilità di gestione dei pacchetti `apt`:
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    In **Fedora** **Red Hat Enterprise Linux 8 +** e **CentOS 8 +** usare `dnf` Gestione pacchetti:
    ```bash
    sudo dnf install autofs
    ```
    Nelle versioni precedenti di **Red Hat Enterprise Linux** e **CentOS** usare `yum` Gestione pacchetti:
    ```bash
    sudo yum install autofs 
    ```
    In **openSUSE** usare l'utilità di gestione dei pacchetti `zypper`:
    ```bash
    sudo zypper install autofs
    ```
2. **Creare un punto di montaggio per le condivisioni** :
   ```bash
    sudo mkdir /fileshares
    ```
3. **Creta un nuovo file di configurazione autofs personalizzato**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **Aggiungere le voci seguenti a/etc/auto.fileshares**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **Aggiungere la voce seguente a/etc/auto.master**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **Riavviare autofs**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **Accedere alla cartella designata per la condivisione**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>Protezione di Linux
Per montare una condivisione file di Azure in Linux, la porta 445 deve essere accessibile. Molte organizzazioni bloccano la porta 445 a causa dei rischi per la sicurezza associati a SMB 1. SMB 1, noto anche come CIFS (Common Internet file System), è un protocollo legacy file system incluso in molte distribuzioni Linux. SMB 1 è un protocollo obsoleto, inefficiente e, soprattutto, non sicuro. La novità è che File di Azure non supporta SMB 1 e, a partire dalla versione kernel Linux 4,18, Linux rende possibile disabilitare SMB 1. Prima di usare le condivisioni file SMB in produzione, è sempre [consigliabile](https://aka.ms/stopusingsmb1) disabilitare SMB 1 nei client Linux.

A partire dal kernel Linux 4,18, il modulo kernel SMB, denominato `cifs` per motivi legacy, espone un nuovo parametro di modulo (spesso definito *in* base a più documenti esterni), denominato `disable_legacy_dialects` . Anche se introdotta nel kernel Linux 4,18, alcuni fornitori hanno sottoportato questa modifica ai kernel meno recenti supportati. Per praticità, nella tabella seguente viene illustrata in dettaglio la disponibilità di questo parametro del modulo nelle distribuzioni comuni di Linux.

| Distribuzione | Può disabilitare SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | No |
| Ubuntu 18.04 | Sì |
| Ubuntu 19.04 + | Sì |
| Debian 8-9 | No |
| Debian 10 + | Sì |
| Fedora 29 + | Sì |
| CentOS 7 | No | 
| CentOS 8 + | Sì |
| Red Hat Enterprise Linux 6. x-7. x | No |
| Red Hat Enterprise Linux 8 + | Sì |
| openSUSE Leap 15,0 | No |
| openSUSE Leap 15.1 + | Sì |
| Tumbleweed openSUSE | Sì |
| SUSE Linux Enterprise 11. x-12. x | No |
| SUSE Linux Enterprise 15 | No |
| SUSE Linux Enterprise 15,1 | No |

È possibile verificare se la distribuzione di Linux supporta il `disable_legacy_dialects` parametro module usando il comando seguente.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Questo comando dovrebbe restituire il messaggio seguente:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Prima di disabilitare SMB 1, è necessario verificare che il modulo SMB non sia attualmente caricato nel sistema (ciò avviene automaticamente se è stata montata una condivisione SMB). È possibile eseguire questa operazione con il comando seguente, che non dovrebbe restituire nulla se SMB non è caricato:

```bash
lsmod | grep cifs
```

Per scaricare il modulo, prima di tutto smontare tutte le condivisioni SMB (usando il `umount` comando come descritto in precedenza). È possibile identificare tutte le condivisioni SMB montate nel sistema con il comando seguente:

```bash
mount | grep cifs
```

Dopo aver smontato tutte le condivisioni file SMB, è possibile scaricare il modulo in modo sicuro. A questo scopo, usare il comando `modprobe` :

```bash
sudo modprobe -r cifs
```

È possibile caricare manualmente il modulo con SMB 1 scaricato usando il `modprobe` comando:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Infine, è possibile verificare che il modulo SMB sia stato caricato con il parametro esaminando i parametri caricati in `/sys/module/cifs/parameters` :

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Per disabilitare in modo permanente SMB 1 su Ubuntu e distribuzioni basate su Debian, è necessario creare un nuovo file (se non si dispone già di opzioni personalizzate per altri moduli) chiamato `/etc/modprobe.d/local.conf` con l'impostazione. Questa operazione può essere eseguita con il comando seguente:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

È possibile verificare che questa operazione abbia funzionato caricando il modulo SMB:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su File di Azure, vedere i collegamenti seguenti:

* [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
* [Domande frequenti](./storage-files-faq.md)
* [Risoluzione dei problemi](storage-troubleshoot-linux-file-connection-problems.md)