---
title: Domande frequenti sulla modalità di scalabilità automatica in Azure Cosmos DB
description: Domande frequenti sulla velocità effettiva con provisioning automatico per i database e i contenitori Azure Cosmos DB
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 413e9c71850b047172859c681cdbb422b7def032
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196465"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Domande frequenti sulla velocità effettiva con provisioning automatico in Azure Cosmos DB

Con la velocità effettiva con provisioning in modalità di scalabilità automatica, Azure Cosmos DB gestirà e scalerà automaticamente le UR/sec del contenitore o del database in base all'utilizzo. Questo articolo risponde alle domande frequenti sulla scalabilità automatica.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="is-autoscale-mode-supported-for-all-apis"></a>La modalità di scalabilità automatica è supportata per tutte le API?
Sì, la modalità di scalabilità automatica è supportata per tutte le API: Core (SQL), Gremlin, Table, Cassandra e API per MongoDB.

### <a name="is-autoscale-mode-supported-for-multi-master-accounts"></a>La modalità di scalabilità automatica è supportata per gli account multimaster?
Sì, la modalità di scalabilità automatica è supportata per gli account multimaster. Le UR/sec massime sono disponibili in ogni area aggiunta all'account Cosmos. 

### <a name="what-is-the-pricing-for-autoscale"></a>Quali sono i prezzi per la scalabilità automatica?
Per informazioni dettagliate, vedere la pagina relativa ai [prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) Azure Cosmos DB. 

### <a name="how-do-i-enable-autoscale-for-my-containers-or-databases"></a>Ricerca per categorie abilitare la scalabilità automatica per i contenitori o i database?
La modalità di scalabilità automatica può essere abilitata nei nuovi contenitori e database creati usando il portale di Azure. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autoscale-mode"></a>È disponibile l'interfaccia della riga di comando o il supporto SDK per creare contenitori o database con la modalità di scalabilità automatica
Attualmente, è possibile creare risorse solo con la modalità di ridimensionamento automatico dal portale di Azure. Il supporto per l'interfaccia della riga di comando e SDK non è ancora disponibile.

### <a name="can-i-enable-autoscale-on-an-existing-container-or-a-database"></a>È possibile abilitare la scalabilità automatica in un contenitore o in un database esistente?
Attualmente, è possibile abilitare la scalabilità automatica sui nuovi contenitori e database durante la creazione. Il supporto per abilitare la modalità di scalabilità automatica nei contenitori e nei database esistenti non è ancora disponibile. È possibile eseguire la migrazione di contenitori esistenti in un nuovo contenitore usando [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) o un [feed di modifiche](change-feed.md). 

### <a name="can-i-turn-off-autoscale-mode-on-a-container-or-database"></a>È possibile disattivare la modalità di scalabilità automatica in un contenitore o in un database?
Sì, è possibile disattivare la scalabilità automatica passando all'opzione "Manual" per la velocità effettiva con provisioning. Nella versione corrente, dopo il passare dalla modalità di scalabilità automatica alla modalità manuale, non è possibile abilitare di nuovo la scalabilità automatica per la stessa risorsa. 

### <a name="is-autoscale-mode-supported-for-shared-throughput-databases"></a>La modalità di scalabilità automatica è supportata per i database con velocità effettiva condivisa?
Sì, la modalità di scalabilità automatica è supportata per i database con velocità effettiva condivisa. Per abilitare questa funzionalità, selezionare la modalità di scalabilità automatica e l'opzione di **provisioning della velocità effettiva** durante la creazione del database. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autoscale-is-enabled"></a>Qual è il numero di raccolte consentite per ogni database di velocità effettiva condivisa quando è abilitata la scalabilità automatica?
Per i database con velocità effettiva condivisa con la modalità di ridimensionamento automatico abilitata, il numero di raccolte consentite è: MIN (25, numero massimo di Ur/s del database/1000). Se, ad esempio, la velocità effettiva massima del database è 20.000 UR/sec, il database può contenere MIN (25, 20.000 UR/s/1000) = 20 raccolte. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Qual è il limite di archiviazione associato a ogni opzione max ur/s?  
Il limite di archiviazione in GB per ogni UR/s massimo è: numero massimo di ur/sec del database o del contenitore/100. Se ad esempio il numero massimo di Ur/s è 20.000 UR/sec, la risorsa può supportare 200 GB di spazio di archiviazione. Vedere l'articolo [limiti di scalabilità](provision-throughput-autoscale.md#autoscale-limits) automatica per le UR e le opzioni di archiviazione massime disponibili. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Cosa accade se si supera il limite di archiviazione associato alla velocità effettiva massima?
Se viene superato il limite di archiviazione associato alla velocità effettiva massima del database o del contenitore, Azure Cosmos DB aumenterà automaticamente la velocità effettiva massima al livello più alto successivo in grado di supportare tale livello di archiviazione. Si supponga, ad esempio, che venga eseguito il provisioning di un database o di un contenitore con l'opzione 4000 ur/s max throughput, che ha un limite di archiviazione di 50 GB. Se l'archiviazione della risorsa aumenta fino a 100 GB, il numero massimo di ur/sec del database o del contenitore verrà aumentato automaticamente a 20.000 UR/s, che può supportare fino a 200 GB. 

