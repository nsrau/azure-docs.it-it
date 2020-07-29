---
title: Risoluzione dei problemi Azure Cosmos DB eccezione non autorizzata
description: Come diagnosticare e correggere un'eccezione non autorizzata
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 872780ff65dabe5a931ae712c0f99b6e4f3e726d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294260"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exception"></a>Diagnosticare e risolvere i problemi Azure Cosmos DB eccezione non autorizzata

HTTP 401: la firma MAC trovata nella richiesta HTTP non corrisponde alla firma calcolata.
Se è stato ricevuto il messaggio di errore 401 seguente: "La firma MAC trovata nella richiesta HTTP non corrisponde alla firma calcolata." Questo problema può essere causato dagli scenari seguenti.

SDK precedenti è possibile che l'eccezione appaia come un'eccezione JSON non valida invece che con l'eccezione 401 non autorizzata corretta. Gli SDK più recenti gestiscono correttamente questo scenario e forniscono un messaggio di errore valido.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
Nell'elenco seguente sono contenute le cause note e le soluzioni per l'eccezione non autorizzata.

### <a name="1-key-was-not-properly-rotated-is-the-most-common-scenario"></a>1. la chiave non è stata ruotata correttamente è lo scenario più comune.
Poco dopo la rotazione di una chiave viene visualizzata la firma MAC 401 e infine la rotazione si interrompe senza modifiche. 

#### <a name="solution"></a>Soluzione:
La chiave è stata ruotata senza seguire le [procedure consigliate](secure-access-to-data.md#key-rotation). La rotazione della chiave dell'account Cosmos DB può richiedere da alcuni secondi a un certo numero di giorni, a seconda delle dimensioni dell'account di Cosmos DB.

### <a name="2-the-key-is-misconfigured"></a>2. la chiave non è configurata correttamente 
401 il problema di firma MAC sarà coerente e si verifica per tutte le chiamate che usano tale chiave

#### <a name="solution"></a>Soluzione:
La chiave non è configurata correttamente nell'applicazione e sta usando la chiave non corretta per l'account o l'intera chiave non è stata copiata.

### <a name="3-the-application-is-using-the-read-only-keys-for-write-operations"></a>3. l'applicazione usa le chiavi di sola lettura per le operazioni di scrittura
401 il problema di firma MAC si verifica solo per operazioni di scrittura, ad esempio crea o Sostituisci, ma la richiesta di lettura ha esito positivo.

#### <a name="solution"></a>Soluzione:
Impostare l'applicazione in modo che usi una chiave di lettura/scrittura per consentire il corretto completamento delle operazioni.

### <a name="4-race-condition-with-create-container"></a>4. race condition con create container
401 il problema di firma MAC viene visualizzato poco dopo la creazione di un contenitore. Questo si verifica solo fino al completamento della creazione del contenitore.

#### <a name="solution"></a>Soluzione:
È presente una race condition con la creazione del contenitore. Un'istanza dell'applicazione sta provando ad accedere al contenitore prima del completamento della creazione del contenitore. Lo scenario più comune per questa race condition è se l'applicazione è in esecuzione e il contenitore viene eliminato e ricreato con lo stesso nome. L'SDK tenterà di usare il nuovo contenitore, ma la creazione del contenitore è ancora in corso, quindi non contiene le chiavi.

## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi relativi all'uso di Azure Cosmos DB .NET SDK
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md)