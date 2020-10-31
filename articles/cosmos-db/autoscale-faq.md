---
title: Domande frequenti sulla velocità effettiva con provisioning a scalabilità automatica in Azure Cosmos DB
description: Domande frequenti sulla velocità effettiva con provisioning a scalabilità automatica per i database e i contenitori Azure Cosmos DB
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/10/2020
ms.openlocfilehash: 58e7d54750da86b8a700a4f2195bc4cfa012ae4b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092688"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Domande frequenti sulla velocità effettiva con provisioning a scalabilità automatica in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Con la velocità effettiva con provisioning a scalabilità automatica, Azure Cosmos DB gestisce e ridimensiona automaticamente le UR/s del database o del contenitore in base all'utilizzo. Questo articolo presenta le risposte ad alcune domande comuni sulla scalabilità automatica.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>Qual è la differenza tra "Autopilot" e "scalabilità automatica" in Azure Cosmos DB?
"Scalabilità automatica" o "velocità effettiva con provisioning a scalabilità automatica" è il nome aggiornato della funzionalità, nota in precedenza come "Autopilot". Con la versione corrente della scalabilità automatica sono state aggiunte nuove funzionalità, tra cui la possibilità di impostare un numero massimo di UR/s personalizzato e il supporto a livello di codice. 

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>Cosa accade ai database e ai contenitori creati nel modello di livello Autopilot precedente?
Le risorse create con il modello di livello precedente sono supportate automaticamente con il nuovo modello di UR/s personalizzato con scalabilità automatica. Il limite superiore del livello diventa il nuovo numero massimo di UR/s, che ha come risultato lo stesso intervallo di scalabilità. 

Se, ad esempio, in precedenza era stato selezionato il livello che è stato ridimensionato tra 400 e 4000 UR/s, il database o il contenitore viene ora visualizzato con un numero massimo di 4000 UR/s, che può essere ridimensionato tra 400 e 4000 UR/s. Da qui è possibile modificare il numero massimo di UR/s con un valore personalizzato, in modo da adattarlo al carico di lavoro. 

### <a name="how-quickly-will-autoscale-scale-up-based-on-spikes-in-traffic"></a>Con quale velocità il ridimensionamento automatico viene scalato in base ai picchi di traffico?
Con la scalabilità automatica, il sistema ridimensiona la velocità effettiva (UR/sec) `T` verso l'alto o verso il basso entro un intervallo compreso tra `0.1 * Tmax` e `Tmax`, in base al traffico in ingresso. Poiché il ridimensionamento è automatico e istantaneo, in qualsiasi momento è possibile utilizzare fino al valore `Tmax` di cui è stato effettuato il provisioning senza alcun ritardo. 

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>Come si determina il numero di UR/s a cui è attualmente dimensionato il sistema?
Usare [metriche di Monitoraggio di Azure](how-to-choose-offer.md#measure-and-monitor-your-usage) per monitorare sia il numero massimo di UR/s di scalabilità automatica di cui è stato effettuato il provisioning che la velocità effettiva corrente (UR/s) a cui il sistema è dimensionato. 

### <a name="what-is-the-pricing-for-autoscale"></a>Quali prezzi vengono applicati per la scalabilità automatica?
Ogni ora viene addebitata la velocità effettiva più elevata `T` a cui il sistema è stato ridimensionato nel corso dell'ora. Se la risorsa non ha ricevuto richieste nel corso di tale ora o se non è stata ridimensionata oltre `0.1 * Tmax`, viene addebitato il minimo di `0.1 * Tmax`. Per informazioni dettagliate, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/). 

### <a name="how-does-autoscale-show-up-on-my-bill"></a>Come viene visualizzata la funzionalità di scalabilità automatica in fattura?
Negli account con singola area di scrittura, la velocità di scalabilità automatica per 100 ur/sec è pari a 1,5 x la velocità della velocità effettiva con provisioning standard (manuale). Nella fattura è visualizzato il contatore della velocità effettiva con provisioning standard esistente. La quantità di questo contatore viene moltiplicata per 1,5. Se, ad esempio, il numero massimo di UR/s del sistema è stato ridimensionato a 6000 UR/s nel corso di un'ora, per tale ora vengono addebitate 60 * 1,5 = 90 unità del contatore.

Negli account con più aree di scrittura, la velocità di scalabilità automatica per 100 ur/sec corrisponde alla frequenza per la velocità effettiva del provisioning di più aree di scrittura standard (manuale). Nella fattura sarà visualizzato il contatore più aree di scrittura esistenti. Poiché le tariffe sono le stesse, se si usa la scalabilità automatica viene visualizzata la stessa quantità rispetto alla velocità effettiva standard.

### <a name="does-autoscale-work-with-reserved-capacity"></a>La scalabilità automatica funziona con la capacità riservata?
Sì. Quando si acquista la capacità riservata per gli account con più aree di scrittura, lo sconto per la prenotazione per le risorse di scalabilità automatica viene applicato all'utilizzo del contatore a un rapporto di 1,5 * il [rapporto dell'area specifica](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region). 

