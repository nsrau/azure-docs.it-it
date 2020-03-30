---
title: Domande frequenti sulla velocità effettiva in modalità pilota automatico di Azure Cosmos DBFrequently asked questions about throughput in Azure Cosmos DB autopilot mode
description: Domande frequenti sulla modalità di pilota automatico per i database e i contenitori di Azure CosmosFrequently asked questions about autopilot mode for Azure Cosmos DB databases and containers
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483310"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>Domande frequenti sulla velocità effettiva con provisioning in modalità pilota automatico database di Azure (anteprima)Frequently asked questions about provisioned throughput in Azure Cosmos DB autopilot mode (Preview)
Con la modalità pilota automatico, Azure Cosmos DB gestirà e ridimensiona automaticamente le RU/s del contenitore o del database in base all'utilizzo. Questo articolo risponde alle domande più frequenti sulla modalità pilota automatico. 

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="is-autopilot-mode-supported-for-all-apis"></a>La modalità pilota automatico è supportata per tutte le API?
Sì, la modalità di pilota automatico è supportata per tutte le API: Core (SQL), Gremlin, Table, Cassandra e API per MongoDB.

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>La modalità pilota automatico è supportata per gli account multimaster?
Sì, la modalità pilota automatico è supportata per gli account multimaster. Le RU/s massime sono disponibili in ogni regione aggiunta all'account Cosmos. 

