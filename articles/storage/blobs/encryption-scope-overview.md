---
title: Ambiti di crittografia per l'archiviazione BLOB (anteprima)
description: Gli ambiti di crittografia offrono la possibilità di gestire la crittografia a livello del contenitore o di un singolo BLOB. È possibile usare gli ambiti di crittografia per creare confini sicuri tra i dati che si trovano nello stesso account di archiviazione, ma appartenenti a clienti diversi.
services: storage
author: tamram
ms.service: storage
ms.date: 09/22/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 6fb3c9b6dbbab036ddb00edd7e1d5980bb425ebe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326117"
---
# <a name="encryption-scopes-for-blob-storage-preview"></a>Ambiti di crittografia per l'archiviazione BLOB (anteprima)

Gli ambiti di crittografia offrono la possibilità di gestire la crittografia a livello del contenitore o di un singolo BLOB. È possibile usare gli ambiti di crittografia per creare confini sicuri tra i dati che si trovano nello stesso account di archiviazione, ma appartenenti a clienti diversi.

Per impostazione predefinita, un account di archiviazione viene crittografato con una chiave con ambito per l'intero account di archiviazione. Con un ambito di crittografia, è possibile specificare che uno o più contenitori sono crittografati con una chiave che ha come ambito solo i contenitori.

È possibile scegliere di usare chiavi gestite da Microsoft o chiavi gestite dal cliente archiviate in Azure Key Vault per proteggere e controllare l'accesso alla chiave che crittografa i dati. Diversi ambiti di crittografia nello stesso account di archiviazione possono usare chiavi gestite da Microsoft o gestite dal cliente.

Dopo aver creato un ambito di crittografia, è possibile specificare tale ambito di crittografia per una richiesta di creazione di un contenitore o di un BLOB. Per ulteriori informazioni su come creare un ambito di crittografia, vedere [creare e gestire ambiti di crittografia (anteprima)](encryption-scope-manage.md).

> [!NOTE]
> Gli ambiti di crittografia non sono supportati con account di archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) o con archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS) durante la fase di anteprima.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!IMPORTANT]
> L'anteprima degli ambiti di crittografia è destinata solo all'uso non in produzione. I contratti di servizio (SLA) di produzione non sono al momento disponibili.
>
> Per evitare costi imprevisti, assicurarsi di disabilitare gli ambiti di crittografia attualmente non necessari.

## <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Creare un contenitore o un BLOB con un ambito di crittografia

I BLOB creati in un ambito di crittografia vengono crittografati con la chiave specificata per tale ambito. È possibile specificare un ambito di crittografia per un singolo BLOB quando si crea il BLOB oppure è possibile specificare un ambito di crittografia predefinito quando si crea un contenitore. Quando si specifica un ambito di crittografia predefinito al livello di un contenitore, tutti i BLOB in tale contenitore vengono crittografati con la chiave associata all'ambito predefinito.

Quando si crea un BLOB in un contenitore con un ambito di crittografia predefinito, è possibile specificare un ambito di crittografia che esegue l'override dell'ambito di crittografia predefinito se il contenitore è configurato in modo da consentire le sostituzioni dell'ambito di crittografia predefinito. Per impedire le sostituzioni dell'ambito di crittografia predefinito, configurare il contenitore per negare gli override per un singolo BLOB.

Le operazioni di lettura su un BLOB che appartiene a un ambito di crittografia si verificano in modo trasparente, purché l'ambito di crittografia non sia disabilitato.

## <a name="disable-an-encryption-scope"></a>Disabilitare un ambito di crittografia

Quando si disabilita un ambito di crittografia, le operazioni di lettura o scrittura successive eseguite con l'ambito di crittografia avranno esito negativo con codice di errore HTTP 403 (accesso negato). Se si riabilita l'ambito di crittografia, le operazioni di lettura e scrittura proseguiranno di nuovo normalmente.

Quando un ambito di crittografia è disabilitato, non viene più fatturato. Disabilitare gli ambiti di crittografia non necessari per evitare addebiti superflui.

Se l'ambito di crittografia è protetto con chiavi gestite dal cliente, è anche possibile eliminare la chiave associata nell'insieme di credenziali delle chiavi per disabilitare l'ambito di crittografia. Tenere presente che le chiavi gestite dal cliente sono protette dall'eliminazione temporanea e dall'eliminazione della protezione nell'insieme di credenziali delle chiavi e una chiave eliminata è soggetta al comportamento definito da tali proprietà. Per ulteriori informazioni, vedere uno degli argomenti seguenti nella documentazione di Azure Key Vault:

- [Come usare la funzionalità di eliminazione temporanea con PowerShell](../../key-vault/general/soft-delete-powershell.md)
- [Come usare l'eliminazione temporanea con l'interfaccia della riga di comando](../../key-vault/general/soft-delete-cli.md)

> [!NOTE]
> Non è possibile eliminare un ambito di crittografia.

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](../common/storage-service-encryption.md)
- [Creare e gestire gli ambiti di crittografia (anteprima)](encryption-scope-manage.md)
- [Chiavi gestite dal cliente per la crittografia di archiviazione di Azure](../common/customer-managed-keys-overview.md)
- [Cos'è l'insieme di credenziali chiave di Azure?](../../key-vault/general/overview.md)
