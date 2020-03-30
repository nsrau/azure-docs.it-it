---
title: Copiare i dati da Amazon S3 ad Archiviazione di Azure usando AzCopy Documenti Microsoft
description: Trasferire dati con AzCopy e bucket Amazon S3
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a3180593eaf8c01c772fd761d88b5f5b9f7657ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941496"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Copiare i dati da Amazon S3 ad Archiviazione di Azure usando AzCopy data from Amazon S3 to Azure Storage by using AzCopy

AzCopy è un'utilità della riga di comando che è possibile usare per copiare BLOB o file in o da un account di archiviazione. Questo articolo illustra come copiare oggetti, directory e bucket da Amazon Web Services (AWS) S3 all'archiviazione BLOB di Azure tramite AzCopy.This article helps you copy objects, directories, and buckets from Amazon Web Services (AWS) S3 to Azure blob storage by using AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Scegliere la modalità di assegnazione delle credenziali di autorizzazione

* Per autorizzare con Archiviazione di Azure, usare Azure Active Directory (AD) o un token di firma di accesso condiviso.To authorize with the Azure Storage, use Azure Active Directory (AD) or a Shared Access Signature (SAS) token.

* Per autorizzare con AWS S3, utilizza una chiave di accesso AWS e una chiave di accesso segreta.

### <a name="authorize-with-azure-storage"></a>Autorizzare con Archiviazione di AzureAuthorize with Azure Storage

Vedere l'articolo Introduzione a [AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e scegliere come fornire le credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE]
> Negli esempi di questo articolo si presuppone che l'identità sia stata autenticata tramite il `AzCopy login` comando. AzCopy usa quindi l'account Azure AD per autorizzare l'accesso ai dati nell'archivio BLOB.
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, è possibile aggiungerlo all'URL della risorsa in ogni comando AzCopy.If you'd prefer use a SAS token to authorize access to blob data, then you can append that token to the resource URL in each AzCopy command.
>
> Ad esempio `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autorizzare con AWS S3

Raccogliere la chiave di accesso AWS e la chiave di accesso segreta, quindi impostare le seguenti variabili di ambiente:

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Macos** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Copiare oggetti, directory e bucket

AzCopy usa l'API [Put Block From URL,](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) quindi i dati vengono copiati direttamente tra AWS S3 e i server di archiviazione. Queste operazioni di copia non utilizzano la larghezza di banda di rete del computer.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Se si decide di rimuovere i dati dai bucket S3 dopo un'operazione di copia, assicurarsi di verificare che i dati siano stati copiati correttamente nell'account di archiviazione prima di rimuoverli.

> [!TIP]
> Gli esempi in questa sezione racchiudono gli argomenti di percorso tra virgolette singole (''). Utilizzare le virgolette singole in tutte le shell dei comandi, ad eccezione della shell dei comandi di Windows (cmd.exe). Se si utilizza una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

 Questi esempi funzionano anche con gli account che hanno uno spazio dei nomi gerarchico. [L'accesso multiprotocollo su Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) consente`blob.core.windows.net`di utilizzare la stessa sintassi URL ( ) in tali account. 

### <a name="copy-an-object"></a>Copiare un oggetto

Utilizzare la stessa`blob.core.windows.net`sintassi URL ( ) per gli account con uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Esempio** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> Gli esempi in questo articolo usano URL di tipo percorso per `http://s3.amazonaws.com/<bucket-name>`i bucket AWS S3 (ad esempio: ). 
>
> È inoltre possibile utilizzare URL virtual ospitati (ad `http://bucket.s3.amazonaws.com`esempio: ). 
>
> Per ulteriori informazioni sull'hosting virtuale dei bucket, vedere [Virtual Hosting of Buckets]](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-directory"></a>Copiare una directory

Utilizzare la stessa`blob.core.windows.net`sintassi URL ( ) per gli account con uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Esempio** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Copiare un bucket

Utilizzare la stessa`blob.core.windows.net`sintassi URL ( ) per gli account con uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Esempio** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Copiare tutti i bucket in tutte le aree