### <a name="what-is-the-pricing-for-autopilot"></a>Qual è il prezzo per il pilota automatico?
Per informazioni dettagliate, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) di Azure Cosmos DB. 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>Come si abilita il pilota automatico per i contenitori o i database?
La modalità pilota automatico può essere abilitata in nuovi contenitori e database creati tramite il portale di Azure.Autopilot mode can be enabled on new containers and databases created using the Azure portal. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>È disponibile il supporto dell'interfaccia della riga di comando o dell'SDK per creare contenitori o database con modalità pilota automatico?
Attualmente, nella versione di anteprima, è possibile creare risorse solo con la modalità pilota automatico dal portale di Azure.Currently, in the preview release, you can only create resources with autopilot mode from the Azure portal. Il supporto per CLI e SDK non è ancora disponibile.

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>È possibile abilitare il pilota automatico su un contenitore esistente o un database?
Attualmente, è possibile abilitare il pilota automatico su nuovi contenitori e database durante la creazione. Il supporto per abilitare la modalità pilota automatico nei contenitori e nei database esistenti non è ancora disponibile. È possibile eseguire la migrazione di contenitori esistenti in un nuovo contenitore usando [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) o il feed di [modifiche.](change-feed.md) 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>È possibile disattivare la modalità di pilota automatico in un contenitore o in un database?
Sì, è possibile disattivare il pilota automatico passando all'opzione 'Manuale' per la velocità effettiva di cui è stato eseguito il provisioning. Nella versione di anteprima, dopo il passaggio dalla modalità pilota automatico alla modalità manuale, non è possibile abilitare nuovamente il pilota automatico per la stessa risorsa. 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>La modalità autopilota è supportata per i database della velocità effettiva condivisa?
Sì, la modalità pilota automatico è supportata per i database della velocità effettiva condivisa. Per abilitare questa funzionalità, selezionare la modalità di pilota automatico e l'opzione Effettua il **provisioning della velocità effettiva** durante la creazione del database. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>Qual è il numero di raccolte consentite per database di velocità effettiva condivisa quando il pilota automatico è abilitato?
Per i database di velocità effettiva condivisa con la modalità pilota automatico abilitata, il numero di raccolte consentite è: MIN(25, Max RU/s del database / 1000). Ad esempio, se la velocità effettiva massima del database è 20.000 RU/s, il database può avere MIN(25, 20,000 RU/s / 1000) - 20 raccolte. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Qual è il limite di archiviazione associato a ogni opzione max RU/s?  
Il limite di archiviazione in GB per ogni NUMERO massimo di RU/s è: Max RU/s del database o contenitore / 100. Ad esempio, se la ru/s massima è 20.000 RU/s, la risorsa può supportare 200 GB di spazio di archiviazione. Vedere l'articolo dei limiti del [pilota automatico](provision-throughput-autopilot.md#autopilot-limits) per le opzioni di archiviazione e RU/s massime disponibili. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Cosa succede se si supera il limite di archiviazione associato alla velocità effettiva massima?
Se viene superato il limite di archiviazione associato alla velocità effettiva massima del database o del contenitore, Azure Cosmos DB aumenterà automaticamente la velocità effettiva massima al livello più alto successivo in grado di supportare tale livello di archiviazione. Si supponga, ad esempio, che venga eseguito il provisioning di un database o di un contenitore con l'opzione di velocità effettiva massima di 4000 RU/s, con un limite di archiviazione di 50 GB. Se l'archiviazione della risorsa aumenta a 100 GB, le RU/s massime del database o del contenitore verranno automaticamente aumentate a 20.000 RU/s, che possono supportare fino a 200 GB. 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>Quanto velocemente il pilota automatico aumenterà e riduce in base ai picchi di traffico?
Il pilota automatico scala istantaneamente verso l'alto o verso il basso i RU/s entro la gamma minima e massima di RU/s, in base al traffico in entrata. La fatturazione viene eseguita con una granularità di 1 ora, in cui ti viene addebitato l'elenco DI RU o s più alto in una determinata ora. 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>È possibile specificare un valore di velocità effettiva massima (RU/s) personalizzato per la modalità pilota automatico?
Attualmente, durante la versione di anteprima, è possibile scegliere tra [quattro opzioni](provision-throughput-autopilot.md#autopilot-limits) per la velocità effettiva massima (RU/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>È possibile aumentare il numero massimo di RU/s (spostamento a un livello superiore) nel database o nel contenitore? 
Sì. Dall'opzione **Ridimensiona & Impostazioni** per il contenitore o dall'opzione **Scala** per il database, è possibile selezionare un numero/i di ru/i massimo superiore per il pilota automatico. Si tratta di un'operazione di scalabilità verticale asincrona che potrebbe richiedere alcune ore di completamento (in genere 4-6 ore, a seconda delle RU/s selezionate) poiché il servizio esegue il provisioning di più risorse per supportare la scala superiore. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>È possibile ridurre il numero massimo di RU/s (spostamento a un livello inferiore) nel database o nel contenitore?
Sì. Se l'archiviazione corrente del database o del contenitore è inferiore al [limite di archiviazione](#what-is-the-storage-limit-associated-with-each-max-rus-option) associato al livello ru/s massimo a cui si vuole ridurre le dimensioni, è possibile ridurre il numero massimo di RU/s a tale livello. Ad esempio, se sono stati selezionati 20.000 RU/s come RU/s massimi, è possibile ridurre le RU/s massime a 4000 RU/s se si dispone di meno di 50 GB di spazio di archiviazione (il limite di archiviazione associato a 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Qual è il mapping tra le RU/s massime e le partizioni fisiche?
Quando si seleziona per la prima volta il numero massimo di RU/s, verrà eseguito il provisioning di Azure Cosmos DB: Max RU/s / 10.000 RU/s , ovvero il numero di partizioni fisiche. Ogni [partizione fisica](partition-data.md#physical-partitions) può supportare fino a 10.000 RU/s e 50 GB di spazio di archiviazione. Con l'aumentare delle dimensioni di archiviazione, Azure Cosmos DB dividerà automaticamente le partizioni per aggiungere altre partizioni fisiche per gestire l'aumento di archiviazione o aumenterà il livello RU/s massimo se l'archiviazione [supera il limite associato.](#what-is-the-storage-limit-associated-with-each-max-rus-option) 

Le RU massime del database o del contenitore vengono suddivise in modo uniforme tra tutte le partizioni fisiche. Pertanto, la velocità effettiva totale su cui è possibile eseguire la scalabilità di qualsiasi singola partizione fisica è: Max RU/s di database o contenitore / partizioni fisiche. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Cosa succede se le richieste in ingresso superano le RU/s massime del database o del contenitore?
Se le RU/s complessive utilizzate superano le RU/s massime del contenitore o del database, le richieste che superano le RU/s massime verranno limitate e restituiranno un codice di stato 429. Anche le richieste che comportano un utilizzo più rifinito al 100% verranno limitate. L'utilizzo normalizzato è definito come il massimo dell'utilizzo di RU/s in tutte le partizioni fisiche. Si supponga, ad esempio, che la velocità effettiva massima sia di 20.000 RU/s e che si disponga di due partizioni fisiche, P_1 e P_2, ognuna in grado di scalare a 10.000 RU/s. In un dato secondo, se P_1 ha utilizzato 6000 RU e P_2 8000 RU, l'utilizzo normalizzato è MAX(6000 RU / 10.000 RU, 8000 RU / 10.000 RU) - 0,8.

> [!NOTE]
> Gli SDK del client Azure Cosmos DB e gli strumenti di importazione dei dati (Azure Data Factory, libreria dell'esecutore bulk) riprovano automaticamente nei 429, pertanto i 429 occasionali vanno bene. Un numero elevato sostenuto di 429s può indicare che è necessario aumentare la versione massima delle RU o rivedere la strategia di partizionamento per una [partizione a caldo.](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled)

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>È ancora possibile vedere 429s (limitazione/limitazione della velocità) quando il pilota automatico è abilitato? 
Sì. È possibile visualizzare i 429 in due scenari. In primo luogo, quando le RU/s utilizzate complessive superano le RU/s massime del contenitore o del database, il servizio limiterà le richieste di conseguenza. 

In secondo luogo, se è presente una partizione a caldo, ovvero un valore di chiave di partizione logica con una quantità sproporzionatamente superiore di richieste rispetto ad altri valori di chiave di partizione, è possibile che la partizione fisica sottostante superi il budget RU/s. Come procedura consigliata, per evitare le partizioni a caldo, scegliere una chiave di [partizione valida](partitioning-overview.md#choose-partitionkey) che si traduca in una distribuzione uniforme dell'archiviazione e della velocità effettiva. 

Ad esempio, se si seleziona l'opzione di velocità effettiva massima di 20.000 RU/s e si dispone di 200 GB di spazio di archiviazione, con quattro partizioni fisiche, ogni partizione fisica può essere ridimensionata automaticamente fino a 5000 RU/s. Se su una particolare chiave di partizione logica si trovava una partizione a caldo, verranno visualizzati 429 s quando la partizione fisica sottostante in cui risiede supera 5000 RU/s, ovvero supera il 100% dell'utilizzo normalizzato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come abilitare il [pilota automatico in un database o un contenitore Cosmos](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode)di Azure.
* Scopri i [vantaggi del pilota automatico](provision-throughput-autopilot.md#benefits-of-autopilot-mode).
* Ulteriori informazioni sulle [partizioni logiche e fisiche](partition-data.md).
