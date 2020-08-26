---
title: Risolvere i problemi relativi a un messaggio "intestazione richiesta troppo grande" o a una richiesta non valida 400 in Azure Cosmos DB
description: Informazioni su come diagnosticare e correggere un'eccezione troppo grande per l'intestazione della richiesta.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a4d8a919d1881f61e490f135cc8fb1659c64cbd3
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871123"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Diagnosticare e risolvere i problemi Azure Cosmos DB messaggio "intestazione richiesta troppo grande"
Il messaggio "intestazione richiesta troppo grande" viene generato con un codice di errore HTTP 400. Questo errore si verifica se la dimensione dell'intestazione della richiesta è aumentata in modo tale che superi le dimensioni massime consentite. Si consiglia di usare la versione più recente dell'SDK. Usare almeno la versione 3. x o 2. x, perché queste versioni aggiungono la traccia delle dimensioni dell'intestazione al messaggio dell'eccezione.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
Il messaggio "intestazione richiesta troppo grande" viene visualizzato se la sessione o il token di continuazione è troppo grande. Le sezioni seguenti descrivono la causa del problema e la relativa soluzione in ogni categoria.

### <a name="session-token-is-too-large"></a>Il token di sessione è troppo grande

#### <a name="cause"></a>Causa:
È probabile che si verifichi una richiesta non valida 400 perché il token di sessione è troppo grande. Se le istruzioni seguenti sono vere, il token di sessione è troppo grande:

* L'errore si verifica in operazioni su punti, ad esempio creazione, lettura e aggiornamento, in cui non è disponibile un token di continuazione.
* L'eccezione è stata avviata senza apportare modifiche all'applicazione. Il token di sessione cresce man mano che il numero di partizioni aumenta nel contenitore. Il numero di partizioni aumenta con l'aumentare della quantità di dati o se la velocità effettiva è aumentata.

#### <a name="temporary-mitigation"></a>Mitigazione temporanea: 
Riavviare l'applicazione client per reimpostare tutti i token di sessione. Infine, il token di sessione aumenterà fino alle dimensioni precedenti che hanno causato il problema. Per evitare il problema completamente, usare la soluzione nella sezione successiva.

#### <a name="solution"></a>Soluzione:
1. Seguire le istruzioni riportate negli articoli suggerimenti sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) o [.NET v2](performance-tips.md) . Convertire l'applicazione in modo che utilizzi la modalità di connessione diretta con il Transmission Control Protocol (TCP). La modalità di connessione diretta con il protocollo TCP non ha la restrizione delle dimensioni dell'intestazione come il protocollo HTTP, quindi evita questo problema. Assicurarsi di usare la versione più recente dell'SDK, che include una correzione per le operazioni di query quando l'interoperabilità del servizio non è disponibile.
1. Se la modalità di connessione diretta con il protocollo TCP non è un'opzione per il carico di lavoro, attenuarla modificando il [livello di coerenza del client](how-to-manage-consistency.md). Il token di sessione viene usato solo per la coerenza di sessione, ovvero il livello di coerenza predefinito per Azure Cosmos DB. Altri livelli di coerenza non usano il token di sessione.

### <a name="continuation-token-is-too-large"></a>Il token di continuazione è troppo grande

#### <a name="cause"></a>Causa:
La richiesta 400 non valida si verifica nelle operazioni di query in cui viene usato il token di continuazione se il token di continuazione è troppo grande o se le diverse query hanno dimensioni del token di continuazione diverse.
    
#### <a name="solution"></a>Soluzione:
1. Seguire le istruzioni riportate negli articoli suggerimenti sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) o [.NET v2](performance-tips.md) . Convertire l'applicazione in modo che utilizzi la modalità di connessione diretta con il protocollo TCP. La modalità di connessione diretta con il protocollo TCP non ha la restrizione delle dimensioni dell'intestazione come il protocollo HTTP, quindi evita questo problema. 
1. Se la modalità di connessione diretta con il protocollo TCP non è un'opzione per il carico di lavoro, impostare l' `ResponseContinuationTokenLimitInKb` opzione. È possibile trovare questa opzione in `FeedOptions` in v2 o `QueryRequestOptions` in V3.

## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi quando si usa Azure Cosmos DB .NET SDK.
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).
