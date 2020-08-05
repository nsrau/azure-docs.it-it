---
title: Procedure consigliate per le operazioni a esecuzione prolungata in Azure Analysis Services | Microsoft Docs
description: Questo articolo descrive le procedure consigliate per le operazioni a esecuzione prolungata.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 5195d63180d45ad408256264e3cf10174e55cd96
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87551949"
---
# <a name="best-practices-for-long-running-operations"></a>Procedure consigliate per le operazioni a esecuzione prolungata

In Azure Analysis Services, un *nodo* rappresenta una macchina virtuale host in cui è in esecuzione una risorsa server. Alcune operazioni, ad esempio query con esecuzione prolungata, operazioni di aggiornamento e sincronizzazione delle query con scalabilità orizzontale, possono avere esito negativo se una risorsa server si sposta in un nodo diverso. I messaggi di errore comuni in questo scenario includono:

- "Si è verificato un errore durante il tentativo di individuare una richiesta XMLA con esecuzione prolungata. La richiesta potrebbe essere stata interrotta dall'aggiornamento del servizio o dal riavvio del server. "
- "Il processo con ID ' <guid> per il modello ' <database> ' è stato annullato a causa di un errore del servizio (inattività) con messaggio ' annullamento della richiesta di aggiornamento perché bloccato senza aggiornamenti. Si tratta di un problema del servizio interno. Inviare nuovamente il processo o un ticket per ricevere assistenza se il problema si verifica ripetutamente ".

Esistono molti motivi per cui le operazioni a esecuzione prolungata possono essere interrotte. Ad esempio, gli aggiornamenti in Azure, ad esempio: 
- Patch del sistema operativo 
- Aggiornamenti della sicurezza
- Aggiornamenti del servizio Azure Analysis Services
- Aggiornamenti Service Fabric. Service Fabric è un componente della piattaforma utilizzato da numerosi servizi cloud Microsoft, tra cui Azure Analysis Services.

Oltre agli aggiornamenti che si verificano nel servizio, si verifica un movimento naturale dei servizi tra i nodi a causa del bilanciamento del carico. I movimenti del nodo sono una parte prevista di un servizio cloud. Azure Analysis Services tenta di ridurre al minimo gli effetti dei movimenti del nodo, ma è impossibile eliminarli completamente. 

Oltre ai movimenti del nodo, è possibile che si verifichino altri errori. Ad esempio, un sistema di database dell'origine dati potrebbe essere offline o la connettività di rete andrà persa. Se durante l'aggiornamento, una partizione contiene 10 milioni righe e si verifica un errore alla riga 9 milionesimo, non è possibile riavviare l'aggiornamento nel momento in cui si è verificato l'errore. Il servizio deve iniziare di nuovo dall'inizio. 

## <a name="refresh-rest-api"></a>Aggiornare l'API REST

Le interruzioni del servizio possono essere complesse per operazioni a esecuzione prolungata, ad esempio l'aggiornamento dati. Azure Analysis Services include un'API REST che consente di ridurre gli effetti negativi sulle interruzioni del servizio. Per altre informazioni, vedere [aggiornamento asincrono con l'API REST](analysis-services-async-refresh.md).
 
Oltre all'API REST, è possibile usare altri approcci per ridurre al minimo i potenziali problemi durante le operazioni di aggiornamento con esecuzione prolungata. L'obiettivo principale è evitare di dover riavviare l'operazione di aggiornamento dall'inizio e di eseguire gli aggiornamenti in batch più piccoli di cui è possibile eseguire il commit nelle fasi. 
 
L'API REST consente un riavvio di questo tipo, ma non consente la flessibilità completa della creazione e dell'eliminazione di partizioni. Se uno scenario richiede operazioni di gestione dati complesse, la soluzione deve includere una forma di batch nella logica. Usando, ad esempio, le transazioni per elaborare i dati in più, i batch separati consentono un errore che non richiede il riavvio dall'inizio, bensì da un checkpoint intermedio. 
 
## <a name="scale-out-query-replicas"></a>Repliche di query con scalabilità orizzontale

Se si usa la logica REST o personalizzata, le query dell'applicazione client possono comunque restituire risultati incoerenti o intermedi durante l'elaborazione dei batch. Se i dati coerenti restituiti dalle query dell'applicazione client sono necessari durante l'elaborazione e i dati del modello sono in uno stato intermedio, è possibile usare la [scalabilità orizzontale](analysis-services-scale-out.md) con le repliche di query di sola lettura.

Usando le repliche di query di sola lettura, mentre gli aggiornamenti vengono eseguiti in batch, gli utenti delle applicazioni client possono continuare a eseguire query sul vecchio snapshot dei dati nelle repliche di sola lettura. Al termine dell'aggiornamento, è possibile eseguire un'operazione di sincronizzazione per aggiornare le repliche di sola lettura.


## <a name="next-steps"></a>Passaggi successivi

[Aggiornamento asincrono con l'API REST](analysis-services-async-refresh.md)  
[Scale-out di Azure Analysis Services](analysis-services-scale-out.md)  
[Disponibilità elevata di Azure Analysis Services](analysis-services-bcdr.md)  
[Linee guida per i tentativi per i servizi di Azure](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   