Utilizzare la stessa`blob.core.windows.net`sintassi URL ( ) per gli account con uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Esempio** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Copiare tutti i bucket in un'area S3 specifica

Utilizzare la stessa`blob.core.windows.net`sintassi URL ( ) per gli account con uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Esempio** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Gestire le differenze nelle regole di denominazione degli oggetti

AWS S3 ha un set diverso di convenzioni di denominazione per i nomi dei bucket rispetto ai contenitori BLOB di Azure. Si può leggere su di loro [qui](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Se si sceglie di copiare un gruppo di bucket in un account di archiviazione di Azure, l'operazione di copia potrebbe non riuscire a causa di differenze di denominazione.

AzCopy gestisce due dei problemi più comuni che possono verificarsi; bucket che contengono punti e bucket che contengono trattini consecutivi. I nomi dei bucket AWS S3 possono contenere punti e trattini consecutivi, ma un contenitore in Azure non può. AzCopy sostituisce i punti con trattini e trattini consecutivi con un numero che `my----bucket` `my-4-bucket`rappresenta il numero di trattini consecutivi (ad esempio: un bucket denominato diventa . 

Inoltre, mentre AzCopy copia i file, verifica i conflitti di denominazione e tenta di risolverli. Ad esempio, se sono presenti `bucket-name` bucket `bucket.name`con il nome e `bucket.name` , `bucket-name` AzCopy `bucket-name-2`risolve un bucket denominato prima e quindi in .

## <a name="handle-differences-in-object-metadata"></a>Gestire le differenze nei metadati degli oggetti

AWS S3 e Azure consentono diversi set di caratteri nei nomi delle chiavi oggetto. Puoi leggere informazioni sui caratteri utilizzati da AWS S3 [qui.](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys) Sul lato Azure, le chiavi degli oggetti BLOB aderiscono alle regole di denominazione per [gli identificatori di C.](https://docs.microsoft.com/dotnet/csharp/language-reference/)

Come parte di `copy` un comando AzCopy, è `s2s-invalid-metadata-handle` possibile fornire un valore per il flag facoltativo che specifica come si desidera gestire i file in cui i metadati del file contengono nomi di chiave incompatibili. Nella tabella seguente viene descritto ogni valore del flag.

| Valore flag | Descrizione  |
|--------|-----------|
| **ExcludeIfInvalid** | (Opzione predefinita) I metadati non sono inclusi nell'oggetto trasferito. AzCopy registra un avviso. |
| **FailIfInvalid** | Gli oggetti non vengono copiati. AzCopy registra un errore e include tale errore nel conteggio non riuscito visualizzato nel riepilogo del trasferimento.  |
| **RenameIfInvalid (RenameIfInvalid)**  | AzCopy risolve la chiave di metadati non valida e copia l'oggetto in Azure usando la coppia di valori di chiave dei metadati risolta. Per informazioni esattamente sui passaggi eseguiti da AzCopy per rinominare le chiavi oggetto, vedere la sezione [Come AzCopy rinomina le chiavi oggetto](#rename-logic) di seguito. Se AzCopy non è in grado di rinominare la chiave, l'oggetto non verrà copiato. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Modalità di ridenominazione delle chiavi oggetto da AzCopyHow AzCopy renames object keys

AzCopy esegue questi passaggi:AzCopy performs these steps:

1. Sostituisce i caratteri non validi con '_'.

2. Aggiunge la `rename_` stringa all'inizio di una nuova chiave valida.

   Questa chiave verrà utilizzata per salvare il **valore**originale dei metadati.

3. Aggiunge la `rename_key_` stringa all'inizio di una nuova chiave valida.
   Questa chiave verrà utilizzata per salvare i metadati originali **chiave**non valida .
   È possibile usare questa chiave per provare e ripristinare i metadati in Azure dal momento che la chiave dei metadati viene mantenuta come valore nel servizio di archiviazione BLOB.

## <a name="next-steps"></a>Passaggi successivi

Trova altri esempi in uno di questi articoli:

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)

- [Trasferire dati con AzCopy e l'archivio BLOB](storage-use-azcopy-blobs.md)

- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)