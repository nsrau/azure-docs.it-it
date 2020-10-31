---
title: Partizionamento e scalabilità orizzontale in Azure Cosmos DB
description: Informazioni sul partizionamento, sulle partizioni logiche e fisiche in Azure Cosmos DB, sulle procedure consigliate per la scelta di una chiave di partizione e su come gestire le partizioni logiche
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: 7c05ca6462d49d1d41791e5b93b7723ac681d448
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080833"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partizionamento e scalabilità orizzontale in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB usa il partizionamento per ridimensionare i singoli contenitori di un database per soddisfare le esigenze di prestazioni dell'applicazione. Nel partizionamento gli elementi in un contenitore sono divisi in subset distinti denominati *partizioni logiche* . Le partizioni logiche vengono formate in base al valore di una *chiave di partizione* associata a ogni elemento in un contenitore. Tutti gli elementi in una partizione logica hanno lo stesso valore della chiave di partizione.

Un contenitore, ad esempio, include gli elementi. Ogni elemento ha un valore univoco per la `UserID` Proprietà. Se `UserID` funge da chiave di partizione per gli elementi nel contenitore e sono presenti 1.000 valori univoci `UserID` , vengono create 1.000 partizioni logiche per il contenitore.

Oltre a una chiave di partizione che determina la partizione logica dell'elemento, ogni elemento in un contenitore dispone di un *ID elemento* (univoco all'interno di una partizione logica). Combinando la chiave di partizione e l' *ID* dell'elemento viene creato l' *Indice* dell'elemento, che identifica in modo univoco l'elemento. La [scelta di una chiave di partizione](#choose-partitionkey) è una decisione importante che influirà sulle prestazioni dell'applicazione.

In questo articolo viene illustrata la relazione tra partizioni logiche e fisiche. Vengono inoltre illustrate le procedure consigliate per il partizionamento e viene fornita una visualizzazione approfondita del funzionamento del ridimensionamento orizzontale in Azure Cosmos DB. Non è necessario comprendere questi dettagli interni per selezionare la chiave di partizione, ma è stata analizzata in modo da ottenere chiarezza sul funzionamento di Azure Cosmos DB.

## <a name="logical-partitions"></a>Partizioni logiche

Una partizione logica è costituita da un set di elementi con la stessa chiave di partizione. Ad esempio, in un contenitore che contiene i dati relativi al cibo dietetico, tutti gli elementi contengono una `foodGroup` Proprietà. È possibile usare `foodGroup` come chiave di partizione per il contenitore. Gruppi di elementi con valori specifici per `foodGroup` , ad esempio `Beef Products` , `Baked Products` e `Sausages and Luncheon Meats` , formano partizioni logiche distinte. Non è necessario preoccuparsi di eliminare una partizione logica quando vengono eliminati i dati sottostanti.

Una partizione logica definisce anche l'ambito delle transazioni del database. È possibile aggiornare gli elementi all'interno di una partizione logica utilizzando una [transazione con isolamento dello snapshot](database-transactions-optimistic-concurrency.md). Quando vengono aggiunti nuovi elementi a un contenitore, le nuove partizioni logiche vengono create in modo trasparente dal sistema.

