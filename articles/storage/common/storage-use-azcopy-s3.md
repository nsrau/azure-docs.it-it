---
title: Trasferire i dati in archiviazione di Azure dal bucket Amazon S3 usando AzCopy v10 | Microsoft Docs
description: Trasferire dati con AzCopy e Amazon S3 bucket
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: c0bc74ac0fe45f2502064340a0c3ce5b82694b06
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245700"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>Copiare dati da Amazon S3 bucket con AzCopy

AzCopy è un'utilità della riga di comando che è possibile usare per copiare BLOB o file da o verso un account di archiviazione. Questo articolo consente di copiare gli oggetti, cartelle e bucket da Amazon Web Services (AWS) S3 all'archiviazione blob di Azure tramite AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Scegliere come fornire le credenziali di autorizzazione

* Per autorizzare con archiviazione di Azure, usare un token di firma di accesso condiviso (SAS) o Azure Active Directory (AD).

* Per autorizzare con AWS S3, usare una chiave di accesso AWS e una chiave di accesso segreta.

### <a name="authorize-with-azure-storage"></a>Autorizzare con archiviazione di Azure

Vedere le [Introduzione ad AzCopy](storage-use-azcopy-v10.md) articolo per il download di AzCopy e scegliere come fornire le credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE]
> Gli esempi in questo articolo presuppongono che l'utente è autenticato la tua identità usando il `AzCopy login` comando. AzCopy Usa quindi l'account Azure AD per autorizzare l'accesso ai dati nell'archiviazione Blob.
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati blob, è possibile aggiungere tale token per l'URL della risorsa in ogni comando di AzCopy.
>
> Ad esempio: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autorizzare con AWS S3

Raccogliere la chiave di accesso AWS e la chiave di accesso segreta e quindi impostare queste variabili di ambiente:

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-folders-and-buckets"></a>Copiare gli oggetti, cartelle e bucket

AzCopy Usa il [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, in modo che i dati vengono copiati direttamente tra AWS S3 e server di archiviazione. Queste operazioni di copia non usano la larghezza di banda di rete del computer in uso.

### <a name="copy-an-object"></a>Copia un oggetto

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **Esempio** | `azcopy cp "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> Esempi in questo articolo usano basato su percorso URL per il bucket di AWS S3 (ad esempio: `http://s3.amazonaws.com/<bucket-name>`). 
>
> È anche possibile usare anche gli URL ospitato in stile virtuali (ad esempio: `http://bucket.s3.amazonaws.com`). 
>
> Per altre informazioni sull'hosting virtuale di bucket, vedere [virtuale che ospita di bucket]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-folder"></a>Copia una cartella

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<folder-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<folder-name>" --recursive=true` |
| **Esempio** | `azcopy cp "https://s3.amazonaws.com/mybucket/myfolder" "https://mystorageaccount.blob.core.windows.net/mycontainer/myfolder" --recursive=true` |

### <a name="copy-a-bucket"></a>Copiare un bucket

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **Esempio** | `azcopy cp "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Copiare tutti i bucket in tutte le aree

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy cp "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Esempio** | `azcopy cp "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Copiare tutti i bucket in un'area specifica S3

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy cp "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Esempio** | `azcopy cp "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Gestire le differenze nelle regole di denominazione di oggetti

AWS S3 ha un diverso set di convenzioni di denominazione per i nomi dei bucket rispetto ai contenitori blob di Azure. Per ulteriori informazioni sulle loro [qui](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Se si sceglie di copiare un gruppo di bucket per un account di archiviazione di Azure, l'operazione di copia potrebbe non riuscire a causa di differenze di denominazione.

AzCopy gestisce due dei problemi più comuni che possono sorgere; bucket che contengono punti e i bucket che contenere trattini consecutivi. I nomi dei bucket di AWS S3 può contenere punti e trattini consecutivi, ma non è un contenitore in Azure. AzCopy sostituisce i punti con trattini e caratteri di trattini consecutivi con un numero che rappresenta il numero di trattini consecutivi (ad esempio: un bucket denominato `my----bucket` diventa `my-4-bucket`. 

Inoltre, come AzCopy copia su file, verifica la presenza di conflitti di denominazione e tenta di risolverli. Ad esempio, se sono presenti bucket con il nome `bucket-name` e `bucket.name`, AzCopy risolve un bucket denominato `bucket.name` la prima per `bucket-name` e quindi a `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>Gestire le differenze nell'oggetto metadati

AWS S3 e Azure consentono diversi set di caratteri nei nomi delle chiavi dell'oggetto. Informazioni sui caratteri che utilizza AWS S3 [qui](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). Sul lato Azure, chiavi dell'oggetto blob conformi alle regole di denominazione per [ C# identificatori](https://docs.microsoft.com/dotnet/csharp/language-reference/).

Come parte di un AzCopy `copy` comando, è possibile fornire un valore per facoltativo di `s2s-invalid-metadata-handle` flag che specifica come gestire i file in cui i metadati del file contengono i nomi di chiave incompatibili. La tabella seguente descrive ogni valore del flag.

| Valore del flag | Descrizione  |
|--------|-----------|
| **ExcludeIfInvalid** | (Opzione predefinita) I metadati non sono incluso nell'oggetto trasferito. AzCopy registra un avviso. |
| **FailIfInvalid** | Gli oggetti non vengono copiati. AzCopy registra un errore e include tale errore nel conteggio non riuscito che viene visualizzato nel riepilogo del trasferimento.  |
| **RenameIfInvalid**  | AzCopy risolve la chiave di metadati non validi e copia l'oggetto in Azure usando i metadati risolti coppia chiave / valore. Per informazioni su esattamente quali passaggi AzCopy richiede di rinominare chiavi dell'oggetto, vedere la [come AzCopy Rinomina chiavi dell'oggetto](#rename-logic) sezione riportata di seguito. Se AzCopy è in grado di rinominare la chiave, quindi non verrà copiato l'oggetto. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Come AzCopy Rinomina chiavi dell'oggetto

AzCopy esegue questi passaggi:

1. Sostituisce i caratteri non validi con '_'.

2. Aggiunge la stringa `rename_` all'inizio di una nuova chiave valida.

   Questa chiave verrà usata per salvare i metadati originali **valore**.

3. Aggiunge la stringa `rename_key_` all'inizio di una nuova chiave valida.
   Questa chiave verrà usata per salvare i metadati originali non valido **chiave**.
   È possibile usare questa chiave per cercare di recuperare i metadati sul lato Azure poiché chiave dei metadati viene mantenuto come valore nel servizio di archiviazione Blob.

## <a name="next-steps"></a>Passaggi successivi

Trovare altri esempi in questi articoli:

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)

- [Trasferire dati con AzCopy e l'archiviazione blob](storage-use-azcopy-blobs.md)

- [Trasferire dati con AzCopy e archiviazione file](storage-use-azcopy-files.md)

- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)