---
title: Usare l'interfaccia della riga di comando di Azure per i file & gli ACL in Azure Data Lake Storage Gen2 (anteprima)Use Azure CLI for files & ACLs in Azure Data Lake Storage Gen2 (preview)
description: Usare l'interfaccia della riga di comando di Azure per gestire le directory e gli elenchi di controllo di accesso (ACL) di file e directory negli account di archiviazione con uno spazio dei nomi gerarchico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77486135"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Usare l'interfaccia della riga di comando di Azure per gestire directory, file e ACL in Azure Data Lake Storage Gen2 (anteprima)Use Azure CLI to manage directories, files, and ACLs in Azure Data Lake Storage Gen2 (preview)

Questo articolo illustra come usare l'interfaccia della [riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) per creare e gestire directory, file e autorizzazioni negli account di archiviazione con uno spazio dei nomi gerarchico. 

> [!IMPORTANT]
> L'estensione `storage-preview` presente in questo articolo è attualmente in anteprima pubblica.

[Esempio](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | di[mapping da Gen1 a Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [Inviare commenti e suggerimentiSample](https://github.com/Azure/azure-cli-extensions/issues) Gen1 to Gen2 mapping Give feedback
## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con spazio dei nomi gerarchico abilitato. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne una.
> * Versione `2.0.67` dell'interfaccia della riga di comando di Azure o successiva.

## <a name="install-the-storage-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di archiviazioneInstall the storage CLI

1. Aprire [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)oppure, se l'interfaccia della riga di comando di Azure è stata [installata](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) in locale, aprire un'applicazione della console dei comandi, ad esempio Windows PowerShell.Open the Azure Cloud Shell , or if you've installed the Azure CLI locally, open a command console application such as Windows PowerShell.

2. Verificare che la versione dell'interfaccia della riga di comando di Azure installata sia `2.0.67` o successiva usando il comando seguente.

   ```azurecli
    az --version
   ```
   Se la versione dell'interfaccia `2.0.67`della riga di comando di Azure è inferiore a , installare una versione successiva. Vedere [Installare l'interfaccia della riga di comando di Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

3. Installare l'estensione `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Connettersi all'account

1. Se si usa l'interfaccia della riga di comando di Azure in locale, eseguire il comando di accesso.

   ```azurecli
   az login
   ```

   Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

   In caso contrario, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) aprire una pagina del browser e immettere il codice di autorizzazione visualizzato nel terminale. Quindi, accedere con le credenziali dell'account nel browser.

   Per altre informazioni sui diversi metodi di autenticazione, vedere Accedere con l'interfaccia della riga di comando di Azure.

2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione che ospiterà il sito Web statico.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Sostituire `<subscription-id>` il valore segnaposto con l'ID della sottoscrizione.

## <a name="create-a-file-system"></a>Creare un file system

Un file system funge da contenitore per i file. È possibile crearne `az storage container create` uno utilizzando il comando . 

In questo esempio viene `my-file-system`creato un file system denominato .

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla `az storage blob directory create` directory utilizzando il comando . 

In questo esempio `my-directory` viene aggiunta una `my-file-system` directory denominata a `mystorageaccount`un file system denominato che si trova in un account denominato .

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Mostra proprietà directory

È possibile stampare le proprietà di una `az storage blob show` directory nella console utilizzando il comando .

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare `az storage blob directory move` una directory utilizzando il comando .

In questo esempio viene rinominata una directory dal nome `my-directory` al nome `my-new-directory`.

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory `az storage blob directory delete` utilizzando il comando .

In questo esempio viene `my-directory`eliminata una directory denominata . 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Verificare l'presenza di una directory

Determinare se una directory specifica esiste nel `az storage blob directory exist` file system utilizzando il comando .

In questo esempio viene `my-directory` indicato se `my-file-system` nel file system è presente una directory denominata. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Scarica da una directory

Scaricare un file da una `az storage blob directory download` directory utilizzando il comando .

In questo esempio `upload.txt` viene scaricato `my-directory`un file denominato da una directory denominata . 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

In questo esempio viene scaricata un'intera directory.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Elencare il contenuto di `az storage blob directory list` una directory utilizzando il comando .

In questo esempio viene elencato il contenuto di una directory denominata `my-directory` che si trova nel `my-file-system` file system di un account di archiviazione denominato `mystorageaccount`. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Caricare un file in una `az storage blob directory upload` directory utilizzando il comando .

In questo esempio viene `upload.txt` caricato un `my-directory`file denominato in una directory denominata . 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

In questo esempio viene caricata un'intera directory.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Mostra proprietà file

È possibile stampare le proprietà di un `az storage blob show` file nella console utilizzando il comando .

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Rinominare o spostare un file

Rinominare o spostare `az storage blob move` un file utilizzando il comando .

In questo esempio viene rinominato un file dal nome `my-file.txt` al nome `my-file-renamed.txt`.

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Eliminare un file

Eliminare un file `az storage blob delete` utilizzando il comando .

In questo esempio viene eliminato un file denominato`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Gestione autorizzazioni

È possibile ottenere, impostare e aggiornare le autorizzazioni di accesso di directory e file.

> [!NOTE]
> Se si usa Azure Active Directory (Azure AD) per autorizzare i comandi, assicurarsi che all'entità di sicurezza sia stato assegnato il ruolo Proprietario dati BLOB di [archiviazione.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere Controllo di [accesso in Archiviazione di Azure Data Lake Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

### <a name="get-directory-and-file-permissions"></a>Ottenere le autorizzazioni per directory e fileGet directory and file permissions

Ottenere l'ACL **directory** di una `az storage blob directory access show` directory utilizzando il comando .

In questo esempio viene ottenuto l'ACL di una directory e quindi l'ACL viene stampato nella console.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Ottenere le autorizzazioni **file** di accesso `az storage blob access show` di un file utilizzando il comando . 

In questo esempio viene ottenuto l'ACL di un file e quindi viene stampato l'ACL nella console.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

L'immagine seguente mostra l'output dopo aver ottenuto l'ACL di una directory.

![Ottenere l'output ACLGet ACL output](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

In questo esempio, l'utente proprietario dispone delle autorizzazioni di lettura, scrittura ed esecuzione. Il gruppo proprietario dispone solo delle autorizzazioni di lettura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [Controllo di accesso in Azure Data Lake Storage Gen2.For](data-lake-storage-access-control.md)more information about access control lists, see Access control in Azure Data Lake Storage Gen2 .

### <a name="set-directory-and-file-permissions"></a>Impostare le autorizzazioni per directory e file

Utilizzare `az storage blob directory access set` il comando per impostare l'ACL di una **directory**. 

In questo esempio l'ACL viene impostato su una directory per l'utente proprietario, il gruppo proprietario o altri utenti, quindi l'ACL viene stampato nella console.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

In questo esempio viene impostato l'ACL *predefinito* in una directory per l'utente proprietario, il gruppo proprietario o altri utenti, quindi l'ACL viene stampato nella console.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Utilizzare `az storage blob access set` il comando per impostare l'acl di un **file**. 

In questo esempio viene impostato l'ACL su un file per l'utente proprietario, il gruppo proprietario o altri utenti e quindi viene stampato l'ACL nella console.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
L'immagine seguente mostra l'output dopo aver impostato l'ACL di un file.

![Ottenere l'output ACLGet ACL output](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

In questo esempio, l'utente proprietario e il gruppo proprietario dispongono solo delle autorizzazioni di lettura e scrittura. Tutti gli altri utenti dispongono di autorizzazioni di scrittura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [Controllo di accesso in Azure Data Lake Storage Gen2.For](data-lake-storage-access-control.md)more information about access control lists, see Access control in Azure Data Lake Storage Gen2 .

### <a name="update-directory-and-file-permissions"></a>Aggiornare le autorizzazioni per directory e file

Un altro modo per impostare `az storage blob directory access update` `az storage blob access update` questa autorizzazione consiste nell'utilizzare il comando o . 

Aggiornare l'ACL di una `-permissions` directory o di un file impostando il parametro sulla forma breve di un ACL.

In questo esempio viene aggiornato l'ACL di una **directory**.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

In questo esempio viene aggiornato l'ACL di un **file.**

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

È inoltre possibile aggiornare l'utente proprietario e il `--owner` `group` gruppo di una directory o di un file impostando i parametri o sull'ID entità o sul nome dell'entità utente (UPN) di un utente. 

In questo esempio viene modificato il proprietario di una directory. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

In questo esempio viene modificato il proprietario di un file. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Gestire i metadati definiti dall'utente

È possibile aggiungere metadati definiti dall'utente `az storage blob directory metadata update` a un file o a una directory utilizzando il comando con una o più coppie nome-valore.

In questo esempio vengono aggiunti metadati definiti dall'utente per una directory denominata `my-directory` directory.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

In questo esempio vengono illustrati tutti `my-directory`i metadati definiti dall'utente per la directory denominata .

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Vedere anche

* [Esempio](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Mapping da Gen1 a Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Commenti e suggerimenti](https://github.com/Azure/azure-cli-extensions/issues)
* [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Codice sorgente](https://github.com/Azure/azure-cli-extensions/tree/master/src)