Non esiste alcun limite al numero di partizioni logiche nel contenitore. Ogni partizione logica può archiviare fino a 20 GB di dati. Le scelte valide per le chiavi di partizione hanno un'ampia gamma di valori possibili. Ad esempio, in un contenitore in cui tutti gli elementi contengono una `foodGroup` proprietà, i dati all'interno della `Beef Products` partizione logica possono aumentare fino a 20 GB. La [selezione di una chiave di partizione](#choose-partitionkey) con una vasta gamma di valori possibili garantisce che il contenitore possa essere ridimensionato.

## <a name="physical-partitions"></a>Partizioni fisiche

Un contenitore viene ridimensionato distribuendo i dati e la velocità effettiva tra le partizioni fisiche. Internamente, viene eseguito il mapping di una o più partizioni logiche a una singola partizione fisica. In genere i contenitori più piccoli hanno molte partizioni logiche, ma richiedono solo una singola partizione fisica. Diversamente dalle partizioni logiche, le partizioni fisiche sono un'implementazione interna del sistema e sono completamente gestite da Azure Cosmos DB.

Il numero di partizioni fisiche nel contenitore dipende dalla configurazione seguente:

* Il numero di velocità effettiva di cui è stato effettuato il provisioning (ogni singola partizione fisica può fornire una velocità effettiva massima di 10.000 unità richiesta al secondo).
* Archiviazione dati totale (ogni singola partizione fisica può archiviare fino a 50 GB di dati).

Non esiste alcun limite al numero totale di partizioni fisiche nel contenitore. Man mano che la velocità effettiva con provisioning o la dimensione dei dati aumenta, Azure Cosmos DB creerà automaticamente nuove partizioni fisiche suddividendo quelle esistenti. Le divisioni di partizioni fisiche non influiscano sulla disponibilità dell'applicazione. Dopo la suddivisione della partizione fisica, tutti i dati all'interno di una singola partizione logica verranno comunque archiviati nella stessa partizione fisica. Una suddivisione della partizione fisica crea semplicemente un nuovo mapping delle partizioni logiche alle partizioni fisiche.

La velocità effettiva con provisioning per un contenitore è divisa uniformemente tra le partizioni fisiche. Una progettazione di chiavi di partizione che non distribuisce le richieste in modo uniforme può comportare un numero eccessivo di richieste indirizzate a un piccolo subset di partizioni che diventano "Hot". Le partizioni a caldo causano un uso inefficiente della velocità effettiva con provisioning, che può comportare la limitazione della frequenza e costi più elevati.

È possibile visualizzare le partizioni fisiche del contenitore nella sezione **archiviazione** del pannello **metriche** del portale di Azure:

:::image type="content" source="./media/partitioning-overview/view-partitions-zoomed-out.png" alt-text="Visualizzazione del numero di partizioni fisiche" lightbox="./media/partitioning-overview/view-partitions-zoomed-in.png" ::: 

Nello screenshot precedente, un contenitore ha `/foodGroup` come chiave di partizione. Ognuna delle tre barre nel grafico rappresenta una partizione fisica. Nell'immagine l' **intervallo di chiavi di partizione** è identico a quello di una partizione fisica. La partizione fisica selezionata contiene tre partizioni logiche: `Beef Products` , `Vegetable and Vegetable Products` e `Soups, Sauces, and Gravies` .

Se si effettua il provisioning di una velocità effettiva di 18.000 unità richiesta al secondo (UR/sec), ciascuna delle tre partizioni fisiche può utilizzare 1/3 della velocità effettiva totale con provisioning. All'interno della partizione fisica selezionata, le chiavi `Beef Products` 6.000 di partizione logiche, `Vegetable and Vegetable Products` e `Soups, Sauces, and Gravies` possono, collettivamente, utilizzare le UR/sec con provisioning della partizione fisica. Poiché la velocità effettiva con provisioning è divisa uniformemente tra le partizioni fisiche del contenitore, è importante scegliere una chiave di partizione che distribuisca equamente l'utilizzo della velocità effettiva [scegliendo la chiave di partizione logica corretta](#choose-partitionkey). 

> [!NOTE]
> Se si sceglie una chiave di partizione che distribuisce in modo uniforme l'utilizzo della velocità effettiva tra partizioni logiche, si garantisce che l'utilizzo della velocità effettiva tra le partizioni fisiche sia bilanciato.

## <a name="managing-logical-partitions"></a>Gestione di partizioni logiche

Azure Cosmos DB in modo trasparente e automatico gestisce il posizionamento delle partizioni logiche nelle partizioni fisiche per soddisfare in modo efficiente le esigenze di scalabilità e prestazioni del contenitore. Poiché i requisiti di archiviazione e velocità effettiva di un'applicazione aumentano, Azure Cosmos DB sposta le partizioni logiche per suddividere automaticamente il carico su un numero maggiore di partizioni fisiche. È possibile ottenere altre informazioni sulle [partizioni fisiche](partitioning-overview.md#physical-partitions).

Azure Cosmos DB usa il partizionamento basato su hash per distribuire partizioni logiche tra partizioni fisiche. Azure Cosmos DB hashing del valore della chiave di partizione di un elemento. Il risultato con hash determina la partizione fisica. Quindi, Azure Cosmos DB alloca in modo uniforme lo spazio chiavi degli hash della chiave di partizione tra le partizioni fisiche.

Le transazioni (in stored procedure o trigger) sono consentite solo per gli elementi di una singola partizione logica.

Sono disponibili altre informazioni sul [modo in cui Azure Cosmos DB gestisce le partizioni](partitioning-overview.md). Non è necessario comprendere i dettagli interni per compilare o eseguire le applicazioni, ma è stato aggiunto qui per un lettore curioso.

## <a name="replica-sets"></a>Set di repliche

Ogni partizione fisica è costituita da un set di repliche, denominato anche [*set di repliche*](global-dist-under-the-hood.md). Ogni set di repliche ospita un'istanza del motore di database. Un set di repliche rende i dati archiviati all'interno della partizione fisica durevole, a disponibilità elevata e coerente. Ogni replica che costituisce la partizione fisica eredita la quota di archiviazione della partizione. Tutte le repliche di una partizione fisica supportano collettivamente la velocità effettiva allocata alla partizione fisica. Azure Cosmos DB gestisce automaticamente i set di repliche.

In genere i contenitori più piccoli richiedono solo una singola partizione fisica, ma avranno comunque almeno 4 repliche.

La figura seguente mostra come le partizioni logiche vengono mappate alle partizioni fisiche distribuite a livello globale:

:::image type="content" source="./media/partitioning-overview/logical-partitions.png" alt-text="Visualizzazione del numero di partizioni fisiche" border="false":::

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Scelta di una chiave di partizione

Una chiave di partizione ha due componenti: il **percorso della chiave di partizione** e il valore della chiave di **partizione** . Si consideri, ad esempio, un elemento {"userId": "Andrew", "lavora per": "Microsoft"} Se si sceglie "userId" come chiave di partizione, di seguito sono riportati i due componenti chiave di partizione:

* Percorso della chiave di partizione, ad esempio: "/userId". Il percorso della chiave di partizione accetta caratteri alfanumerici e di sottolineatura (_). È inoltre possibile utilizzare gli oggetti annidati utilizzando la notazione del percorso standard (/).

* Valore della chiave di partizione (ad esempio: "Andrew"). Il valore della chiave di partizione può essere di tipo stringa o numerico.

Per informazioni sui limiti relativi alla velocità effettiva, all'archiviazione e alla lunghezza della chiave di partizione, vedere l'articolo relativo alle [quote del servizio Azure Cosmos DB](concepts-limits.md) .

La selezione della chiave di partizione è una scelta di progettazione semplice, ma importante, in Azure Cosmos DB. Una volta selezionata la chiave di partizione, non è possibile modificarla sul posto. Se è necessario modificare la chiave di partizione, è necessario spostare i dati in un nuovo contenitore con la nuova chiave di partizione desiderata.

Per **tutti i** contenitori, la chiave di partizione deve:

* Essere una proprietà con un valore che non cambia. Se una proprietà è la chiave di partizione, non è possibile aggiornare il valore della proprietà.

* Avere una cardinalità elevata. In altre parole, la proprietà deve avere un'ampia gamma di valori possibili.

* Diffondere il consumo delle unità richiesta e l'archiviazione dei dati in modo uniforme in tutte le partizioni logiche. In questo modo si garantisce anche il consumo di ur e la distribuzione di archiviazione tra le partizioni fisiche.

Se sono necessarie [transazioni ACID](database-transactions-optimistic-concurrency.md#multi-item-transactions) con più elementi in Azure Cosmos DB, sarà necessario usare [stored procedure o trigger](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures). Tutte le stored procedure e i trigger basati su JavaScript hanno come ambito un'unica partizione logica.

## <a name="partition-keys-for-read-heavy-containers"></a>Chiavi di partizione per i contenitori con intensa attività di lettura

Per la maggior parte dei contenitori, i criteri indicati sopra sono tutti quelli da considerare quando si sceglie una chiave di partizione. Per i contenitori di grandi dimensioni, tuttavia, potrebbe essere necessario scegliere una chiave di partizione che viene visualizzata frequentemente come filtro nelle query. Le query possono essere [indirizzate in modo efficiente solo alle partizioni fisiche pertinenti](how-to-query-container.md#in-partition-query) includendo la chiave di partizione nel predicato del filtro.

Se la maggior parte delle richieste del carico di lavoro è costituita da query e la maggior parte delle query dispone di un filtro di uguaglianza sulla stessa proprietà, questa proprietà può essere una scelta ottimale per la chiave di partizione. Se, ad esempio, si esegue spesso una query che filtra in `UserID` , `UserID` la selezione come chiave di partizione ridurrebbe il numero di [query tra partizioni](how-to-query-container.md#avoiding-cross-partition-queries).

Tuttavia, se il contenitore è di dimensioni ridotte, è probabile che non si disponga di partizioni fisiche sufficienti per preoccuparsi dell'effetto sulle prestazioni delle query tra partizioni. La maggior parte dei contenitori di piccole dimensioni in Azure Cosmos DB richiede solo una o due partizioni fisiche.

Se il contenitore può raggiungere più di alcune partizioni fisiche, è necessario assicurarsi di selezionare una chiave di partizione che riduce al minimo le query tra partizioni. Il contenitore richiederà più di alcune partizioni fisiche quando si verifica una delle condizioni seguenti:

* Il provisioning del contenitore avrà più di 30.000 UR

* Nel contenitore vengono archiviati oltre 100 GB di dati

## <a name="using-item-id-as-the-partition-key"></a>Uso dell'ID elemento come chiave di partizione

Se il contenitore dispone di una proprietà con un'ampia gamma di valori possibili, probabilmente si tratta di una scelta ottimale per la chiave di partizione. Un esempio possibile di tale proprietà è l' *ID dell'elemento* . Per i contenitori di piccole dimensioni o con un elevato numero di operazioni di scrittura, l' *ID dell'elemento* è naturalmente un'ottima scelta per la chiave di partizione.

L' *ID elemento* della proprietà di sistema esiste in ogni elemento del contenitore. Potrebbero essere presenti altre proprietà che rappresentano un ID logico dell'elemento. In molti casi, si tratta anche di opzioni di chiave di partizione eccezionali per gli stessi motivi dell' *ID dell'elemento* .

L' *ID dell'elemento* è un'ottima scelta per le chiavi di partizione per i motivi seguenti:

* È disponibile un'ampia gamma di valori possibili, ovvero un *ID* univoco per ogni elemento.
* Poiché è presente un *ID di elemento* univoco per ogni elemento, l' *ID dell'elemento* ha un ottimo lavoro per bilanciare uniformemente il consumo di ur e l'archiviazione dei dati.
* È possibile eseguire facilmente letture di punti efficienti, poiché si conosce sempre la chiave di partizione di un elemento se si conosce l' *ID elemento* .

Di seguito sono riportati alcuni aspetti da considerare quando si seleziona l' *ID elemento* come chiave di partizione:

* Se l' *ID dell'elemento* è la chiave di partizione, diventerà un identificatore univoco nell'intero contenitore. Non sarà possibile avere elementi con un *ID elemento* duplicato.
* Se si dispone di un contenitore con elevato numero di operazioni di lettura con numerose [partizioni fisiche](partitioning-overview.md#physical-partitions), le query saranno più efficienti se hanno un filtro di uguaglianza con l' *ID dell'elemento* .
* Non è possibile eseguire stored procedure o trigger su più partizioni logiche.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [velocità effettiva con provisioning in Azure Cosmos DB](request-units.md).
* Informazioni sulla [distribuzione globale in Azure Cosmos DB](distribute-data-globally.md).
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
