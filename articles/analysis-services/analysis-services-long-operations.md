---
title: Procedure consigliate per le operazioni a esecuzione prolungata in Azure Analysis Services Documenti Microsoft
description: In questo articolo vengono descritte le procedure consigliate per le operazioni a esecuzione prolungata.
author: minewiskan
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 3f6b2194cc422a827bbc7a15c012173b3f814b52
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428108"
---
# <a name="best-practices-for-long-running-operations"></a>Procedure consigliate per le operazioni a esecuzione prolungataBest practices for long running operations

In Azure Analysis Services un *nodo* rappresenta una macchina virtuale host in cui è in esecuzione una risorsa server. Alcune operazioni, ad esempio le query a esecuzione prolungata, le operazioni di aggiornamento e la sincronizzazione con scalabilità orizzontale delle query, possono avere esito negativo se una risorsa server viene spostata in un nodo diverso. I messaggi di errore comuni in questo scenario includono:Common error messages in this scenario include:

- "Si è verificato un errore durante il tentativo di individuare una richiesta XMLA a esecuzione prolungata. La richiesta potrebbe essere stata interrotta dall'aggiornamento del servizio o dal riavvio del server."
- "Processo con<guid>ID '<database>per il modello ' è stato annullato a causa di un errore del servizio (inattività) con il messaggio 'Annullamento della richiesta di aggiornamento poiché è stato bloccato senza aggiornamenti. Si tratta di un problema interno del servizio. Inviare nuovamente il lavoro o inviare un ticket per ottenere assistenza se questo problema si verifica ripetutamente."

Esistono molti motivi per cui le operazioni a esecuzione prolungata possono essere interrotte. Ad esempio, gli aggiornamenti in Azure, ad esempio:For example, updates in Azure such as: 
- Patch del sistema operativo 
- Aggiornamenti per la sicurezza
- Aggiornamenti del servizio Azure Analysis Services
- Service Fabric aggiorna. Service Fabric is a platform component used by a number of Microsoft cloud services, including Azure Analysis Services.

Oltre agli aggiornamenti che si verificano nel servizio, esiste un movimento naturale di servizi tra i nodi a causa del bilanciamento del carico. I movimenti dei nodi sono una parte prevista di un servizio cloud. Azure Analysis Services tenta di ridurre al minimo gli impatti derivanti dagli movimenti dei nodi, ma è impossibile eliminarli completamente. 

Oltre ai movimenti del nodo, possono verificarsi altri errori. Ad esempio, un sistema di database di origine dati potrebbe essere offline o la connettività di rete viene persa. Se durante l'aggiornamento, una partizione ha 10 milioni di righe e si verifica un errore in corrispondenza del 9 milionesimo, non è possibile riavviare l'aggiornamento nel punto di errore. Il servizio deve ricominciare dall'inizio. 

## <a name="refresh-rest-api"></a>Aggiorna API REST

Le interruzioni del servizio possono essere difficili per le operazioni a esecuzione prolungata, ad esempio l'aggiornamento dei dati. Azure Analysis Services include un'API REST per ridurre gli impatti negativi delle interruzioni del servizio. Per altre informazioni, vedere [Aggiornamento asincrono con l'API REST.](analysis-services-async-refresh.md)
 
Oltre all'API REST, esistono altri approcci che è possibile usare per ridurre al minimo i potenziali problemi durante le operazioni di aggiornamento a esecuzione prolungata. L'obiettivo principale consiste nell'evitare di dover riavviare l'operazione di aggiornamento dall'inizio ed eseguire invece gli aggiornamenti in batch più piccoli di cui è possibile eseguire il commit in fasi. 
 
L'API REST consente tale riavvio, ma non consente la massima flessibilità di creazione ed eliminazione delle partizioni. Se uno scenario richiede operazioni di gestione dei dati complesse, la soluzione deve includere una qualche forma di invio in batch nella relativa logica. Ad esempio, utilizzando le transazioni per elaborare i dati in più batch separati consente a un errore non richiedere il riavvio dall'inizio, ma da un checkpoint intermedio. 
 
## <a name="scale-out-query-replicas"></a>Repliche di query con scalabilità orizzontaleScale-out query replicas

Utilizzando REST o logica personalizzata, le query dell'applicazione client possono comunque restituire risultati incoerenti o intermedi durante l'elaborazione dei batch. Se sono necessari dati coerenti restituiti dalle query dell'applicazione client durante l'elaborazione e i dati del modello sono in uno stato intermedio, è possibile usare la [scalabilità](analysis-services-scale-out.md) orizzontale con le repliche di query di sola lettura.

Utilizzando repliche di query di sola lettura, mentre gli aggiornamenti vengono eseguiti in batch, gli utenti dell'applicazione client possono continuare a eseguire query sullo snapshot precedente dei dati nelle repliche di sola lettura. Al termine degli aggiornamenti, è possibile eseguire un'operazione synch per aggiornare le repliche di sola lettura.


## <a name="next-steps"></a>Passaggi successivi

[Aggiornamento asincrono con l'API REST](analysis-services-async-refresh.md)  
[Ridimensionamento orizzontale di Azure Analysis Services](analysis-services-scale-out.md)  
[Disponibilità elevata di Azure Analysis Services](analysis-services-bcdr.md)  
[Indicazioni sui tentativi per i servizi di AzureRetry guidance for Azure services](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   

