---
title: Come montare l'archivio BLOB di Azure come file system in Linux | Microsoft Docs
description: Montare un contenitore di archiviazione BLOB di Azure con FUSE in Linux
services: storage
documentationcenter: linux
author: seguler
manager: jahogg
ms.service: storage
ms.devlang: bash
ms.topic: article
ms.date: 01/19/2018
ms.author: seguler
ms.openlocfilehash: 299b96c783fb3606347bb448d00d44f0071da429
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse-preview"></a>Come montare l'archivio BLOB come file system con blobfuse (anteprima)

## <a name="overview"></a>Panoramica
[Blobfuse](https://github.com/Azure/azure-storage-fuse) è un driver del file system virtuale per Archiviazione BLOB di Azure, che consente di accedere ai dati BLOB in blocchi esistenti nell'account di archiviazione tramite il file system di Linux. Archiviazione BLOB di Azure è un servizio di archiviazione di oggetti e quindi non ha uno spazio dei nomi gerarchico. Blobfuse fornisce questo spazio dei nomi tramite lo schema della directory virtuale usando la barra "/" come delimitatore.  

Questa guida illustra come usare blobfuse e come montare un contenitore di archiviazione BLOB in Linux e accedere ai dati. Per altre informazioni su blobfuse, vedere i dettagli nel [repository di blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse non garantisce il 100% di conformità POSIX perché si limita a convertire le richieste in [API REST BLOB](https://docs.microsoft.com/en-us/rest/api/storageservices/blob-service-rest-api). Le operazioni di ridenominazione, ad esempio, sono atomiche in POSIX, ma non in blobfuse.
> Per un elenco completo delle differenze tra un file system nativo e blobfuse, vedere il [repository del codice sorgente di blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Installare blobfuse in Linux
I file binari di blobfuse sono disponibili nei [repository software Microsoft per Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software). Per installare blobfuse, configurare uno di questi repository.

### <a name="configure-the-microsoft-package-repository"></a>Configurare il repository di pacchetti Microsoft
Configurare il [repository di pacchetti Linux per i prodotti Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Ad esempio, in una distribuzione Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Analogamente, sostituire l'URL con `.../rhel/7/...` in modo che faccia riferimento a una distribuzione Enterprise Linux 7.

Un altro esempio in Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Analogamente, sostituire l'URL con `.../ubuntu/16.04/...` in modo che faccia riferimento a una distribuzione Ubuntu 16.04.

### <a name="install-blobfuse"></a>Installare blobfuse

In una distribuzione Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

In una distribuzione Enterprise Linux:
```bash
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Preparare il montaggio
Blobfuse richiede un percorso temporaneo nel file system per memorizzare nel buffer e nella cache eventuali file aperti e offrire in questo modo prestazioni di tipo nativo. Per questo percorso temporaneo, scegliere il disco con le prestazioni più elevate o usare un disco RAM per prestazioni ottimali. 

> [!NOTE]
> Blobfuse archivia i contenuti di tutti i file aperti nel percorso temporaneo. Verificare di avere spazio sufficiente per tutti i file aperti. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Facoltativo) Usare un disco RAM per il percorso temporaneo
L'esempio seguente crea un disco RAM da 16 GB e una directory per blobfuse. Scegliere le dimensioni in base alle proprie esigenze. Questo disco RAM consente a blobfuse di aprire file di dimensioni massime pari a 16 GB. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-for-temporary-path"></a>Usare un'unità SSD per il percorso temporaneo
In Azure è possibile usare i dischi temporanei (unità SSD) disponibili nelle VM per fornire un buffer a bassa latenza per blobfuse. Nelle distribuzioni Ubuntu questo disco temporaneo viene montato in "/mnt", mentre nelle distribuzioni RedHat e CentOS viene montato in "/mnt/resource/".

Assicurarsi che l'utente abbia accesso al percorso temporaneo:
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Configurare le credenziali dell'account di archiviazione
Con blobfuse le credenziali devono essere archiviate in un file di testo con il formato seguente: 

```
accountName myaccount
accountKey myaccesskey==
containerName mycontainer
```

Dopo aver creato questo file, assicurarsi di limitare l'accesso in modo che nessun altro utente possa leggerlo.
```bash
chmod 700 fuse_connection.cfg
```

### <a name="create-an-empty-directory-for-mounting"></a>Creare una directory vuota per il montaggio
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Eseguire il montaggio

> [!NOTE]
> Per un elenco completo di opzioni di montaggio, vedere il [repository di blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Per montare blobfuse, usare il comando seguente con l'utente. Questo comando monta nella posizione "/mycontainer" il contenitore specificato in "/path/to/fuse_connection.cfg".

```bash
blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Si avrà ora accesso ai BLOB in blocchi tramite le normali API del file system. Si noti che la directory montata è accessibile solo all'utente che esegue il montaggio, per proteggere l'accesso. Per consentire l'accesso a tutti gli utenti, è possibile eseguire il montaggio tramite l'opzione ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Passaggi successivi

* [Blobfuse home page (Home page di blobfuse)](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Report blobfuse issues (Segnalare i problemi di blobfuse)](https://github.com/Azure/azure-storage-fuse/issues) 

