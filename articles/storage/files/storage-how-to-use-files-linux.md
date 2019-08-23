---
title: Usare File di Azure con Linux | Microsoft Docs
description: Informazioni su come montare una condivisione file di Azure tramite SMB in Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9674df346ff0b5c046df26fe85bf7ba0bbb802e6
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900316"
---
# <a name="use-azure-files-with-linux"></a>Usare File di Azure con Linux

[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere montate nelle distribuzioni Linux usando il [client del kernel SMB](https://wiki.samba.org/index.php/LinuxCIFS). Questo articolo illustra due modi per montare una condivisione file di Azure: su richiesta con il comando `mount` e all'avvio creando una voce in `/etc/fstab`.

> [!NOTE]  
> Per montare una condivisione file di Azure al di fuori dell'area di Azure in cui è ospitata, ad esempio in locale o in un'area di Azure diversa, il sistema operativo deve supportare la funzionalità di crittografia di SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Prerequisiti per il montaggio di una condivisione file di Azure con Linux e il pacchetto cifs-utils
<a id="smb-client-reqs"></a>

* **Un account di archiviazione di Azure esistente e una condivisione file**: Per completare questo articolo, è necessario disporre di un account di archiviazione e di una condivisione file. Se non ne è già stato creato uno, vedere una delle guide introduttive sull'oggetto: [Creare condivisioni file-CLI](storage-how-to-use-files-cli.md).

* **Il nome e la chiave dell'account di archiviazione** Per completare questo articolo, sono necessari il nome e la chiave dell'account di archiviazione. Se ne è stata creata una usando la Guida introduttiva dell'interfaccia della riga di comando, è necessario che siano già presenti. in caso contrario, consultare la Guida introduttiva dell'interfaccia della riga di comando collegata in precedenza per informazioni su come recuperare la chiave dell'account

* **Selezionare una distribuzione Linux che soddisfi le esigenze di montaggio.**  
      File di Azure può essere installato tramite SMB 2.1 e SMB 3.0. Per le connessioni da client locali o in altre aree di Azure, è necessario usare SMB 3.0. File di Azure rifiuterà SMB 2.1 (o SMB 3.0 senza crittografia). Se si accede alla condivisione file di Azure da una macchina virtuale nella stessa area di Azure, è possibile accedere alla condivisione file usando SMB 2.1, se e solo se, è disabilitato il *trasferimento sicuro obbligatorio* per l'account di archiviazione che ospita la condivisione file di Azure. È sempre consigliabile richiedere il trasferimento sicuro e usare solo SMB 3.0 con crittografia.

    Il supporto della crittografia in SMB 3.0 è stato introdotto nella versione kernel Linux 4.11 ed è stato eseguito il backport per le versioni kernel precedenti per distribuzioni di Linux più diffuse. Al momento della pubblicazione di questo documento, le distribuzioni seguenti della raccolta di Azure supportano l'opzione di montaggio specificata nelle intestazioni della tabella. 

### <a name="minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30"></a>Versioni minime consigliate con funzionalità di montaggio corrispondenti (versione SMB 2,1 vs SMB versione 3,0)

|   | SMB 2.1 <br>(Montaggio in macchine virtuali nella stessa area di Azure) | SMB 3.0 <br>(Montaggio in locale e tra più aree) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 9 +<sup>*</sup> |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

<sup>*</sup>Potrebbe essere necessario l'utilizzo di un kernel con backporting. È possibile installarla con `sudo apt-get install linux-image-cloud-amd64`.

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

* **Scegliere le autorizzazioni per directory e file della condivisione montata**: negli esempi seguenti viene usata l'autorizzazione `0777` per concedere le autorizzazioni di lettura, scrittura ed esecuzione a tutti gli utenti. Se lo si desidera, è possibile sostituirlo con altre [autorizzazioni con chmod](https://en.wikipedia.org/wiki/Chmod) , sebbene ciò comporterebbe l'accesso potenzialmente restrittivo. Se si usano altre autorizzazioni, è consigliabile usare UID e GID anche per mantenere l'accesso per gli utenti e i gruppi locali di propria scelta.

> [!NOTE]
> Se non si assegna in modo esplicito l'autorizzazione file e directory con dir_mode e file_mode, per impostazione predefinita viene impostato su 0755.

* **Assicurarsi che la porta 445 sia aperta**: SMB comunica tramite la porta TCP 445: verificare che il firewall non blocchi le porte TCP 445 dal computer client.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montare la condivisione file di Azure su richiesta con `mount`

1. **[Installare il pacchetto cifs-utils per la distribuzione Linux](#install-cifs-utils)** .

1. **Creare una cartella per il punto di montaggio**: Una cartella per un punto di montaggio può essere creata in qualsiasi punto del file system, ma è la convenzione comune per crearla in una nuova cartella. Ad esempio, il comando seguente crea una nuova directory, sostituisce **< storage_account_name >** e **< file_share_name >** con le informazioni appropriate per l'ambiente:

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Usare il comando mount per montare la condivisione file di Azure**: Ricordarsi di sostituire **< storage_account_name >** , **< share_name >** , < **smb_version >** , **< storage_account_key >** e **< mount_point >** con le informazioni appropriate per il ambiente. Se la distribuzione di Linux supporta SMB 3,0 con crittografia (vedere [comprendere i requisiti del client SMB](#smb-client-reqs) per ulteriori informazioni), usare **3,0** per **< > smb_version**. Per le distribuzioni Linux che non supportano SMB 3,0 con crittografia, usare **2,1** per **< > smb_version**. Una condivisione file di Azure può essere montata solo all'esterno di un'area di Azure (inclusa in locale o in un'area di Azure diversa) con SMB 3,0. Se lo si desidera, è possibile modificare le autorizzazioni directory e file della condivisione montata, ma ciò significherebbe la restrizione dell'accesso.

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Dopo che la condivisione file di Azure è stata completata, è possibile usare `sudo umount <mount_point>` per smontare la condivisione.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Creare un punto di montaggio permanente per la condivisione file di Azure con `/etc/fstab`

1. **[Installare il pacchetto cifs-utils per la distribuzione Linux](#install-cifs-utils)** .

1. **Creare una cartella per il punto di montaggio**: Una cartella per un punto di montaggio può essere creata in qualsiasi punto del file system, ma è la convenzione comune per crearla in una nuova cartella. Ogni volta che si crea tale cartella, prendere nota del percorso assoluto. Ad esempio, il comando seguente crea una nuova directory, sostituisce **< storage_account_name >** e **< file_share_name >** con le informazioni appropriate per l'ambiente in uso.

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Creare un file di credenziali in cui archiviare nome utente (il nome dell'account di archiviazione) e password (la chiave dell'account di archiviazione) per la condivisione file.** Sostituire **< storage_account_name >** e **< storage_account_key >** con le informazioni appropriate per l'ambiente in uso.

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **Modificare le autorizzazioni nel file di credenziali in modo che solo l'account radice possa leggere o modificare il file della password.** Poiché la chiave dell'account di archiviazione è essenzialmente la password di un amministratore con privilegi elevati per l'account di archiviazione, è importante impostare autorizzazioni sul file che consentano l'accesso solo all'account radice al fine di evitare che utenti con privilegi inferiori possano recuperare la chiave dell'account di archiviazione.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. **Usare il comando seguente per accodare la riga seguente a `/etc/fstab`** : Ricordarsi di sostituire **< storage_account_name >** , **< share_name >** , **< smb_version**> e **< mount_point >** con le informazioni appropriate per l'ambiente in uso. Se la distribuzione di Linux supporta SMB 3,0 con crittografia (vedere [comprendere i requisiti del client SMB](#smb-client-reqs) per ulteriori informazioni), usare **3,0** per **< > smb_version**. Per le distribuzioni Linux che non supportano SMB 3,0 con crittografia, usare **2,1** per **< > smb_version**. Una condivisione file di Azure può essere montata solo all'esterno di un'area di Azure (inclusa in locale o in un'area di Azure diversa) con SMB 3,0.

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs _netdev,nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> Per montare la condivisione file di Azure dopo la modifica, è possibile usare `sudo mount -a` `/etc/fstab` anziché riavviare il computer.

## <a name="feedback"></a>Commenti e suggerimenti

Siamo interessati all'opinione degli utenti Linux.

Il gruppo di utenti di File di Azure per Linux mette a disposizione un forum per condividere commenti e suggerimenti durante la valutazione e l'adozione dell'archiviazione di file su Linux. Per entrare a far parte del gruppo, inviare un messaggio di posta elettronica a [Utenti di File di Azure su Linux](mailto:azurefileslinuxusers@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su File di Azure, vedere i collegamenti seguenti:

* [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
* [Domande frequenti](../storage-files-faq.md)
* [Risoluzione dei problemi](storage-troubleshoot-linux-file-connection-problems.md)
