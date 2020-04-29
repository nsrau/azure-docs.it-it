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
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77486135"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Usare l'interfaccia della riga di comando di Azure per gestire directory, file e ACL in Azure Data Lake Storage Gen2 (anteprima)

Questo articolo illustra come usare l'interfaccia della [riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) per creare e gestire directory, file e autorizzazioni negli account di archiviazione che hanno uno spazio dei nomi gerarchico. 

> [!IMPORTANT]
> L' `storage-preview` estensione descritta in questo articolo è attualmente disponibile in anteprima pubblica.

[Sample](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | Il[mapping](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)[Give feedback](https://github.com/Azure/azure-cli-extensions/issues) di esempio da Gen1 a Gen2 fornisce commenti e suggerimenti | 
## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con spazio dei nomi gerarchico (HNS) abilitato. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne uno.
> * Versione `2.0.67` dell'interfaccia della riga di comando di Azure o successiva.

## <a name="install-the-storage-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di archiviazione

1. Aprire il [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)o, se l'interfaccia della riga di comando di Azure è stata [installata](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) localmente, aprire un'applicazione console comando come Windows PowerShell.

2. Verificare che la versione dell'interfaccia della riga di comando di `2.0.67` Azure installata sia o successiva usando il comando seguente.

   ```azurecli
    az --version
   ```
   Se la versione dell'interfaccia della riga di comando `2.0.67`di Azure è inferiore a, installare una versione successiva. Vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

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

   In caso contrario, aprire una pagina [https://aka.ms/devicelogin](https://aka.ms/devicelogin) del browser in e immettere il codice di autorizzazione visualizzato nel terminale. Quindi, accedere con le credenziali dell'account nel browser.

   Per altre informazioni sui diversi metodi di autenticazione, vedere Accedere con l'interfaccia della riga di comando di Azure.

2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione che ospiterà il sito Web statico.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Sostituire il `<subscription-id>` valore del segnaposto con l'ID della sottoscrizione.

## <a name="create-a-file-system"></a>Creare un file system

Un file system funge da contenitore per i file. È possibile crearne uno usando il `az storage container create` comando. 

In questo esempio viene creata una `my-file-system`file System denominata.

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory usando il `az storage blob directory create` comando. 

In questo esempio viene aggiunta una `my-directory` directory denominata a un `my-file-system` file System denominato che si trova in un `mystorageaccount`account denominato.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Mostra proprietà Directory

È possibile stampare le proprietà di una directory nella console usando il `az storage blob show` comando.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory tramite il `az storage blob directory move` comando.

Questo esempio rinomina una directory dal nome `my-directory` con il nome. `my-new-directory`

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory tramite il `az storage blob directory delete` comando.

In questo esempio viene eliminata `my-directory`una directory denominata. 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Controllare se esiste una directory

Determinare se una directory specifica esiste nel file system usando il `az storage blob directory exist` comando.

Questo esempio Mostra se una directory denominata `my-directory` esiste nel `my-file-system` file System. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Scarica da una directory

Scaricare un file da una directory usando il `az storage blob directory download` comando.

In questo esempio viene scaricato un `upload.txt` file denominato da una `my-directory`directory denominata. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

In questo esempio viene scaricata un'intera directory.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Elencare il contenuto di una directory tramite il `az storage blob directory list` comando.

Questo esempio elenca il contenuto di una directory denominata `my-directory` che si trova nel `my-file-system` file System di un account di archiviazione denominato `mystorageaccount`. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Caricare un file in una directory tramite il `az storage blob directory upload` comando.

In questo esempio viene caricato un file denominato `upload.txt` in una directory denominata `my-directory`. 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

Questo esempio carica un'intera directory.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Mostra proprietà file

È possibile stampare le proprietà di un file nella console usando il `az storage blob show` comando.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Rinominare o spostare un file

Rinominare o spostare un file utilizzando il `az storage blob move` comando.

Questo esempio rinomina un file dal nome `my-file.txt` al nome. `my-file-renamed.txt`

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Eliminare un file

Eliminare un file usando il `az storage blob delete` comando.

Questo esempio elimina un file denominato`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Gestione autorizzazioni

È possibile ottenere, impostare e aggiornare le autorizzazioni di accesso di directory e file.

> [!NOTE]
> Se si usa Azure Active Directory (Azure AD) per autorizzare i comandi, assicurarsi che all'entità di sicurezza sia stato assegnato il [ruolo proprietario dati BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Per ulteriori informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere [controllo di accesso in Azure Data Lake storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-directory-and-file-permissions"></a>Ottenere le autorizzazioni per directory e file

Ottenere l'ACL di una **directory** tramite il `az storage blob directory access show` comando.

Questo esempio Mostra come ottenere l'ACL di una directory e quindi stampa l'ACL nella console.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Ottenere le autorizzazioni di accesso di un **file** tramite il `az storage blob access show` comando. 

Questo esempio Mostra come ottenere l'ACL di un file e quindi stamparlo nella console.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Nell'immagine seguente viene illustrato l'output dopo aver ricevuto l'ACL di una directory.

![Ottieni output ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

In questo esempio, l'utente proprietario dispone delle autorizzazioni di lettura, scrittura ed esecuzione. Il gruppo proprietario dispone solo delle autorizzazioni di lettura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [controllo di accesso in Azure Data Lake storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Impostare le autorizzazioni per directory e file

Usare il `az storage blob directory access set` comando per impostare l'ACL di una **directory**. 

Questo esempio imposta l'ACL in una directory per l'utente proprietario, il gruppo proprietario o altri utenti, quindi stampa l'ACL nella console.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

In questo esempio viene impostato l'ACL *predefinito* in una directory per l'utente proprietario, il gruppo proprietario o altri utenti, quindi viene stampato l'ACL nella console.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Usare il `az storage blob access set` comando per impostare l'ACL di un **file**. 

In questo esempio viene impostato l'ACL in un file per l'utente proprietario, il gruppo proprietario o altri utenti, quindi viene stampato l'ACL nella console.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
Nell'immagine seguente viene illustrato l'output dopo l'impostazione dell'ACL di un file.

![Ottieni output ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

In questo esempio, l'utente proprietario e il gruppo proprietario hanno solo le autorizzazioni di lettura e scrittura. Tutti gli altri utenti dispongono di autorizzazioni di scrittura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [controllo di accesso in Azure Data Lake storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Aggiornare le autorizzazioni per directory e file

Un altro modo per impostare questa autorizzazione consiste nell'usare `az storage blob directory access update` il `az storage blob access update` comando o. 

Aggiornare l'ACL di una directory o di un file impostando il `-permissions` parametro sulla forma abbreviata di un ACL.

Questo esempio aggiorna l'ACL di una **directory**.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Questo esempio aggiorna l'ACL di un **file**.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

È anche possibile aggiornare l'utente proprietario e il gruppo di una directory o di un file `--owner` impostando i parametri o `group` sull'ID entità o sul nome dell'entità utente (UPN) di un utente. 

Questo esempio Mostra come modificare il proprietario di una directory. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

In questo esempio viene modificato il proprietario di un file. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Gestire i metadati definiti dall'utente

È possibile aggiungere metadati definiti dall'utente a un file o a una directory usando `az storage blob directory metadata update` il comando con una o più coppie nome/valore.

In questo esempio vengono aggiunti i metadati definiti dall'utente per `my-directory` una directory denominata directory.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

Questo esempio Mostra tutti i metadati definiti dall'utente per la `my-directory`directory denominata.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Vedere anche

* [Esempio](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Mapping tra Gen1 e Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Commenti e suggerimenti](https://github.com/Azure/azure-cli-extensions/issues)
* [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Codice sorgente](https://github.com/Azure/azure-cli-extensions/tree/master/src)