La capacità riservata dell'area con più scritture funziona allo stesso tempo per la scalabilità automatica e la velocità effettiva con provisioning standard (manuale). Vedere [Capacità riservata di Azure Cosmos DB](cosmos-db-reserved-capacity.md)

### <a name="does-autoscale-work-with-free-tier"></a>La scalabilità automatica funziona con il livello gratuito?
Sì. Nel livello gratuito è possibile usare la velocità effettiva con scalabilità automatica in un contenitore. Il supporto per i database con velocità effettiva condivisa con scalabilità automatica con numero massimo di UR/s personalizzato non è ancora disponibile. Vedere come [funziona la fatturazione del livello gratuito con la scalabilità automatica](understand-your-bill.md#billing-examples-with-free-tier-accounts).

### <a name="is-autoscale-supported-for-all-apis"></a>La scalabilità automatica è supportata per tutte le API?
Sì, la scalabilità automatica è supportata per tutte le API: Core (SQL), Gremlin, Tabella, Cassandra e API per MongoDB.

### <a name="is-autoscale-supported-for-multi-region-write-accounts"></a>La funzionalità di scalabilità automatica è supportata per gli account di scrittura in più aree?
Sì. Le UR/s massime sono disponibili in ogni area aggiunta all'account Azure Cosmos DB. 

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>Come si può abilitare la scalabilità automatica nei nuovi database o nei nuovi contenitori?
Vedere questo articolo su [come abilitare la scalabilità automatica](how-to-provision-autoscale-throughput.md).

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>È possibile abilitare la scalabilità automatica in un database o in un contenitore esistente?
Sì. È anche possibile passare da velocità effettiva con scalabilità automatica a velocità effettiva con provisioning standard (manuale) e viceversa, in base alle esigenze. Per tutte le API, per eseguire queste operazioni è attualmente possibile usare solo il [portale di Azure](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container).

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>Come funziona la migrazione tra velocità effettiva con scalabilità automatica e velocità effettiva con provisioning standard (manuale)?
Dal punto di vista concettuale, il cambiamento del tipo di velocità effettiva è un processo in due fasi. Prima si invia la richiesta di modifica delle impostazioni della velocità effettiva per l'uso della scalabilità automatica o del provisioning manuale. In entrambi i casi, il sistema determina e imposta automaticamente un valore UR/s iniziale, in base alle impostazioni della velocità effettiva e alla risorsa di archiviazione correnti. Durante questo passaggio, non viene accettato alcun valore UR/s indicato dall'utente. Dopo che l'aggiornamento sarà stato completato, sarà possibile [modificare il numero di UR/s](#can-i-change-the-max-rus-on-the-database-or-container) per adattarlo al carico di lavoro. 

**Migrazione da velocità effettiva con provisioning standard (manuale) a velocità effettiva con scalabilità automatica**

Per un contenitore, usare la formula seguente per stimare il numero massimo di UR/s per la scalabilità automatica iniziale: ``MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)``, arrotondato al migliaio di UR/s più vicino. Il numero massimo di UR/s effettivo iniziale per la scalabilità automatica può variare a seconda della configurazione dell'account.

