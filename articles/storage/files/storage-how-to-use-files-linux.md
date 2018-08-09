---
title: Usare File di Azure con Linux | Microsoft Docs
description: Informazioni su come montare una condivisione file di Azure tramite SMB in Linux.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: renash
ms.component: files
ms.openlocfilehash: 89343f3e4ec91dd32b24cdea6632cecd855cc6f8
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523224"
---
# <a name="use-azure-files-with-linux"></a>Usare File di Azure con Linux
[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere montate nelle distribuzioni Linux usando il [client del kernel SMB](https://wiki.samba.org/index.php/LinuxCIFS). Questo articolo illustra due modi per montare una condivisione file di Azure: su richiesta con il comando `mount` e all'avvio creando una voce in `/etc/fstab`.

> [!NOTE]  
> Per montare una condivisione file di Azure al di fuori dell'area di Azure in cui è ospitata, ad esempio in locale o in un'area di Azure diversa, il sistema operativo deve supportare la funzionalità di crittografia di SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Prerequisiti per il montaggio di una condivisione file di Azure con Linux e il pacchetto cifs-utils
<a id="smb-client-reqs"></a>
* **Selezionare una distribuzione Linux che soddisfi le esigenze di montaggio.**  
      File di Azure può essere installato tramite SMB 2.1 e SMB 3.0. Per le connessioni da client locali o in altre aree di Azure, File di Azure rifiuterà SMB 2.1 (o SMB 3.0 senza crittografia). Se è abilitato *Trasferimento sicuro obbligatorio* per un account di archiviazione, File di Azure consentirà solo connessioni crittografate con SMB 3.0.
    
    Il supporto della crittografia in SMB 3.0 è stato introdotto nella versione kernel Linux 4.11 ed è stato eseguito il backport per le versioni kernel precedenti per distribuzioni di Linux più diffuse. Al momento della pubblicazione di questo documento, le distribuzioni seguenti della raccolta di Azure supportano l'opzione di montaggio specificata nelle intestazioni della tabella. 

* **Versioni minime consigliate con funzionalità di montaggio corrispondenti (SMB versione 2.1 e SMB versione 3.0)**    
    
    |   | SMB 2.1 <br>(Montaggio in macchine virtuali nella stessa area di Azure) | SMB 3.0 <br>(Montaggio in locale e tra più aree) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |
    
    Se la propria distribuzione di Linux non è elencata, è possibile verificare la versione kernel Linux con il comando seguente:    

   ```bash
   uname -r
   ```    

* <a id="install-cifs-utils"></a>**Viene installato il pacchetto cifs-utils.**  
    Il pacchetto cifs-utils può essere installato tramite l'utilità di gestione dei pacchetti nella distribuzione Linux scelta. 

    Nelle distribuzioni basate su **Ubuntu** e **Debian** usare l'utilità di gestione dei pacchetti `apt-get`:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    In **RHEL** e **CentOS** usare l'utilità di gestione dei pacchetti `yum`:

    ```bash
    sudo yum install cifs-utils
    ```

    In **openSUSE** usare l'utilità di gestione dei pacchetti `zypper`:

    ```bash
    sudo zypper install cifs-utils
    ```

    In altre distribuzioni usare l'utilità di gestione pacchetti appropriata o [compilare il codice sorgente](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)
    
* **Scegliere le autorizzazioni per le directory e i file della condivisione montata**: negli esempi seguenti viene usato `0777` per concedere le autorizzazioni di lettura, scrittura ed esecuzione a tutti gli utenti. È possibile sostituirlo con altre [autorizzazioni chmod](https://en.wikipedia.org/wiki/Chmod) in base alle proprie esigenze. 

* **Nome dell'account di archiviazione**: per montare una condivisione file di Azure, sarà necessario il nome dell'account di archiviazione.

* **Chiave dell'account di archiviazione**: per montare una condivisione file di Azure, sarà necessaria la chiave dell'account primaria (o secondaria). Le chiavi di firma di accesso condiviso non sono attualmente supportate per il montaggio.

* **Assicurarsi che la porta 445 sia aperta**: SMB comunica tramite la porta TCP 445. Verificare che il firewall non blocchi le porte TCP 445 dal computer client.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montare la condivisione file di Azure su richiesta con `mount`
1. **[Installare il pacchetto cifs-utils per la distribuzione Linux](#install-cifs-utils)**.

2. **Creare una cartella per il punto di montaggio**: una cartella per un punto di montaggio può essere creata in qualsiasi posizione nel file system; tuttavia, normalmente viene creata sotto la cartella `/mnt`. Ad esempio: 

    ```bash
    mkdir /mnt/MyAzureFileShare
    ```

3. **Usare il comando Monta per montare la condivisione file di Azure**: ricordarsi di sostituire `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>` e `<mount-point>` con le informazioni appropriate per il proprio ambiente. Se la distribuzione di Linux supporta SMB 3.0 con crittografia (vedere [Comprendere i requisiti del client SMB](#smb-client-reqs) per altre informazioni), usare `3.0` per `<smb-version>`. Per le distribuzioni di Linux che non supportano SMB 3.0 con crittografia, usare `2.1` per `<smb-version>`. Si noti che una condivisione file di Azure può essere installata solo all'esterno di un'area di Azure (incluso in locale o in un'area diversa di Azure) con SMB 3.0. 

    ```bash
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Dopo che la condivisione file di Azure è stata completata, è possibile usare `sudo umount <mount-point>` per smontare la condivisione.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Creare un punto di montaggio permanente per la condivisione file di Azure con `/etc/fstab`
1. **[Installare il pacchetto cifs-utils per la distribuzione Linux](#install-cifs-utils)**.

2. **Creare una cartella per il punto di montaggio**: una cartella per un punto di montaggio può essere creata in qualsiasi posizione nel file system; tuttavia, normalmente viene creata sotto la cartella `/mnt`. Ogni volta che si crea tale cartella, prendere nota del percorso assoluto. Ad esempio, il comando seguente crea una nuova cartella sotto `/mnt` (il percorso è un percorso assoluto).

    ```bash
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **Creare un file di credenziali in cui archiviare nome utente (il nome dell'account di archiviazione) e password (la chiave dell'account di archiviazione) per la condivisione file.** Ricordare di sostituire `<storage-account-name>` e `<storage-account-key>` con le informazioni appropriate per l'ambiente. 

    ```bash
    if [ -d "/etc/smbcredentials" ]; then
        sudo mkdir /etc/smbcredentials
    fi

    if [ ! -f "/etc/smbcredentials/<storage-account-name>.cred" ]; then
        sudo bash -c 'echo "username=<storage-account-name>" >> /etc/smbcredentials/<storage-account-name>.cred'
        sudo bash -c 'echo "password=<storage-account-key>" >> /etc/smbcredentials/<storage-account-name>.cred'
    fi
    ```

4. **Modificare le autorizzazioni nel file di credenziali in modo che solo l'account radice possa leggere o modificare il file della password.** Poiché la chiave dell'account di archiviazione è essenzialmente la password di un amministratore con privilegi elevati per l'account di archiviazione, è importante impostare autorizzazioni sul file che consentano l'accesso solo all'account radice al fine di evitare che utenti con privilegi inferiori possano recuperare la chiave dell'account di archiviazione.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage-account-name>.cred
    ```

5. **Usare il comando seguente per accodare la riga seguente a `/etc/fstab`**: ricordare di sostituire `<storage-account-name>`, `<share-name>`, `<smb-version>` e `<mount-point>` con le informazioni appropriate per l'ambiente. Se la distribuzione di Linux supporta SMB 3.0 con crittografia (vedere [Comprendere i requisiti del client SMB](#smb-client-reqs) per altre informazioni), usare `3.0` per `<smb-version>`. Per le distribuzioni di Linux che non supportano SMB 3.0 con crittografia, usare `2.1` per `<smb-version>`. Si noti che una condivisione file di Azure può essere installata solo all'esterno di un'area di Azure (incluso in locale o in un'area diversa di Azure) con SMB 3.0. 

    ```bash
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,credentials=/etc/smbcredentials/<storage-account-name>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Per montare la condivisione file di Azure dopo la modifica, è possibile usare `sudo mount -a` `/etc/fstab` anziché riavviare il computer.

## <a name="feedback"></a>Commenti e suggerimenti
Siamo interessati all'opinione degli utenti Linux.

Il gruppo di utenti di File di Azure per Linux mette a disposizione un forum per condividere commenti e suggerimenti durante la valutazione e l'adozione dell'archiviazione di file su Linux. Per entrare a far parte del gruppo, inviare un messaggio di posta elettronica a [Utenti di File di Azure su Linux](mailto:azurefileslinuxusers@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su File di Azure, vedere i collegamenti seguenti.
* [Introduzione a File di Azure](storage-files-introduction.md)
* [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
* [DOMANDE FREQUENTI](../storage-files-faq.md)
* [Risoluzione dei problemi](storage-troubleshoot-linux-file-connection-problems.md)
