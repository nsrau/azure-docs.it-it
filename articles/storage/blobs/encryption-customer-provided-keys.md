---
title: Fornire una chiave di crittografia per una richiesta all'archiviazione BLOB
titleSuffix: Azure Storage
description: I client che effettuano richieste nell'archiviazione BLOB di Azure possono fornire una chiave di crittografia in base alle singole richieste. L'inclusione della chiave di crittografia nella richiesta offre un controllo granulare sulle impostazioni di crittografia per le operazioni di archiviazione BLOB.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: abdc83019205fc39e1e85a53da7e49f8a7d4f11c
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91618727"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage"></a>Fornire una chiave di crittografia per una richiesta all'archiviazione BLOB

I client che effettuano richieste nell'archiviazione BLOB di Azure possono fornire una chiave di crittografia in base alle singole richieste. L'inclusione della chiave di crittografia nella richiesta offre un controllo granulare sulle impostazioni di crittografia per le operazioni di archiviazione BLOB. Le chiavi fornite dal cliente possono essere archiviate in Azure Key Vault o in un altro archivio chiavi.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="encrypting-read-and-write-operations"></a>Crittografia delle operazioni di lettura e scrittura

Quando un'applicazione client fornisce una chiave di crittografia per la richiesta, archiviazione di Azure esegue in modo trasparente la crittografia e la decrittografia durante la lettura e la scrittura dei dati BLOB. Archiviazione di Azure scrive un hash SHA-256 della chiave di crittografia insieme al contenuto del BLOB. L'hash viene usato per verificare che tutte le operazioni successive eseguite sul BLOB usino la stessa chiave di crittografia.

Archiviazione di Azure non archivia o gestisce la chiave di crittografia che il client invia con la richiesta. La chiave viene rimossa in modo sicuro non appena il processo di crittografia o decrittografia è stato completato.

Quando un client crea o aggiorna un BLOB usando una chiave fornita dal cliente nella richiesta, le successive richieste di lettura e scrittura per tale BLOB devono anche fornire la chiave. Se la chiave non viene fornita su una richiesta di un BLOB che è già stata crittografata con una chiave fornita dal cliente, la richiesta ha esito negativo con codice di errore 409 (conflitto).

Se l'applicazione client invia una chiave di crittografia per la richiesta e l'account di archiviazione viene crittografato anche usando una chiave gestita da Microsoft o una chiave gestita dal cliente, archiviazione di Azure usa la chiave fornita nella richiesta per la crittografia e la decrittografia.

Per inviare la chiave di crittografia come parte della richiesta, un client deve stabilire una connessione sicura ad archiviazione di Azure tramite HTTPS.

Ogni snapshot BLOB può avere una propria chiave di crittografia.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Intestazioni della richiesta per specificare le chiavi fornite dal cliente

Per le chiamate REST, i client possono usare le intestazioni seguenti per passare in modo sicuro le informazioni sulla chiave di crittografia per una richiesta all'archiviazione BLOB:

|Intestazione della richiesta | Descrizione |
|---------------|-------------|
|`x-ms-encryption-key` |Obbligatorio per le richieste di scrittura e di lettura. Valore della chiave di crittografia AES-256 con codifica Base64. |
|`x-ms-encryption-key-sha256`| Obbligatorio per le richieste di scrittura e di lettura. SHA256 con codifica Base64 della chiave di crittografia. |
|`x-ms-encryption-algorithm` | Obbligatorio per le richieste di scrittura, facoltativo per le richieste di lettura. Specifica l'algoritmo da utilizzare per la crittografia dei dati utilizzando la chiave specificata. Deve essere AES256. |

La specifica delle chiavi di crittografia nella richiesta è facoltativa. Tuttavia, se si specifica una delle intestazioni elencate in precedenza per un'operazione di scrittura, è necessario specificarle tutte.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operazioni di archiviazione BLOB che supportano chiavi fornite dal cliente

Le operazioni di archiviazione BLOB seguenti supportano l'invio di chiavi di crittografia fornite dal cliente su una richiesta:

- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Block List](/rest/api/storageservices/put-block-list)
- [Put Block](/rest/api/storageservices/put-block)
- [Inserisci blocco da URL](/rest/api/storageservices/put-block-from-url)
- [Put Page](/rest/api/storageservices/put-page)
- [Put Page from URL](/rest/api/storageservices/put-page-from-url)
- [Append Block](/rest/api/storageservices/append-block)
- [Set Blob Properties](/rest/api/storageservices/set-blob-properties)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Ruotare le chiavi fornite dal cliente

Per ruotare una chiave di crittografia usata per crittografare un BLOB, scaricare il BLOB e quindi caricarlo nuovamente con la nuova chiave di crittografia.

> [!IMPORTANT]
> Non è possibile usare la portale di Azure per leggere o scrivere in un contenitore o in un BLOB crittografato con una chiave fornita nella richiesta.
>
> Assicurarsi di proteggere la chiave di crittografia fornita su una richiesta di archiviazione BLOB in un archivio chiavi sicuro come Azure Key Vault. Se si tenta di eseguire un'operazione di scrittura su un contenitore o un BLOB senza la chiave di crittografia, l'operazione avrà esito negativo e si perderà l'accesso all'oggetto.

## <a name="next-steps"></a>Passaggi successivi

- [Specificare una chiave fornita dal cliente per una richiesta all'archivio BLOB con .NET](storage-blob-customer-provided-key.md)
- [Crittografia del servizio di archiviazione di Azure per dati inattivi](../common/storage-service-encryption.md)