Esempio 1: Si supponga di avere un contenitore con una velocità effettiva con provisioning manuale di 10.000 UR/s e 25 GB di spazio di archiviazione. Quando si abilita la scalabilità automatica, il numero massimo di UR/s iniziale della scalabilità automatica corrisponderà a 10.000 UR/s, ridimensionabili tra 1000 e 10.000 UR/s. 

Esempio 2: Si supponga di avere un contenitore con una velocità effettiva con provisioning manuale di 50.000 UR/s e 2500 GB di spazio di archiviazione. Quando si abilita la scalabilità automatica, il numero massimo di UR/s iniziale della scalabilità automatica corrisponderà a 250.000 UR/s, ridimensionabili tra 25.000 e 250.000 UR/s. 

**Migrazione da velocità effettiva con scalabilità automatica a velocità effettiva con provisioning standard (manuale)**

La velocità effettiva con provisioning manuale iniziale è pari al numero massimo di UR/s della scalabilità automatica corrente. 

Esempio: Si supponga di avere un database o un contenitore con scalabilità automatica con un numero massimo di UR/s pari a 20.000 (con scalabilità compresa tra 2000 e 20.000 UR/sec). Quando si esegue l'aggiornamento per l'uso della velocità effettiva con provisioning manuale, la velocità effettiva iniziale sarà di 20.000 UR/s. 

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>L'interfaccia della riga di comando di Azure e PowerShell supportano la gestione di database o contenitori con scalabilità automatica?
È attualmente possibile creare e gestire risorse con scalabilità automatica con il portale di Azure, .NET V3 SDK, Java V4 SDK e Azure Resource Manager. Nell'interfaccia della riga di comando di Azure, in PowerShell e negli altri SDK il supporto non è ancora disponibile.

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>La scalabilità automatica è supportata per i database con velocità effettiva condivisa?
Sì, la scalabilità automatica è supportata per i database con velocità effettiva condivisa. Per abilitare questa funzionalità, selezionare la scalabilità automatica e l'opzione **Provisioning velocità effettiva** durante la creazione del database. 

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>Qual è il numero di contenitori consentito per database con velocità effettiva condivisa quando è abilitata la scalabilità automatica?
Azure Cosmos DB impone un massimo di 25 contenitori per un database con velocità effettiva condivisa. Questo valore si applica ai database con velocità effettiva con scalabilità automatica o con provisioning standard (manuale). 

### <a name="what-is-the-impact-of-autoscale-on-database-consistency-level"></a>Qual è l'effetto della scalabilità automatica sul livello di coerenza del database?
Non vi è alcun effetto sulla scalabilità automatica sul livello di coerenza del database.
Per ulteriori informazioni sui livelli di coerenza disponibili, vedere l'articolo relativo ai [livelli di coerenza](consistency-levels.md) .

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Qual è il limite di archiviazione associato a ciascun valore massimo di UR/s?  
Il limite di archiviazione in GB per ogni numero massimo di UR/s corrisponde al numero massimo di UR/s del database o del contenitore / 100. Se ad esempio il numero massimo di UR/s è 20.000, la risorsa può supportare 200 GB di spazio di archiviazione. Vedere l'articolo sui [limiti della scalabilità automatica](provision-throughput-autoscale.md#autoscale-limits) per le opzioni disponibili per il numero massimo di UR/s e lo spazio di archiviazione corrispondente. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Cosa accade se si supera il limite di archiviazione associato alla velocità effettiva massima?
Se il limite di archiviazione associato alla velocità effettiva massima del database o del contenitore viene superato, Azure Cosmos DB aumenta automaticamente la velocità effettiva massima al numero di UR/s più alto successivo che può essere supportato dal livello di archiviazione corrente.

Se ad esempio si inizia con un numero massimo di UR/s pari a 50.000 (con scalabilità compresa tra 5000 e 50.000 UR/s), è possibile archiviare fino a 500 GB di dati. Se si superano i 500 GB, se ad esempio l'archiviazione è ora pari a 600 GB, il nuovo numero massimo di UR/s è di 60.000 UR/s (con scalabilità compresa tra 6000 e 60.000 UR/s).

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>È possibile modificare il numero massimo di UR/s nel database o nel contenitore? 
Sì. Vedere questo [articolo](how-to-provision-autoscale-throughput.md) su come eseguire la modifica del numero massimo di UR/s. La modifica del numero massimo di UR/s, a seconda del valore richiesto, può consistere in un'operazione asincrona il cui completamento può richiedere tempo (fino a 4-6 ore, a seconda delle UR/s selezionate)

