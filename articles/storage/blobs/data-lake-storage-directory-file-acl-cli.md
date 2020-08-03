---
title: Usare l'interfaccia della riga di comando di Azure per i file e le ACL in Azure Data Lake Storage Gen2
description: Usare l'interfaccia della riga di comando di Azure per gestire directory ed elenchi di controllo di accesso di file e directory in account di archiviazione che hanno uno spazio dei nomi gerarchico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 05/18/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6140260b75580270b365e59358d97e0a54c7b4a7
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503940"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Usare l'interfaccia della riga di comando di Azure per gestire directory, file ed elenchi di controllo di accesso in Azure Data Lake Storage Gen2

Questo articolo illustra come usare l['interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) per creare e gestire directory, file e autorizzazioni negli account di archiviazione che hanno uno spazio dei nomi gerarchico. 

[Esempi](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  di [Invia commenti e suggerimenti](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni.
> * Interfaccia della riga di comando di Azure versione `2.6.0` o successiva.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Assicurarsi che sia installata la versione corretta dell'interfaccia della riga di comando di Azure

1. Aprire [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)o aprire un'applicazione console comando come Windows PowerShell, se è stata [installata](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) l'interfaccia della riga di comando di Azure in locale.

2. Verificare con il comando seguente che la versione dell'interfaccia della riga di comando di Azure installata corrisponda a `2.6.0` o successive.

   ```azurecli
    az --version
   ```
   Se la versione dell'interfaccia della riga di comando di Azure fosse inferiore a `2.6.0`, installare una versione successiva. Vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-account"></a>Effettuare la connessione all'account

1. Se si usa l'interfaccia della riga di comando di Azure in locale, eseguire il comando di accesso.

   ```azurecli
   az login
   ```

   Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

   In caso contrario, aprire una pagina del browser all'indirizzo [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e immettere il codice di autorizzazione visualizzato nel terminale. Quindi, accedere con le credenziali dell'account nel browser.

   Per altre informazioni sui diversi metodi di autenticazione, vedere [Autorizzare l'accesso ai dati BLOB o ai dati della coda con l'interfaccia della riga di comando di Azure](../common/authorize-data-operations-cli.md).

2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva alla sottoscrizione dell'account di archiviazione che ospiterà il sito Web statico.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

> [!NOTE]
> L'esempio presentato in questo articolo illustra l'autorizzazione Azure Active Directory. Per altre informazioni sui metodi di autorizzazione, vedere [Autorizzare l'accesso ai dati BLOB o ai dati della coda con l'interfaccia della riga di comando di Azure](../common/authorize-data-operations-cli.md).

## <a name="create-a-container"></a>Creare un contenitore

Un contenitore funge da file system per i file. È possibile crearne uno con il comando `az storage fs create`. 

Questo esempio crea un contenitore denominato `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>Mostra proprietà contenitore

È possibile stampare le proprietà di un contenitore nella console usando il `az storage fs show` comando.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>Elencare il contenuto del contenitore

Elencare il contenuto di una directory con il comando `az storage fs file list`.

Questo esempio elenca il contenuto di un contenitore denominato `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>Eliminare un contenitore

Eliminare un contenitore usando il `az storage fs delete` comando.

In questo esempio viene eliminato un contenitore denominato `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory con il comando `az storage fs directory create`. 

In questo esempio viene aggiunta una directory denominata `my-directory` a un contenitore denominato `my-file-system` che si trova in un account denominato `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Mostrare le proprietà della directory

È possibile stampare nella console le proprietà di una directory con il comando `az storage fs directory show`.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory con il comando `az storage fs directory move`.

Questo esempio rinomina una directory dal nome `my-directory` al nome `my-new-directory` nello stesso contenitore.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

Questo esempio Mostra come spostare una directory in un contenitore denominato `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory con il comando `az storage fs directory delete`.

Questo esempio illustra come eliminare una directory denominata `my-directory`. 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Controllare se esista una directory

Determinare se una directory specifica esiste nel contenitore tramite il `az storage fs directory exists` comando.

Questo esempio Mostra se una directory denominata `my-directory` esiste nel `my-file-system` contenitore. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Scaricare da una directory

Scaricare un file da una directory con il comando `az storage fs file download`.

Questo esempio illustra come scaricare un file denominato `upload.txt` da una directory denominata `my-directory`. 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Elencare il contenuto di una directory con il comando `az storage fs file list`.

Questo esempio elenca il contenuto di una directory denominata `my-directory` che si trova nel `my-file-system` contenitore di un account di archiviazione denominato `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Caricare un file in una directory con il comando `az storage fs directory upload`.

Questo esempio illustra come caricare un file denominato `upload.txt` in una directory denominata `my-directory`. 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Mostrare le proprietà file

È possibile stampare nella console le proprietà di un file con il comando `az storage fs file show`.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Rinominare o spostare un file

Rinominare o spostare un file con il comando `az storage fs file move`.

Questo esempio illustra come rinominare un file da `my-file.txt` a `my-file-renamed.txt`.

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Eliminare un file

Eliminare un file con il comando `az storage fs file delete`.

Questo esempio illustra come eliminare un file denominato `my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-permissions"></a>Gestione autorizzazioni

È possibile ottenere, impostare e aggiornare le autorizzazioni di accesso di file e directory.

> [!NOTE]
> Se si usa Azure Active Directory per autorizzare i comandi, assicurarsi che all'entità di sicurezza sia stato assegnato il [ruolo proprietario del BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Recuperare un ACL

Recuperare l'ACL da una **directory** con il comando `az storage fs access show`.

Questo esempio illustra come recuperare l'ACL di una directory e quindi come stampare l'ACL nella console.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Ottenere le autorizzazioni di accesso di un **file** con il comando `az storage fs access show`. 

Questo esempio illustra come recuperare l'ACL di un file e quindi come stampare l'ACL nella console.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Nell'immagine seguente illustra l'output dopo aver recuperato l'ACL di una directory.

![Recuperare l'output di un ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

In questo esempio, l'utente proprietario è provvisto delle autorizzazioni di lettura, scrittura ed esecuzione. Il gruppo proprietario è provvisto delle sole autorizzazioni di lettura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Impostare un ACL

Usare il comando `az storage fs access set` per impostare l'ACL da una **directory**. 

Questo esempio illustra come impostare l'ACL in una directory per l'utente proprietario, il gruppo proprietario o altri utenti, quindi stampa l'ACL nella console.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Questo esempio illustra come impostare l'ACL *predefinito* in una directory per l'utente proprietario, il gruppo proprietario o altri utenti, infine stampa l'ACL nella console.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Usare il comando `az storage fs access set` per impostare l'ACL da un **file**. 

Questo esempio illustra come impostare l'ACL in un file per l'utente proprietario, il gruppo proprietario o altri utenti, quindi stampa l'ACL nella console.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Nell'immagine seguente illustra l'output dopo aver impostato l'ACL di un file.

![Recuperare l'output di un ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

In questo esempio, l'utente proprietario e il gruppo proprietario sono provvisti delle sole autorizzazioni di lettura e scrittura. Tutti gli altri utenti sono provvisti di autorizzazioni di scrittura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-an-acl"></a>Aggiornare un ACL

Un altro modo di impostare questa autorizzazione consiste nell'usare il comando `az storage fs access set`. 

Aggiornare l'ACL di una directory o di un file impostando il parametro `-permissions` sulla dettatura breve di un ACL.

Questo esempio illustra come aggiornare l'ACL di una **directory**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Questo esempio illustra come aggiornare l'ACL di un **file**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

È anche possibile aggiornare l'utente proprietario e il gruppo di una directory o di un file impostando i parametri `--owner` o `group` sull'ID entità oppure sul nome dell'entità utente (UPN) di un utente. 

Questo esempio illustra come modificare il proprietario di una directory. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Questo esempio illustra come modificare il proprietario di un file. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>Vedere anche

* [Esempi](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
* [Commenti e suggerimenti](https://github.com/Azure/azure-cli-extensions/issues)
* [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


