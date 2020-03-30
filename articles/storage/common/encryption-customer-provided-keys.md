---
title: Fornire una chiave di crittografia in una richiesta di archiviazione BLOBProvide an encryption key on a request to Blob storage
titleSuffix: Azure Storage
description: I client che effettuano richieste per l'archiviazione BLOB di Azure hanno la possibilità di fornire una chiave di crittografia in base alle richieste (anteprima). L'inclusione della chiave di crittografia nella richiesta offre un controllo granulare sulle impostazioni di crittografia per le operazioni di archiviazione BLOB.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c8a5555c5c33255fdc5902a115e7e9103a4e936f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79410064"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage-preview"></a>Fornire una chiave di crittografia in una richiesta di archiviazione BLOB (anteprima)Provide an encryption key on a request to Blob storage (preview)

I client che effettuano richieste per l'archiviazione BLOB di Azure hanno la possibilità di fornire una chiave di crittografia in base alle richieste (anteprima). L'inclusione della chiave di crittografia nella richiesta offre un controllo granulare sulle impostazioni di crittografia per le operazioni di archiviazione BLOB. Le chiavi fornite dal cliente possono essere archiviate in Archiviazione delle chiavi di Azure o in un altro archivio chiavi.

## <a name="encrypting-read-and-write-operations"></a>Crittografia delle operazioni di lettura e scritturaEncrypting read and write operations

Quando un'applicazione client fornisce una chiave di crittografia nella richiesta, Archiviazione di Azure esegue la crittografia e la decrittografia in modo trasparente durante la lettura e la scrittura di dati BLOB. Archiviazione di Azure scrive un hash SHA-256 della chiave di crittografia insieme al contenuto del BLOB. L'hash viene usato per verificare che tutte le operazioni successive sul BLOB usino la stessa chiave di crittografia.

Archiviazione di Azure non archivia né gestisce la chiave di crittografia inviata dal client con la richiesta. La chiave viene eliminata in modo sicuro non appena il processo di crittografia o decrittografia è completo.

Quando un client crea o aggiorna un BLOB usando una chiave fornita dal cliente nella richiesta, anche le richieste di lettura e scrittura successive per tale BLOB devono fornire la chiave. Se la chiave non viene fornita in una richiesta per un BLOB che è già stato crittografato con una chiave fornita dal cliente, la richiesta ha esito negativo con codice di errore 409 (conflitto).

Se l'applicazione client invia una chiave di crittografia nella richiesta e l'account di archiviazione viene crittografato anche usando una chiave gestita da Microsoft o una chiave gestita dal cliente, Archiviazione di Azure usa la chiave fornita nella richiesta di crittografia e decrittografia.

Per inviare la chiave di crittografia come parte della richiesta, un client deve stabilire una connessione sicura ad Archiviazione di Azure tramite HTTPS.

Ogni snapshot BLOB può avere la propria chiave di crittografia.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Intestazioni di richiesta per specificare le chiavi fornite dal clienteRequest headers for specifying customer-provided keys

Per le chiamate REST, i client possono usare le intestazioni seguenti per passare in modo sicuro le informazioni sulla chiave di crittografia in una richiesta all'archiviazione BLOB:For REST calls, clients can use the following headers to securely pass encryption key information on a request to Blob storage:

|Intestazione di richiesta | Descrizione |
|---------------|-------------|
|`x-ms-encryption-key` |Obbligatorio sia per le richieste di scrittura che per le richieste di lettura. Valore della chiave di crittografia AES-256 con codifica Base64. |
|`x-ms-encryption-key-sha256`| Obbligatorio sia per le richieste di scrittura che per le richieste di lettura. SHA256 con codifica Base64 della chiave di crittografia. |
|`x-ms-encryption-algorithm` | Obbligatorio per le richieste di scrittura, facoltativo per le richieste di lettura. Specifica l'algoritmo da utilizzare per crittografare i dati utilizzando la chiave specificata. Dev'essere AES256. |

La specifica delle chiavi di crittografia nella richiesta è facoltativa. Tuttavia, se si specifica una delle intestazioni elencate in precedenza per un'operazione di scrittura, è necessario specificarle tutte.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operazioni di archiviazione BLOB che supportano le chiavi fornite dal clienteBlob storage operations supporting customer-provided keys

Le operazioni di archiviazione BLOB seguenti supportano l'invio di chiavi di crittografia fornite dal cliente in una richiesta:The following Blob storage operations support sending customer-provided encryption keys on a request:

- [Put Blob](/rest/api/storageservices/put-blob)
- [Elenco Put Block](/rest/api/storageservices/put-block-list)
- [Put Block](/rest/api/storageservices/put-block)
- [Inserisci blocco da URL](/rest/api/storageservices/put-block-from-url)
- [Put Page](/rest/api/storageservices/put-page)
- [Inserisci pagina dall'URL](/rest/api/storageservices/put-page-from-url)
- [Append Block](/rest/api/storageservices/append-block)
- [Impostare le proprietà BLOB](/rest/api/storageservices/set-blob-properties)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Ruotare le chiavi fornite dal cliente

Per ruotare una chiave di crittografia usata per crittografare un BLOB, scaricare il BLOB e quindi caricarlo nuovamente con la nuova chiave di crittografia.

> [!IMPORTANT]
> Il portale di Azure non può essere usato per leggere o scrivere in un contenitore o in un BLOB crittografato con una chiave fornita nella richiesta.
>
> Assicurarsi di proteggere la chiave di crittografia fornita in una richiesta all'archiviazione BLOB in un archivio chiavi sicuro come L'insieme di credenziali delle chiavi di Azure.Be sure to protect the encryption key that you provide on a request to Blob storage in a secure key store like Azure Key Vault. Se si tenta di eseguire un'operazione di scrittura in un contenitore o blob senza la chiave di crittografia, l'operazione avrà esito negativo e si perderà l'accesso all'oggetto.

## <a name="next-steps"></a>Passaggi successivi

- [Specificare una chiave fornita dal cliente in una richiesta all'archiviazione BLOB con .NETSpecify a customer-provided key on a request to Blob storage with .NET](../blobs/storage-blob-customer-provided-key.md)
- [Crittografia di Archiviazione di Azure per i dati inattiviAzure Storage encryption for data at rest](storage-service-encryption.md)