#### <a name="increasing-the-max-rus"></a>Aumento del numero massimo di UR/s
Quando si invia una richiesta di aumento del numero massimo di UR/s `Tmax`, a seconda del numero massimo di UR/s selezionato, il servizio effettua il provisioning di una quantità maggiore di risorse per supportare il numero massimo di UR/s più alto. Durante questa operazione, il carico di lavoro e le operazioni esistenti non vengono interessate. Il sistema continuerà a ridimensionare il database o il contenitore tra il `0.1*Tmax` precedente e il `Tmax` fino a quando non sarà pronto il nuovo intervallo di scalabilità da `0.1*Tmax_new` a `Tmax_new`.

#### <a name="lowering-the-max-rus"></a>Riduzione del numero massimo di UR/s
Quando si riduce il numero massimo di UR/s, il valore minimo che è possibile impostare è `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)`, arrotondato al migliaio di UR/s più vicino. 

Esempio 1: Si supponga di avere un contenitore con scalabilità automatica con un numero massimo di UR/s pari a 20.000 (con scalabilità compresa tra 2000 e 20.000 UR/s) e 50 GB di spazio di archiviazione. Il valore minimo più basso per il quale è possibile impostare il numero massimo di UR/s corrisponde a MAX(4000, 20.000/10, **50 * 100** ) = 5000 UR/s (con scalabilità compresa tra 500 e 5000 UR/s).

Esempio 2: Si supponga di avere un contenitore con scalabilità automatica con un numero massimo di UR/s pari a 100.000 e 100 GB di spazio di archiviazione. Si decide ora di ridimensionare il numero massimo di UR/s a 150.000 (con scalabilità compresa tra 15.000 e 150.000 UR/s). Il valore minimo più basso per il quale è ora possibile impostare il numero massimo di UR/s corrisponde a MAX(4000, **150.000/10** , 100 * 100) = 15,000 UR/s (con scalabilità compresa tra 1500 e 15,000 UR/s). 

Per un database con velocità effettiva condivisa, quando si riduce il numero massimo di UR/s, il valore minimo che è possibile impostare è `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`, arrotondato al migliaio di UR/s più vicino.  

Le formule e gli esempi precedenti sono correlati al numero massimo di UR/s con scalabilità automatica più basso che è possibile impostare, distinto dall'intervallo tra `0.1 * Tmax` e `Tmax` di ridimensionamento automatico eseguito dal sistema. Indipendentemente dal numero massimo di UR/s, il sistema esegue sempre il ridimensionamento tra `0.1 * Tmax` e `Tmax`. 

### <a name="how-does-ttl-work-with-autoscale"></a>Come funziona TTL con la scalabilità automatica?
Con la scalabilità automatica, le operazioni TTL non influiscono sul ridimensionamento delle UR/s. Tutte le UR utilizzate per operazioni TTL non rientrano tra le UR/s fatturate per il contenitore con scalabilità automatica. 

