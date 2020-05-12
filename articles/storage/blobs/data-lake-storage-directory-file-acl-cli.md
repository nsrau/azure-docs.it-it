---
title: Usare l'interfaccia della riga di comando di Azure per i file & ACL in Azure Data Lake Storage Gen2
description: Usare l'interfaccia della riga di comando di Azure per gestire directory e elenchi di controllo di accesso (ACL) di file e directory in account di archiviazione che hanno uno spazio dei nomi gerarchico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 79a87f02c8730e0b2110e7475de721f11beda568
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120607"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Usare l'interfaccia della riga di comando di Azure per gestire directory, file e ACL in Azure Data Lake Storage Gen2

Questo articolo illustra come usare l'interfaccia della [riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) per creare e gestire directory, file e autorizzazioni negli account di archiviazione che hanno uno spazio dei nomi gerarchico. 

Mapping tra Gen1 [e Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)  |  [Invia commenti e suggerimenti](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con spazio dei nomi gerarchico (HNS) abilitato. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne uno.
> * Versione dell'interfaccia della riga di comando di Azure `2.5.1` o successiva.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Assicurarsi che sia installata la versione corretta dell'interfaccia della riga di comando di Azure

1. Aprire il [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)o, se l'interfaccia della riga di comando di Azure è stata [installata](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) localmente, aprire un'applicazione console comando come Windows PowerShell.

2. Verificare che la versione dell'interfaccia della riga di comando di Azure installata sia `2.5.1` o successiva usando il comando seguente.

   ```azurecli
    az --version
   ```
   Se la versione dell'interfaccia della riga di comando di Azure è inferiore `2.5.1` a, installare una versione successiva. Vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-account"></a>Connettersi all'account

1. Se si usa l'interfaccia della riga di comando di Azure in locale, eseguire il comando login.

   ```azurecli
   az login
   ```

   Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

   In caso contrario, aprire una pagina [https://aka.ms/devicelogin](https://aka.ms/devicelogin) del browser in e immettere il codice di autorizzazione visualizzato nel terminale. Quindi, accedere con le credenziali dell'account nel browser.

   Per altre informazioni sui diversi metodi di autenticazione, vedere [autorizzare l'accesso ai dati BLOB o della coda con l'interfaccia](../common/authorize-data-operations-cli.md)della riga di comando di Azure.

2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione che ospiterà il sito Web statico.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Sostituire il `<subscription-id>` valore del segnaposto con l'ID della sottoscrizione.

> [!NOTE]
> Nell'esempio presentato in questo articolo viene illustrata l'autorizzazione Azure Active Directory (AD). Per altre informazioni sui metodi di autorizzazione, vedere [autorizzare l'accesso ai dati BLOB o della coda con l'interfaccia](../common/authorize-data-operations-cli.md)della riga di comando di Azure

## <a name="create-a-file-system"></a>Creare un file system

Un file system funge da contenitore per i file. È possibile crearne uno usando il `az storage fs create` comando. 

In questo esempio viene creata una file system denominata `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-system-properties"></a>Mostra proprietà file system

È possibile stampare le proprietà di un file system nella console usando il `az storage fs show` comando.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-file-system-contents"></a>Elenca contenuti file system

Elencare il contenuto di una directory tramite il `az storage fs file list` comando.

In questo esempio viene elencato il contenuto di un file system denominato `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file-system"></a>Eliminare un file system

Eliminare un file system usando il `az storage fs delete` comando.

In questo esempio viene eliminata una file system denominata `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory usando il `az storage fs directory create` comando. 

In questo esempio viene aggiunta una directory denominata `my-directory` a un file System denominato `my-file-system` che si trova in un account denominato `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Mostra proprietà Directory

È possibile stampare le proprietà di una directory nella console usando il `az storage fs directory show` comando.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory tramite il `az storage fs directory move` comando.

Questo esempio rinomina una directory dal nome `my-directory` al nome `my-new-directory` nello stesso file System.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

In questo esempio una directory viene spostata in una file system denominata `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory tramite il `az storage fs directory delete` comando.

In questo esempio viene eliminata una directory denominata `my-directory` . 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Controllare se esiste una directory

Determinare se una directory specifica esiste nel file system usando il `az storage fs directory exists` comando.

Questo esempio Mostra se una directory denominata `my-directory` esiste nel `my-file-system` file System. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Scarica da una directory

Scaricare un file da una directory usando il `az storage fs file download` comando.

In questo esempio viene scaricato un file denominato `upload.txt` da una directory denominata `my-directory` . 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Elencare il contenuto di una directory tramite il `az storage fs file list` comando.

Questo esempio elenca il contenuto di una directory denominata `my-directory` che si trova nel `my-file-system` file System di un account di archiviazione denominato `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Caricare un file in una directory tramite il `az storage fs directory upload` comando.

In questo esempio viene caricato un file denominato `upload.txt` in una directory denominata `my-directory` . 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Mostra proprietà file

È possibile stampare le proprietà di un file nella console usando il `az storage fs file show` comando.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Rinominare o spostare un file

Rinominare o spostare un file utilizzando il `az storage fs file move` comando.

Questo esempio rinomina un file dal nome `my-file.txt` al nome `my-file-renamed.txt` .

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Eliminare un file

Eliminare un file usando il `az storage fs file delete` comando.

Questo esempio elimina un file denominato`my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-permissions"></a>Gestione autorizzazioni

È possibile ottenere, impostare e aggiornare le autorizzazioni di accesso di directory e file.

> [!NOTE]
> Se si usa Azure Active Directory (Azure AD) per autorizzare i comandi, assicurarsi che all'entità di sicurezza sia stato assegnato il [ruolo proprietario dati BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Per ulteriori informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere [controllo di accesso in Azure Data Lake storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Ottenere un ACL

Ottenere l'ACL di una **directory** tramite il `az storage fs access show` comando.

Questo esempio Mostra come ottenere l'ACL di una directory e quindi stampa l'ACL nella console.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Ottenere le autorizzazioni di accesso di un **file** tramite il `az storage fs access show` comando. 

Questo esempio Mostra come ottenere l'ACL di un file e quindi stamparlo nella console.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Nell'immagine seguente viene illustrato l'output dopo aver ricevuto l'ACL di una directory.

![Ottieni output ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

In questo esempio, l'utente proprietario dispone delle autorizzazioni di lettura, scrittura ed esecuzione. Il gruppo proprietario dispone solo delle autorizzazioni di lettura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [controllo di accesso in Azure Data Lake storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Imposta un ACL

Usare il `az storage fs access set` comando per impostare l'ACL di una **directory**. 

Questo esempio imposta l'ACL in una directory per l'utente proprietario, il gruppo proprietario o altri utenti, quindi stampa l'ACL nella console.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

In questo esempio viene impostato l'ACL *predefinito* in una directory per l'utente proprietario, il gruppo proprietario o altri utenti, quindi viene stampato l'ACL nella console.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Usare il `az storage fs access set` comando per impostare l'ACL di un **file**. 

In questo esempio viene impostato l'ACL in un file per l'utente proprietario, il gruppo proprietario o altri utenti, quindi viene stampato l'ACL nella console.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Nell'immagine seguente viene illustrato l'output dopo l'impostazione dell'ACL di un file.

![Ottieni output ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

In questo esempio, l'utente proprietario e il gruppo proprietario hanno solo le autorizzazioni di lettura e scrittura. Tutti gli altri utenti dispongono di autorizzazioni di scrittura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [controllo di accesso in Azure Data Lake storage Gen2](data-lake-storage-access-control.md).

### <a name="update-an-acl"></a>Aggiornare un ACL

Un altro modo per impostare questa autorizzazione consiste nell'usare il `az storage fs access set` comando. 

Aggiornare l'ACL di una directory o di un file impostando il `-permissions` parametro sulla forma abbreviata di un ACL.

Questo esempio aggiorna l'ACL di una **directory**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Questo esempio aggiorna l'ACL di un **file**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

È anche possibile aggiornare l'utente proprietario e il gruppo di una directory o di un file impostando i `--owner` parametri o sull' `group` ID entità o sul nome dell'entità utente (UPN) di un utente. 

Questo esempio Mostra come modificare il proprietario di una directory. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

In questo esempio viene modificato il proprietario di un file. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>Vedere anche

* [Mapping tra Gen1 e Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Fornire commenti e suggerimenti](https://github.com/Azure/azure-cli-extensions/issues)
* [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


