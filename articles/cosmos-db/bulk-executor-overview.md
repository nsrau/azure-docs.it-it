---
title: Eseguire importazioni e aggiornamenti bulk di dati in Azure Cosmos DB usando la libreria dell'executor bulk
description: Eseguire operazioni bulk in Azure Cosmos DB tramite API di importazione e aggiornamento bulk rese disponibili dalla libreria dell'executor bulk.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: e0176af0ce77e9306f6f0031122f8ba8a7b27e61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894479"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Panoramica della libreria dell'executor bulk di Azure Cosmos DB
 
Azure Cosmos DB è un servizio di database distribuito in modo globale veloce e flessibile che è progettato in modo elastico con scalabilità orizzontale per supportare: 

* Grande velocità effettiva di lettura e scrittura (milioni di operazioni al secondo).  
* Archiviazione di volumi elevati di (centinaia di terabyte o anche di più) dati transazionali e operativi con una latenza prevedibile al millisecondo.  

La libreria dell'executor bulk consente di sfruttare la velocità effettiva e la capacità di archiviazione eccezionali disponibili. La libreria dell'executor bulk consente anche di eseguire operazioni bulk in Azure Cosmos DB tramite API di importazione e aggiornamento bulk. Altre informazioni sulle funzionalità della libreria dell'executor bulk sono disponibili nelle sezioni seguenti. 

> [!NOTE] 
> Attualmente la libreria dell'executor bulk supporta le operazioni di importazione e aggiornamento ed è supportata solo dagli account delle API SQL e Gremlin di Azure Cosmos DB.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Caratteristiche principali della libreria dell'executor bulk  
 
* Riduce in modo significativo le risorse di calcolo del lato client necessarie per saturare la velocità effettiva allocata a un contenitore. Un'applicazione a thread singolo che scrive i dati usando che l'API di importazione in blocco raggiunge una velocità effettiva di scrittura 10 volte maggiore rispetto a un'applicazione a thread multipli che scrive i dati in parallelo mentre satura il CPU del computer del client.  

* Evita le tediose attività di scrittura della logica delle applicazioni per gestire la limitazione della frequenza delle richieste, i timeout delle richieste e altre eccezioni temporanee consentendo di controllare queste condizioni in modo efficiente all'interno della libreria.  

* Fornisce un meccanismo semplificato per le applicazioni che eseguono operazioni bulk per la scalabilità orizzontale. Una singola istanza dell'executor bulk in esecuzione in una macchina virtuale di Azure può usare più di 500 K unità richiesta al secondo. È possibile ottenere una velocità effettiva superiore tramite l'aggiunta di istanze aggiuntive nel client di singole macchine virtuali.  
 
* È possibile eseguire l'importazione del bulk di più di un terabyte di dati entro un'ora usando un'architettura di scalabilità orizzontale.  

* È possibile aggiornare in blocco i dati esistenti nei contenitori di Azure Cosmos DB come patch. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Funzionamento dell'Executor in blocco 

Quando un'operazione in blocco per importare o aggiornare i documenti viene attivata con un batch di entità, esse vengono mescolate inizialmente in dei bucket che corrispondono ai loro intervalli di chiavi di partizione di Azure Cosmos DB. In ogni bucket che corrisponde a una partizione dell'intervallo delle chiavi, esse sono suddivise in mini-batch e ogni mini batch agisce come un payload di cui viene eseguito il commit sul lato server. La libreria dell'executor bulk dispone di ottimizzazioni predefinite per l'esecuzione simultanea di questi batch mini sia all'interno degli intervalli sia tra gli intervalli delle chiavi di partizione. L'immagine seguente illustra come l'executor bulk raggruppa i dati in chiavi di partizione differenti:  

![Architettura dell'executor Bulk](./media/bulk-executor-overview/bulk-executor-architecture.png)

La libreria dell'Executor Bulk garantisce di usare drasticamente la velocità effettiva allocata a una raccolta. Usa un  [meccanismo di controllo della congestione in stile AIMD](https://tools.ietf.org/html/rfc5681) per ogni intervallo di chiavi di partizione di Azure Cosmos DB per gestire in modo efficiente la limitazione della frequenza delle richieste e i timeout. 

## <a name="next-steps"></a>Fasi successive 
  
* Per altre informazioni, provare le applicazioni di esempio che usano la libreria dell'Executor in bloco in [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md).  
* Consultare le note sulla versione e le informazioni dell'SDK dell'executor bulk in [.NET](sql-api-sdk-bulk-executor-dot-net.md) e [Java](sql-api-sdk-bulk-executor-java.md).
* La libreria dell'Executor Bulk è integrata nel connettore Spark DB Cosmos, per altre informazioni, vedere l'articolo [connettore di Azure Cosmos DB Spark](spark-connector.md).  
* La libreria dell'executor bulk è integrata anche in una nuova versione del [connettore di Azure Cosmos DB](https://aka.ms/bulkexecutor-adf-v2) per consentire ad Azure Data Factory di copiare i dati.
