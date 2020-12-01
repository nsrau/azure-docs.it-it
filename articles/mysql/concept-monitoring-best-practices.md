---
title: Procedure consigliate per il monitoraggio-database di Azure per MySQL
description: Questo articolo descrive le procedure consigliate per monitorare il database di Azure per MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354989"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>Procedure consigliate per il monitoraggio di database di Azure per MySQL-server singolo

Vengono fornite informazioni sulle procedure consigliate che è possibile utilizzare per monitorare le operazioni di database e garantire che le prestazioni non vengano compromesse quando le dimensioni dei dati aumentano. Quando si aggiungono nuove funzionalità alla piattaforma, verranno rifinite le procedure consigliate descritte in questa sezione.

## <a name="layout-of-the-current-monitoring-toolkit"></a>Layout del Toolkit di monitoraggio corrente

Database di Azure per MySQL offre strumenti e metodi che è possibile usare per monitorare facilmente l'utilizzo, aggiungere o rimuovere risorse, ad esempio CPU, memoria o I/O, risolvere i problemi potenziali e contribuire a migliorare le prestazioni di un database. È possibile [monitorare le metriche delle prestazioni](concepts-monitoring.md#metrics) a intervalli regolari per visualizzare i valori medi, massimi e minimi per diversi intervalli di tempo.

È possibile [impostare gli avvisi](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) per una soglia metrica, in modo da essere informati se il server ha raggiunto tali limiti e intraprendere le azioni appropriate.  

Monitorare il server di database per assicurarsi che le risorse assegnate al database siano in grado di gestire il carico di lavoro dell'applicazione. Se il database sta raggiungendo i limiti delle risorse, prendere in considerazione quanto segue:
    * Identificazione e ottimizzazione delle prime query che utilizzano risorse. 
    * Aggiunta di altre risorse tramite l'aggiornamento del livello di servizio.

### <a name="cpu-utilization"></a>Uso della CPU
Monitorare l'utilizzo della CPU e se il database esaurisce le risorse della CPU. Se l'utilizzo della CPU è pari al 90% o superiore a quello previsto per la scalabilità verticale del calcolo, aumentando il numero di Vcore o la scalabilità al piano tariffario successivo.  Verificare che la velocità effettiva o la concorrenza sia quella prevista per la scalabilità verticale e orizzontale della CPU. 

### <a name="memory"></a>Memoria 
La quantità di memoria disponibile per il server di database è proporzionale al [numero di Vcore](concepts-pricing-tiers.md). Verificare che la memoria sia sufficiente per il carico di lavoro. Eseguire il test di carico dell'applicazione per verificare che la memoria sia sufficiente per le operazioni di lettura e scrittura. Se il consumo di memoria del database aumenta spesso oltre una soglia definita, questo indica che è necessario aggiornare l'istanza aumentando il livello di prestazioni Vcore o superiore. Utilizzare [query Store](concepts-query-store.md), [consigli sulle prestazioni delle query](concepts-performance-recommendations.md) per identificare le query con la durata più lunga, più eseguita. Esplora le opportunità di ottimizzazione. 

### <a name="storage"></a>Archiviazione 
La [quantità di spazio di archiviazione](howto-create-manage-server-portal.md#scale-compute-and-storage) di cui è stato effettuato il provisioning per il server MySQL determina i IOPS per il server. Lo spazio di archiviazione usato dal servizio include i file di database, i log delle transazioni, i log del server e gli snapshot di backup. Assicurarsi che lo spazio su disco utilizzato non superi costantemente oltre il 85% dello spazio totale su disco con provisioning. In tal caso, è necessario eliminare o archiviare i dati dal server di database per liberare spazio. 

### <a name="network-traffic"></a>Traffico di rete 

Velocità effettiva di ricezione della rete **, velocità effettiva di trasmissione della rete** : frequenza del traffico di rete da e verso l'istanza di MySQL in megabyte al secondo. È necessario valutare il requisito di velocità effettiva per il server e monitorare costantemente il traffico se la velocità effettiva è inferiore al previsto. 

### <a name="database-connections"></a>Connessioni di database 
**Connessioni di database** : il numero di sessioni client connesse al database di Azure per MySQL deve essere allineato con i [limiti di connessione per le dimensioni dello SKU selezionato](concepts-server-parameters.md#max_connections) . 


## <a name="next-steps"></a>Passaggi successivi

- [Procedura consigliata per le prestazioni del database di Azure per MySQL](concept-performance-best-practices.md)
- [Procedura consigliata per le operazioni server con database di Azure per MySQL](concept-operation-excellence-best-practices.md)
