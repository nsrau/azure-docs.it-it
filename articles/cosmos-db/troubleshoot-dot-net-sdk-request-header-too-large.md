---
title: Risoluzione dei problemi dell'intestazione della richiesta troppo grande o 400 richiesta non valida nel Azure Cosmos DB
description: Come diagnosticare e correggere un'eccezione troppo grande per l'intestazione della richiesta
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 942af52c43a1d4526a42f20df40abf3f510e0ee6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294429"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Diagnosticare e risolvere i problemi dell'intestazione della richiesta Azure Cosmos DB messaggio troppo grande
Il messaggio dell'intestazione della richiesta troppo grande viene generato con il codice di errore HTTP 400. Questo errore si verifica se la dimensione dell'intestazione della richiesta è aumentata troppo grande e supera le dimensioni massime consentite. Si consiglia di usare la versione più recente dell'SDK. Assicurarsi di usare almeno la versione 3. x o 2. x, perché queste versioni aggiungono la traccia delle dimensioni dell'intestazione al messaggio dell'eccezione.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
L'intestazione della richiesta messaggio troppo grande viene visualizzato se la sessione o il token di continuazione è troppo grande. Nelle sezioni seguenti vengono descritte la causa e la soluzione del problema in ogni categoria.

### <a name="1-session-token-too-large"></a>1. token di sessione troppo grande

#### <a name="cause"></a>Causa:
400 la richiesta non valida è probabilmente causata da un token di sessione di grandi dimensioni. Se le istruzioni seguenti sono vere, viene verificato che il token di sessione sia troppo grande.

* L'errore si verifica in un'operazione del punto, ad esempio creazione, lettura, aggiornamento e così via, in cui non è presente un token di continuazione.
* L'eccezione è stata avviata senza apportare modifiche all'applicazione. Il token di sessione aumenta con l'aumentare del numero di partizioni nel contenitore. Il numero di partizioni aumenta con l'aumentare della quantità di dati o se la velocità effettiva è aumentata.

#### <a name="temporary-mitigation"></a>Mitigazione temporanea: 
Riavviare l'applicazione client per reimpostare tutti i token di sessione. Il token di sessione aumenterà infine le dimensioni precedenti che hanno causato il problema. Quindi, usare la soluzione nella sezione successiva per evitare completamente questo problema.

#### <a name="solution"></a>Soluzione:
1. Seguire le istruzioni riportate nell'articolo Suggerimenti per le prestazioni di [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) o [.NET v2](performance-tips.md) e convertire l'applicazione in modo che usi la modalità di connessione diretta con il protocollo TCP. La modalità diretta con il protocollo TCP non ha la restrizione delle dimensioni dell'intestazione come il protocollo HTTP, quindi evita questo problema. Assicurarsi di usare la versione più recente dell'SDK, che include una correzione per le operazioni di query quando l'interoperabilità del servizio non è disponibile.
2. Se la modalità di connessione diretta con il protocollo TCP non è un'opzione per il carico di lavoro, attenuarla modificando il [livello di coerenza del client](how-to-manage-consistency.md). Il token di sessione viene usato solo per la coerenza di sessione, ovvero il livello di coerenza predefinito per Azure Cosmos DB. Altri livelli di coerenza non usano il token di sessione.

### <a name="2-continuation-token-too-large"></a>2. token di continuazione troppo grande

#### <a name="cause"></a>Causa:
La richiesta 400 non valida si verifica nelle operazioni di query in cui viene utilizzato il token di continuazione. Se il token di continuazione è troppo grande o se le diverse query hanno dimensioni del token di continuazione diverse.
    
#### <a name="solution"></a>Soluzione:
1. Seguire le istruzioni riportate nell'articolo Suggerimenti per le prestazioni di [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) o [.NET v2](performance-tips.md) e convertire l'applicazione in modo che usi la modalità di connessione diretta con il protocollo TCP. La modalità diretta con il protocollo TCP non ha la restrizione delle dimensioni dell'intestazione come il protocollo HTTP, quindi evita questo problema. 
3. Se la modalità di connessione diretta con il protocollo TCP non è un'opzione per il carico di lavoro, provare a impostare l' `ResponseContinuationTokenLimitInKb` opzione. È possibile trovare questa opzione in `FeedOptions` per v2 o `QueryRequestOptions` in V3.

## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi relativi all'uso di Azure Cosmos DB .NET SDK
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md)
