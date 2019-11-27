---
title: Usare l'interfaccia della riga di comando di Azure per i file & ACL in Azure Data Lake Storage Gen2 (anteprima)
description: Usare l'interfaccia della riga di comando di Azure per gestire directory e elenchi di controllo di accesso (ACL) di file e directory in account di archiviazione che hanno uno spazio dei nomi gerarchico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 95bb43f1531b6234ccb90eb7d66404ccc66f60f3
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485146"
---
# <a name="use-azure-cli-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Usare l'interfaccia della riga di comando di Azure per i file & ACL in Azure Data Lake Storage Gen2 (anteprima)

Questo articolo illustra come usare l'interfaccia della [riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) per creare e gestire directory, file e autorizzazioni negli account di archiviazione che hanno uno spazio dei nomi gerarchico. 

> [!IMPORTANT]
> L'estensione `storage-preview` descritta in questo articolo è attualmente disponibile in anteprima pubblica.

[Esempio](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | il [mapping di Gen1 a Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [inviare commenti e suggerimenti](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con spazio dei nomi gerarchico (HNS) abilitato. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne uno.
> * Versione `2.0.67` o successiva dell'interfaccia della riga di comando di Azure

## <a name="install-the-storage-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di archiviazione

1. Aprire il [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)o, se l'interfaccia della riga di comando di Azure è stata [installata](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) localmente, aprire un'applicazione console comando come Windows PowerShell.

2. Verificare che la versione dell'interfaccia della riga di comando di Azure installata sia `2.0.67` o successiva usando il comando seguente.

   ```azurecli
    az --version
   ```
   Se la versione dell'interfaccia della riga di comando di Azure è inferiore a `2.0.67`, installare una versione successiva. Vedere [installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Installare l'estensione `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Connettersi all'account

1. Se si usa l'interfaccia della riga di comando di Azure in locale, eseguire il comando login.

   ```azurecli
   az login
   ```

   Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

   In caso contrario, aprire una pagina del browser in [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e immettere il codice di autorizzazione visualizzato nel terminale. Quindi, accedere con le credenziali dell'account nel browser.

   Per altre informazioni sui diversi metodi di autenticazione, vedere accedere con l'interfaccia della riga di comando di Azure.

2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione che ospiterà il sito Web statico.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Sostituire il valore del segnaposto `<subscription-id>` con l'ID della sottoscrizione.

## <a name="create-a-file-system"></a>Creare un file system

Un file system funge da contenitore per i file. È possibile crearne uno usando il comando `az storage container create`. 

In questo esempio viene creato un file system denominato `my-file-system`.

```azurecli
az storage container create --name my-file-system
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory usando il comando `az storage blob directory create`. 

In questo esempio viene aggiunta una directory denominata `my-directory` a una file system denominata `my-file-system` che si trova in un account denominato `mystorageaccount`.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Mostra proprietà Directory

È possibile stampare le proprietà di una directory nella console usando il comando `az storage blob show`.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory usando il comando `az storage blob directory move`.

Questo esempio rinomina una directory dal nome `my-directory` al nome `my-new-directory`.

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory usando il comando `az storage blob directory delete`.

In questo esempio viene eliminata una directory denominata `my-directory`. 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Controllare se esiste una directory

Determinare se una directory specifica esiste nel file system usando il comando `az storage blob directory exist`.

Questo esempio Mostra se nella file system `my-file-system` esiste una directory denominata `my-directory`. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Scarica da una directory

Scaricare un file da una directory usando il comando `az storage blob directory download`.

Questo esempio Scarica un file denominato `upload.txt` da una directory denominata `my-directory`. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

In questo esempio viene scaricata un'intera directory.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Elencare il contenuto di una directory usando il comando `az storage blob directory list`.

Questo esempio elenca il contenuto di una directory denominata `my-directory` che si trova nel `my-file-system` file system di un account di archiviazione denominato `mystorageaccount`. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Caricare un file in una directory usando il comando `az storage blob directory upload`.

In questo esempio viene caricato un file denominato `upload.txt` in una directory denominata `my-directory`. 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

Questo esempio carica un'intera directory.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Mostra proprietà file

È possibile stampare le proprietà di un file nella console usando il comando `az storage blob show`.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Rinominare o spostare un file

Rinominare o spostare un file usando il comando `az storage blob move`.

Questo esempio rinomina un file dal nome `my-file.txt` al nome `my-file-renamed.txt`.

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Eliminare un file

Eliminare un file usando il comando `az storage blob delete`.

Questo esempio elimina un file denominato `my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Gestisci autorizzazioni

È possibile ottenere, impostare e aggiornare le autorizzazioni di accesso di directory e file.

### <a name="get-directory-and-file-permissions"></a>Ottenere le autorizzazioni per directory e file

Ottenere l'ACL di una **directory** usando il comando `az storage blob directory access show`.

Questo esempio Mostra come ottenere l'ACL di una directory e quindi stampa l'ACL nella console.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Ottenere le autorizzazioni di accesso di un **file** usando il comando `az storage blob access show`. 

Questo esempio Mostra come ottenere l'ACL di un file e quindi stamparlo nella console.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Nell'immagine seguente viene illustrato l'output dopo aver ricevuto l'ACL di una directory.

![Ottieni output ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

In questo esempio, l'utente proprietario dispone delle autorizzazioni di lettura, scrittura ed esecuzione. Il gruppo proprietario dispone solo delle autorizzazioni di lettura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [controllo di accesso in Azure Data Lake storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Impostare le autorizzazioni per directory e file

Usare il comando `az storage blob directory access set` per impostare l'ACL di una **directory**. 

Questo esempio imposta l'ACL in una directory per l'utente proprietario, il gruppo proprietario o altri utenti, quindi stampa l'ACL nella console.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Usare il comando `az storage blob access set` per impostare l'ACL di un **file**. 

In questo esempio viene impostato l'ACL in un file per l'utente proprietario, il gruppo proprietario o altri utenti, quindi viene stampato l'ACL nella console.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
Nell'immagine seguente viene illustrato l'output dopo l'impostazione dell'ACL di un file.

![Ottieni output ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

In questo esempio, l'utente proprietario e il gruppo proprietario hanno solo le autorizzazioni di lettura e scrittura. Tutti gli altri utenti dispongono di autorizzazioni di scrittura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [controllo di accesso in Azure Data Lake storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Aggiornare le autorizzazioni per directory e file

Un altro modo per impostare questa autorizzazione consiste nell'usare il comando `az storage blob directory access update` o `az storage blob access update`. 

Aggiornare l'ACL di una directory o di un file impostando il parametro `-permissions` sulla forma abbreviata di un ACL.

Questo esempio aggiorna l'ACL di una **directory**.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Questo esempio aggiorna l'ACL di un **file**.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

È anche possibile aggiornare l'utente proprietario e il gruppo di una directory o di un file impostando i parametri `--owner` o `group` sull'ID entità o sul nome dell'entità utente (UPN) di un utente. 

Questo esempio Mostra come modificare il proprietario di una directory. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

In questo esempio viene modificato il proprietario di un file. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Gestire i metadati definiti dall'utente

È possibile aggiungere metadati definiti dall'utente a un file o a una directory usando il comando `az storage blob directory metadata update` con una o più coppie nome/valore.

Questo esempio aggiunge i metadati definiti dall'utente per una directory denominata `my-directory` directory.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

Questo esempio Mostra tutti i metadati definiti dall'utente per la directory denominata `my-directory`.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Vedere anche

* [Esempio](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Mapping tra Gen1 e Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Commenti e suggerimenti](https://github.com/Azure/azure-cli-extensions/issues)
* [Gap di funzionalità note](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Codice sorgente](https://github.com/Azure/azure-cli-extensions/tree/master/src)