Si supponga, ad esempio, di avere un contenitore con scalabilità automatica con 400 - 4000 UR/s:
- Ora 1: T=0: Il contenitore non ha utilizzo (nessuna richiesta TTL o di carico di lavoro). Le UR/s fatturabili sono 400.
- Ora 1: T=1: TTL abilitato.
- Ora 1: T=2: Il contenitore inizia a ricevere richieste, che utilizzano 1000 UR in 1 secondo. È anche presente l'equivalente di 200 UR di operazioni TTL che devono essere eseguite. Le UR/s fatturabili sono comunque 1000. Indipendentemente da quando vengono eseguite, le operazioni TTL non influiscono sulla logica di ridimensionamento della scalabilità automatica.

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Qual è il mapping tra il numero massimo di UR/s e le partizioni fisiche?
Quando si seleziona per la prima volta il numero massimo di UR/s, Azure Cosmos DB effettua il provisioning del numero massimo di UR/s / 10.000 UR/s = numero di partizioni fisiche. Ogni [partizione fisica](partitioning-overview.md#physical-partitions) può supportare fino a 10.000 UR/s e 50 GB di spazio di archiviazione. Man mano che le dimensioni dello spazio di archiviazione aumentano, Azure Cosmos DB suddivide automaticamente le partizioni per aggiungere partizioni fisiche e gestire l'aumento dello spazio di archiviazione, oppure aumenta il numero massimo di UR/s se lo spazio di archiviazione [supera il limite associato](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Il numero massimo di UR/s del database o del contenitore viene diviso uniformemente tra tutte le partizioni fisiche. Quindi la velocità effettiva totale a cui una singola partizione fisica può essere ridimensionata corrisponde al numero massimo di UR/s del database o del contenitore / numero delle partizioni fisiche. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Cosa accade se le richieste in ingresso superano il numero massimo di UR/s del database o del contenitore?
Se il numero di UR/s utilizzate complessivamente supera il numero massimo di UR/s del database o del contenitore, le richieste che superano il numero massimo di UR/s vengono limitate e restituiscono il codice di stato 429. Vengono limitate anche le richieste che comportano un utilizzo normalizzato superiore al 100%. L'utilizzo normalizzato è definito come l'utilizzo massimo delle UR/s tra tutte le partizioni fisiche. Si supponga, ad esempio, che la velocità effettiva massima sia di 20.000 UR/s e che siano presenti due partizioni fisiche, P_1 e P_2, ognuna in grado di ridimensionarsi fino a 10.000 UR/s. In un secondo specifico, se P_1 ha usato 6000 UR e P_2 8000 UR, l'utilizzo normalizzato corrisponde a MAX(6000 UR / 10.000 UR, 8000 UR / 10.000 UR) = 0,8.

> [!NOTE]
> Gli SDK client di Azure Cosmos DB e gli strumenti di importazione dei dati (Azure Data Factory, libreria di esecuzione bulk) riprovano automaticamente in caso di codice di stato 429, quindi la comparsa occasionale di tale codice non è un problema. La frequenza elevata del codice 429 può indicare che è necessario aumentare il numero massimo di UR/s o rivedere la strategia di partizionamento per una [partizione con accesso frequente](#autoscale-rate-limiting).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> Se la scalabilità automatica è abilitata, è comunque possibile vedere codici di stato 429 (limitazione delle richieste/della frequenza)? 
Sì. È possibile vedere codici 429 in due scenari. Nel primo scenario, se le UR/s utilizzate superano il numero massimo di UR/s del database o del contenitore, il servizio limita le richieste di conseguenza. 

Nel secondo scenario, nel caso di una partizione con accesso frequente, se un valore di chiave di partizione logica ha una quantità di richieste sproporzionatamente più elevata rispetto ad altri valori di chiave di partizione, è possibile che la partizione fisica sottostante superi il budget di UR/s. Come procedura consigliata, per evitare partizioni con accesso frequente, [scegliere una chiave di partizione adeguata](partitioning-overview.md#choose-partitionkey) che comporti una distribuzione uniforme sia dello spazio di archiviazione che della velocità effettiva. 

Se ad esempio si seleziona l'opzione di velocità effettiva massima di 20.000 UR/s e si hanno 200 GB di spazio di archiviazione, con quattro partizioni fisiche, ogni partizione fisica può essere ridimensionata automaticamente fino a 5000 UR/s. Se in una chiave di partizione logica è presente una partizione con accesso frequente e la partizione fisica sottostante supera 5000 UR/s, ovvero supera il 100% di utilizzo normalizzato, vengono visualizzati codici di stato 429.


## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [abilitare la scalabilità automatica in un database o in un contenitore di Azure Cosmos DB](how-to-provision-autoscale-throughput.md).
* Informazioni sui [vantaggi della velocità effettiva con provisioning con scalabilità automatica](provision-throughput-autoscale.md#benefits-of-autoscale).
* Altre informazioni sulle [partizioni logiche e fisiche](partitioning-overview.md).
                        