### <a name="how-quickly-will-autoscale-up-and-down-based-on-spikes-in-traffic"></a>Con quale rapidità la scalabilità automatica si basa su picchi di traffico?
In modalità di scalabilità automatica è possibile aumentare o ridurre istantaneamente le UR/sec nell'intervallo minimo e massimo di Ur/s, in base al traffico in ingresso. La fatturazione viene eseguita a una granularità di 1 ora, in cui vengono addebitate le UR/sec più alte in un'ora specifica.

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autoscale-mode"></a>È possibile specificare un valore di velocità effettiva massima personalizzata (UR/sec) per la modalità di scalabilità automatica?
Attualmente, è possibile scegliere tra [quattro opzioni](provision-throughput-autoscale.md#autoscale-limits) per la velocità effettiva massima (UR/sec).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>È possibile aumentare il numero massimo di Ur/s (passare a un livello superiore) nel database o nel contenitore? 
Sì. Dall'opzione **ridimensiona & impostazioni** per il contenitore o l'opzione di **ridimensionamento** per il database, è possibile selezionare un numero massimo di Ur/s superiore per la modalità di scalabilità automatica. Si tratta di un'operazione di scalabilità verticale asincrona che può richiedere qualche minuto (in genere 4-6 ore, a seconda delle UR/sec selezionate), perché il servizio effettua il provisioning di più risorse per supportare la scalabilità più elevata. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>È possibile ridurre il numero massimo di Ur/s (passare a un livello inferiore) nel database o nel contenitore?
Sì. Fino a quando l'archiviazione corrente del database o del contenitore è inferiore al [limite di archiviazione](#what-is-the-storage-limit-associated-with-each-max-rus-option) associato al livello massimo di Ur/s a cui si vuole applicare la riduzione, è possibile ridurre il numero massimo di ur/sec a tale livello. Se, ad esempio, sono state selezionate 20.000 UR/s come numero massimo di ur/sec, è possibile ridurre il numero massimo di Ur/s a 4000 ur/s se sono presenti meno di 50 GB di spazio di archiviazione (limite di archiviazione associato a 4000 ur/sec).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Qual è il mapping tra le UR/s e le partizioni fisiche massime?
Quando si seleziona per la prima volta il numero massimo di Ur/s, Azure Cosmos DB effettuerà il provisioning: numero massimo di Ur/s/10.000 UR/sec = numero di partizioni fisiche. Ogni [partizione fisica](partition-data.md#physical-partitions) può supportare fino a 10.000 UR/sec e 50 GB di spazio di archiviazione. Man mano che le dimensioni di archiviazione aumentano, Azure Cosmos DB suddividerà automaticamente le partizioni per aggiungere più partizioni fisiche per gestire l'aumento dell'archiviazione oppure aumenterà il livello massimo di Ur/s se lo spazio di archiviazione [supera il limite associato](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Il numero massimo di ur/sec del database o del contenitore è diviso uniformemente tra tutte le partizioni fisiche. Quindi, la velocità effettiva totale a cui è possibile applicare una singola partizione fisica è: numero massimo di ur/sec delle partizioni fisiche del database o del contenitore/#. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Cosa accade se le richieste in ingresso superano il numero massimo di ur/sec del database o del contenitore?
Se le UR/sec utilizzate complessivamente superano il numero massimo di ur/sec del contenitore o del database, le richieste che superano il numero massimo di ur/sec saranno limitate e restituiranno un codice di stato 429. Verranno limitate anche le richieste che comportano un utilizzo normalizzato superiore al 100%. L'utilizzo normalizzato viene definito come il massimo dell'utilizzo di Ur/s tra tutte le partizioni fisiche. Si supponga, ad esempio, che la velocità effettiva massima sia di 20.000 UR/sec e che siano presenti due partizioni fisiche, P_1 e P_2, ciascuna in grado di scalare fino a 10.000 UR/sec. In un determinato secondo, se P_1 ha usato 6000 ur e P_2 8000 ur, l'utilizzo normalizzato è massimo (6000 UR/10.000 UR, 8000 UR/10.000 UR) = 0,8.

> [!NOTE]
> I Azure Cosmos DB gli SDK client e gli strumenti di importazione dei dati (Azure Data Factory, bulk Executor Library) riprovare automaticamente a eseguire 429s, quindi 429s occasionali. Un numero elevato elevato di 429s può indicare che è necessario aumentare il numero massimo di ur/sec o rivedere la strategia di partizionamento per una [partizione a caldo](#autoscale-rate-limiting).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a>Quando è abilitata la scalabilità automatica, è ancora possibile vedere 429s (limitazione della velocità). 
Sì. È possibile vedere 429s in due scenari. In primo luogo, quando le UR/sec consumate supera il numero massimo di ur/sec del contenitore o del database, il servizio limiterà le richieste di conseguenza. 

In secondo luogo, se è presente una partizione critica, ovvero un valore di chiave di partizione logica con una quantità di richieste più elevata rispetto ad altri valori di chiave di partizione, è possibile che la partizione fisica sottostante superi il proprio budget di Ur/s. Come procedura consigliata, per evitare le partizioni a caldo, [scegliere una chiave di partizione](partitioning-overview.md#choose-partitionkey) appropriata che comportano una distribuzione uniforme di archiviazione e velocità effettiva. 

Ad esempio, se si seleziona l'opzione 20.000 UR/s max throughput e si hanno 200 GB di spazio di archiviazione, con quattro partizioni fisiche, ogni partizione fisica può essere ridimensionata automaticamente fino a 5000 ur/sec. Se è presente una partizione sensibile in una determinata chiave di partizione logica, verrà visualizzato 429s quando la partizione fisica sottostante in cui si trova è superiore a 5000 ur/sec, ovvero supera il 100% di utilizzo normalizzato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [abilitare la scalabilità automatica in un contenitore o un database di Azure Cosmos](provision-throughput-autoscale.md#create-db-container-autoscale).
* Informazioni sui [vantaggi della velocità effettiva con provisioning in modalità di scalabilità](provision-throughput-autoscale.md#autoscale-benefits)automatica.
* Altre informazioni sulle [partizioni logiche e fisiche](partition-data.md).
