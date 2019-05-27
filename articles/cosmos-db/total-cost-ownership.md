---
title: Costo totale di proprietà (TCO) con Azure Cosmos DB
description: Questo articolo confronta il costo totale di proprietà di Azure Cosmos DB con IaaS e i database locali
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: e603435a49894a9b8faa2df9b49a8572b94eecb5
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65965582"
---
# <a name="total-cost-of-ownershiptco-with-azure-cosmos-db"></a>Costo totale di proprietà (TCO) con Azure Cosmos DB

Azure Cosmos DB è stato progettato con la multi-tenancy con granularità fine e la governance delle risorse. Questa progettazione consente ad Azure Cosmos DB di funzionare a costi significativamente più bassi permettendo agli utenti di risparmiare. Attualmente Azure Cosmos DB supporta più di 280 carichi di lavoro dei clienti in un singolo computer con una densità in continuo aumento e migliaia di carichi di lavoro dei clienti all'interno di un cluster. Esegue il bilanciamento del carico delle repliche dei carichi di lavoro dei clienti in diversi computer di un cluster e in più cluster all'interno di un data center. Per altre informazioni, vedere [Azure Cosmos DB: Spingere la frontiera dei database distribuiti a livello globale](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). Grazie alla governance delle risorse, alla multi-tenancy e all'integrazione nativa con il resto dell'infrastruttura di Azure, Azure Cosmos DB è in media da 4 a 6 volte più economico di MongoDB, Cassandra o di altri OSS NoSQL in esecuzione su IaaS e fino a 10 volte più economico dei motori di database in esecuzione in locale. Vedere il documento sul [costo totale di non proprietà di un servizio cloud di database NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

Le soluzioni di database OSS NoSQL, come i motori Apache Cassandra, MongoDB, HBase, sono state progettate per essere usate in locale. Quando usate come servizio gestito, sono equivalenti a un modello di Resource Manager con un database tenant per la gestione dei cluster con provisioning e il supporto di monitoraggio. Le architetture OSS NoSQL richiedono notevoli costi operativi e trovare le competenze necessarie può risultare difficile e costoso. D'altra parte, Azure Cosmos DB è un servizio cloud completamente gestito che consente agli sviluppatori di concentrarsi sull'innovazione aziendale piuttosto che sulla gestione e la manutenzione dell'infrastruttura di database. 

A differenza di un servizio di database cloud nativo come Azure Cosmos DB, i motori di database OSS NoSQL non sono stati progettati e creati con la governance delle risorse o la multi-tenancy con granularità fine come principi architetturali fondamentali. I motori di database OSS NoSQL come Cassandra e MongoDB partono dal presupposto fondamentale che tutte le risorse della macchina virtuale in cui sono in esecuzione sono disponibili per il loro utilizzo. Molti di questi motori di database non possono funzionare se la quantità di risorse scende al di sotto di una determinata soglia, come ad esempio nel caso di piccole istanze di macchine virtuali, e sono disponibili con configurazioni consigliate dal fornitore che suggeriscono in genere macchine virtuali su larga scala con costi più elevati. Pertanto, non è possibile ospitare un database NoSQL OSS o qualsiasi altra localmente motore di database e rendono disponibile tramite un modello di tariffazione in base al consumo, ad esempio richieste al secondo o utilizzato nell'archivio.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Costo totale di proprietà di Azure Cosmos DB 

Il modello di provisioning serverless di Azure Cosmos DB elimina la necessità di effettuare il provisioning in eccesso dell'infrastruttura di database. Le risorse di Azure Cosmos DB sono fornite senza alcuna necessità di configurazioni o licenze specializzate. Di conseguenza, le applicazioni basate su Azure Cosmos DB possono essere eseguite con un risparmio del 70% del costo totale di proprietà rispetto ai database OSS NoSQL. Per alcuni esempi in tempo reale, vedere i [casi d'uso dei clienti](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Altri vantaggi del modello tariffario di Azure Cosmos DB includono:

* **Ottimo rapporto qualità/prezzo:** i partner, i clienti e gli analisti di mercato hanno confermato un ottimo rapporto qualità/prezzo di tutte le funzionalità offerte da Azure Cosmos DB rispetto a ciò che i clienti possono ottenere implementando queste soluzioni in modo autonomo o tramite altri fornitori. Le funzionalità del database come distribuzione globale, multimaster, modelli di coerenza ben definiti e intuitivi, indicizzazione automatica, sono notevolmente semplificate con Azure Cosmos DB senza alcuna complessità, sovraccarico o tempi di inattività.

* **Non è necessaria alcuna amministrazione DevOps su NoSQL:** con Azure Cosmos DB non è necessario usare DevOps per gestire le distribuzioni, eseguire la manutenzione, ridimensionare o installare patch. È possibile eseguire tutti i carichi di lavoro che si eseguirebbero con il cluster OSS NoSQL ospitato in locale o nell'infrastruttura cloud.

![Costo di proprietà di Azure Cosmos DB](./media/total-cost-ownership/tco.png)

* **Possibilità di scalare elasticamente:** la velocità effettiva di Azure Cosmos DB può essere aumentata o ridotta, permettendo di ridurre il costo di proprietà durante i periodi non di punta. I cluster OSS NoSQL distribuiti nell'infrastruttura cloud offrono un'elasticità limitata e le distribuzioni locali non sono per definizione elastiche. In Azure Cosmos DB, se si effettua il provisioning di una velocità effettiva maggiore, si garantisce la possibilità di ottenere una scalabilità lineare di tale velocità effettiva. Questa garanzia è supportata da contratti di servizio finanziari e al 99° percentile a qualsiasi livello di scalabilità.

* **Economie di scala:** un servizio gestito come Azure Cosmos DB viene eseguito con un numero elevato di nodi, integrati in modo nativo con funzionalità di rete, risorse di archiviazione e ambienti di calcolo. Grazie alla standardizzazione su larga scala di Azure Cosmos DB, è possibile risparmiare sui costi.

* **Ottimizzato per il cloud:** Azure Cosmos DB è stato progettato da zero con multi-tenancy con granularità fine e isolamento delle prestazioni. Ciò consente di posizionare, eseguire e bilanciare in modo ottimale migliaia di tenant e i relativi carichi di lavoro in cluster e data center. Al contrario, la generazione corrente di database OSS NoSQL opera in locale con l'intera macchina virtuale che si presume possa eseguire il carico di lavoro di un singolo tenant. Inoltre, questi database non sono progettati per sfruttare appieno l'infrastruttura e l'hardware di un provider di servizi cloud. Ad esempio, un motore di database OSS NoSQL non tiene conto delle differenze tra una macchina virtuale inattiva e un aggiornamento di routine dell'immagine o del fatto che il disco Premium è stato già replicato in tre livelli. Non riesce a sfruttare questi vantaggi e a trasmettere i vantaggi e i risparmi ai clienti.

* **Pagamento su base oraria:** per i carichi di lavoro su larga scala, che devono essere ridimensionati in qualsiasi momento, l'addebito avverrà unicamente su base oraria. I carichi di lavoro di un'applicazione, in genere, variano a seconda dei periodi dell'anno e dei dati sottoposti a query. Con Azure Cosmos DB, passare a un piano superiore o inferiore in base alle proprie esigenze e pagare solo per ciò che serve. Con sistemi ospitati IaaS o in locale, non è possibile abbinare questo modello, poiché non c'è un modo per ritirare l'hardware ogni ora. In questi casi, con Azure Cosmos DB è possibile risparmiare in media da 10 a 14 volte.

* **Si ottengono gratuitamente numerose funzionalità:** in Azure Cosmos DB, i carichi di lavoro di scrittura sono sostanzialmente più economici rispetto ad altri servizi di database. Inoltre, Azure Cosmos DB offre funzionalità come l'[indicizzazione automatica](indexing-policies.md), la [durata (TTL)](time-to-live.md), il [feed di modifiche](change-feed.md) e altre senza costi aggiuntivi, mentre in genere vengono addebitate con altri servizi di database.

* **Usa la valuta unificata per diversi carichi di lavoro:** a differenza di altre offerte, in Azure Cosmos DB non è necessario segmentare i carichi di lavoro, ad esempio in letture e scritture oppure effettuare il provisioning della velocità effettiva in base a ciascun tipo di carico di lavoro a seconda che si tratti della velocità effettiva di lettura o di scrittura. In Azure Cosmos DB, la velocità effettiva di cui viene effettuato il provisioning viene riservata usando una valuta unificata e normalizzata in termini di unità richiesta o UR/sec. Azure Cosmos DB non impone di assegnare una priorità ai carichi di lavoro, di eseguire la pianificazione della capacità o di pagare separatamente per ogni tipo di capacità. Tale approccio consente di scambiare facilmente la stessa UR/s tra varie operazioni e tipi di carico di lavoro.

* **Non richiede il provisioning di macchine virtuali per la scalabilità:** la maggior parte dei database operativi richiede di utilizzare macchine virtuali di grandi dimensioni per evitare l'intereferenza delle risorse che possono influire negativamente e per una governance delle risorse separata in caso di scalabilità. Questo comporta l'onere e l'impegno iniziale dei costi per i clienti. Con Azure Cosmos DB, è possibile iniziare con carichi di lavoro di piccole dimensioni e aumentare fino a dimensioni su larga scala senza interruzioni e senza tempi di inattività o impatto sulla disponibilità dei dati.

* **È possibile utilizzare la velocità effettiva con provisioning fino a un limite massimo:** in virtù del sub-core multiplexing in Azure Cosmos DB, è possibile saturare la velocità effettiva con provisioning in misura maggiore rispetto alle opzioni ospitate su IaaS o alle offerte di terze parti. Questo metodo consente di risparmiare molto di più rispetto ad altre soluzioni.

* **Stretta integrazione di Azure Cosmos DB con altri servizi di Azure:** Azure Cosmos DB offre un'integrazione nativa con funzionalità di rete, ambiente di calcolo, Funzioni di Azure (serverless), Azure IoT e altri servizi di Azure. Con questa integrazione, è possibile ottenere le migliori prestazioni e una velocità di replica dei dati in tutto il mondo con solide garanzie. Le soluzioni di terze parti non saranno in grado di eguagliare tali prestazioni o prevedono in genere un addebito aggiuntivo per offrire tali funzionalità.

* **È possibile ottenere automaticamente una disponibilità elevata, con almeno 10-20 domini di errore per impostazione predefinita:** Azure Cosmos DB supporta la distribuzione dei carichi di lavoro nei domini di errore, una funzionalità che è fondamentale per la disponibilità elevata. Offre una disponibilità elevata del 99,999 per letture e scritture al 99° percentile in qualsiasi parte del mondo. Il costo dell'implementazione di una caratteristica simile autonomamente o tramite una soluzione di terze parti sarebbe elevato.

* **È possibile ottenere automaticamente tutte le funzionalità aziendali, senza costi aggiuntivi:** Azure Cosmos DB offre il set più completo di certificazioni di conformità, sicurezza e crittografia di dati inattivi e in transito senza costi aggiuntivi (rispetto alla concorrenza). È possibile ottenere automaticamente la disponibilità a livello di area in qualsiasi parte del mondo. È possibile estendere il database in qualsiasi numero di aree di Azure e aggiungere o rimuovere aree in qualsiasi momento.

* **È possibile risparmiare fino al 65% dei costi con capacità di riserva:** La [capacità di riserva](cosmos-db-reserved-capacity.md) di Azure Cosmos DB permette di risparmiare grazie al pagamento anticipato delle risorse di Azure Cosmos DB per un anno o tre anni. È possibile ridurre significativamente i costi con impegni inziali di un anno o di tre anni e risparmiare tra il 20 e il 65% rispetto al prezzo normale. Nei carichi di lavoro mission-critical è possibile ottenere i contratti di servizio migliori in termini di capacità di provisioning.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [come il modello di determinazione dei prezzi di Azure Cosmos DB sia conveniente per i clienti](total-cost-ownership.md)
* Altre informazioni sull'[ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni sull'[ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[Ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[ottimizzazione del costo delle operazioni di lettura e scrittura](optimize-cost-reads-writes.md)
* Altre informazioni sull'[Ottimizzazione dei costi delle query](optimize-cost-queries.md)
* Altre informazioni sull'[ottimizzazione dei costi degli account Cosmos multiarea](optimize-cost-regions.md)
* Altre informazioni sul [costo totale di non proprietà di un servizio cloud di database NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
