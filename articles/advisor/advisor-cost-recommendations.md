---
title: Ridurre i costi del servizio con Azure Advisor
description: Usare Azure Advisor per ottimizzare il costo delle distribuzioni di Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: d234e89d0d042999805fae73d3df24c03d1027c9
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654039"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>Ridurre i costi del servizio usando Azure Advisor

Azure Advisor consente di ottimizzare e ridurre la spesa complessiva di Azure identificando le risorse inattive e sottoutilizzate.È possibile ottenere consigli sui costi nella scheda **Costo** del dashboard di Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Ottimizzare la spesa correlata alle macchine virtuali ridimensionando o arrestando le istanze sottoutilizzate 

Sebbene in alcuni scenari applicativi possa verificarsi un utilizzo ridotto legato alla progettazione, è comunque possibile risparmiare denaro mediante la gestione delle dimensioni e del numero delle macchine virtuali. 

Le azioni consigliate vengono arrestate o ridimensionate, specifiche della risorsa valutata.

Il modello di valutazione avanzata in Advisor considera la chiusura di macchine virtuali quando entrambe le istruzioni sono vere: 
- P95th del valore massimo del valore massimo di utilizzo della CPU è inferiore al 3%. 
- L'utilizzo della rete è inferiore al 2% per un periodo di sette giorni.
- Il numero di richieste di memoria è inferiore ai valori soglia

Advisor considera il ridimensionamento delle macchine virtuali quando è possibile adattare il carico corrente in uno SKU più piccolo (all'interno della stessa famiglia di SKU) o un numero minore di istanze in modo che:
- Il carico corrente non supera il 80% di utilizzo per i carichi di lavoro che non sono rivolte agli utenti. 
- Il carico non supera il 40% per i carichi di lavoro rivolte agli utenti. 

In questo caso, Advisor determina il tipo di carico di lavoro analizzando le caratteristiche di utilizzo della CPU del carico di lavoro.

Advisor Mostra il risparmio sui costi stimato per l'azione consigliata: ridimensionare o arrestare. Per il ridimensionamento, Advisor fornisce informazioni sugli SKU correnti e di destinazione.

Se si desidera essere più aggressivi nell'identificare le macchine virtuali sottoutilizzate, è possibile modificare la regola di utilizzo della CPU in base alla sottoscrizione.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>Ottimizza la spesa per i server MariaDB, MySQL e PostgreSQL con il dimensionamento corretto 
Advisor analizza l'utilizzo e valuta se le risorse del server di database MariaDB, MySQL o PostgreSQL sono state sottoutilizzate per un periodo di tempo prolungato negli ultimi sette giorni. Un basso utilizzo delle risorse comporta una spesa indesiderata che può essere risolta senza un impatto significativo sulle prestazioni. Per ridurre i costi e gestire in modo efficiente le risorse, è consigliabile ridurre la dimensione di calcolo (VCore) di metà.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Ridurre i costi eliminando i circuiti ExpressRoute il cui provisioning è stato annullato

Advisor identifica i circuiti ExpressRoute di Azure che sono stati in stato provider **senza provisioning** per più di un mese. Si consiglia di eliminare il circuito se non si prevede di effettuare il provisioning del circuito con il provider di connettività.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Ridurre i costi eliminando o riconfigurando i gateway di rete virtuale inattivi

Advisor identifica i gateway di rete virtuale che sono rimasti inattivi per più di 90 giorni. Poiché questi gateway sono fatturati su base oraria, è consigliabile riconfigurarli o eliminarli se non si prevede di usarli più. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Acquistare istanze di macchina virtuale riservate per risparmiare denaro rispetto ai costi dei piani con pagamento in base al consumo

Advisor esamina l'utilizzo della macchina virtuale negli ultimi 30 giorni per determinare se è possibile risparmiare denaro acquistando una prenotazione di Azure. Advisor Mostra le aree e le dimensioni in cui il rischio di risparmio è maggiore e il risparmio stimato dalle prenotazioni di acquisto. Con le prenotazioni di Azure è possibile sostenere anticipatamente i costi di base delle macchine virtuali. Gli sconti si applicano automaticamente alle macchine virtuali nuove o esistenti con le stesse dimensioni e la stessa area delle prenotazioni. [Altre informazioni sulle istanze di macchina virtuale riservate di Azure.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor informa anche le istanze riservate che scadranno nei 30 giorni successivi. Si consiglia di acquistare nuove istanze riservate per evitare i prezzi con pagamento in base al consumo.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Acquistare istanze riservate per diversi tipi di risorse per risparmiare sui costi con pagamento in base al consumo

Advisor analizza i modelli di utilizzo per gli ultimi 30 giorni per le risorse seguenti e consiglia gli acquisti di capacità riservata che ottimizzano i costi.

### <a name="azure-cosmos-db-reserved-capacity"></a>Capacità riservata Azure Cosmos DB
Advisor analizza i modelli di utilizzo del Azure Cosmos DB negli ultimi 30 giorni e consiglia gli acquisti di capacità riservata per ottimizzare i costi. Utilizzando la capacità riservata, è possibile effettuare il pre-acquisto Azure Cosmos DB utilizzo orario e risparmiare sui costi con pagamento in base al consumo. La capacità riservata è un vantaggio di fatturazione e si applica automaticamente alle distribuzioni nuove ed esistenti. Advisor calcola le stime di risparmio per le singole sottoscrizioni usando i prezzi di prenotazione di 3 anni e estrapolando i modelli di utilizzo osservati negli ultimi 30 giorni. Sono disponibili raccomandazioni per l'ambito condiviso per gli acquisti di capacità riservata e possono aumentare il risparmio.

### <a name="sql-paas-reserved-capacity"></a>Capacità riservata di SQL PaaS
Advisor analizza i pool di database elastici di SQL PaaS e i modelli di utilizzo di SQL Istanza gestita negli ultimi 30 giorni. Si consiglia quindi di acquistare la capacità riservata per ottimizzare i costi. Con la capacità riservata è possibile pre-acquistare l'utilizzo orario del database SQL e risparmiare sui costi di calcolo SQL. La licenza SQL viene addebitata separatamente e non viene scontata dalla prenotazione. La capacità riservata è un vantaggio di fatturazione e si applica automaticamente alle distribuzioni nuove ed esistenti. Advisor calcola le stime di risparmio per le singole sottoscrizioni usando i prezzi di prenotazione di 3 anni e estrapolando i modelli di utilizzo osservati negli ultimi 30 giorni. Sono disponibili raccomandazioni per l'ambito condiviso per gli acquisti di capacità riservata e possono aumentare il risparmio.

### <a name="app-service-stamp-fee-reserved-capacity"></a>Capacità riservata del timbro del servizio app
Advisor analizza il modello di utilizzo della tariffa per gli Stamp per l'ambiente di isolamento del servizio app Azure negli ultimi 30 giorni e consiglia gli acquisti di capacità riservata che ottimizzano i costi. Con la capacità riservata è possibile pre-acquistare l'utilizzo orario per la tassa di timbro per l'ambiente isolato e risparmiare sui costi con pagamento in base al consumo. Si noti che la capacità riservata si applica solo alla tariffa di timbro e non alle istanze del servizio app. La capacità riservata è un vantaggio di fatturazione e si applica automaticamente alle distribuzioni nuove ed esistenti. Advisor calcola il salvataggio delle stime per le singole sottoscrizioni usando i prezzi di prenotazione di 3 anni in base ai modelli di utilizzo negli ultimi 30 giorni.

### <a name="blob-storage-reserved-capacity"></a>Capacità riservata archiviazione BLOB
Advisor analizza l'archiviazione BLOB di Azure e Azure Data Lake l'utilizzo dell'archiviazione negli ultimi 30 giorni. Calcola quindi gli acquisti di capacità riservata che ottimizzano i costi. Con la capacità riservata è possibile pre-acquistare l'utilizzo orario e risparmiare sui costi di richiesta correnti. La capacità riservata dell'archiviazione BLOB si applica solo ai dati archiviati negli account per utilizzo generico di BLOB di Azure V2 e Azure Data Lake Storage Gen2. La capacità riservata è un vantaggio di fatturazione e si applica automaticamente alle distribuzioni nuove ed esistenti. Advisor calcola le stime di risparmio per le singole sottoscrizioni usando i prezzi di prenotazione di 3 anni e i modelli di utilizzo osservati negli ultimi 30 giorni. Sono disponibili raccomandazioni per l'ambito condiviso per gli acquisti di capacità riservata e possono aumentare il risparmio.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>Capacità riservata di MariaDB, MySQL e PostgreSQL
Advisor analizza i modelli di utilizzo per database di Azure per MariaDB, database di Azure per MySQL e database di Azure per PostgreSQL negli ultimi 30 giorni. Si consiglia quindi di acquistare la capacità riservata per ottimizzare i costi. Usando la capacità riservata, puoi acquistare in anticipo MariaDB, MySQL e l'utilizzo orario di PostgreSQL e risparmiare sui costi attuali. La capacità riservata è un vantaggio di fatturazione e si applica automaticamente alle distribuzioni nuove ed esistenti. Advisor calcola le stime di risparmio per le singole sottoscrizioni usando i prezzi di prenotazione di 3 anni e i modelli di utilizzo osservati negli ultimi 30 giorni. Sono disponibili raccomandazioni per l'ambito condiviso per gli acquisti di capacità riservata e possono aumentare il risparmio.

### <a name="azure-synapse-analytics-formerly-sql-data-warehouse-reserved-capacity"></a>Capacità riservata di Azure sinapsi Analytics (in precedenza SQL Data Warehouse)
Advisor analizza i modelli di utilizzo di Azure sinapsi Analytics negli ultimi 30 giorni e consiglia gli acquisti di capacità riservata che ottimizzano i costi. Con la capacità riservata è possibile acquistare in anticipo l'utilizzo orario di sinapsi Analytics e risparmiare sui costi su richiesta. La capacità riservata è un vantaggio di fatturazione e si applica automaticamente alle distribuzioni nuove ed esistenti. Advisor calcola le stime di risparmio per le singole sottoscrizioni usando i prezzi di prenotazione di 3 anni e i modelli di utilizzo osservati negli ultimi 30 giorni. Sono disponibili raccomandazioni per l'ambito condiviso per gli acquisti di capacità riservata e possono aumentare il risparmio.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Eliminare gli indirizzi IP pubblici non associati per risparmiare denaro

Advisor identifica gli indirizzi IP pubblici che non sono associati a risorse di Azure come i bilanciamenti del carico e le macchine virtuali. A questi indirizzi IP pubblici è associato un addebito nominale. Se non si prevede di usarli, è possibile risparmiare denaro eliminando questi elementi.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Eliminare le pipeline di Azure Data Factory con esito negativo

Advisor rileva Azure Data Factory pipeline che hanno ripetutamente esito negativo. È consigliabile risolvere i problemi o eliminare le pipeline, se non sono necessarie. Verranno addebitati i costi per queste pipeline, anche se non sono in grado di soddisfare le proprie problemi.

## <a name="use-standard-snapshots-for-managed-disks"></a>USA snapshot standard per Managed Disks
Per risparmiare il 60% dei costi, è consigliabile archiviare gli snapshot nell'archiviazione standard, indipendentemente dal tipo di archiviazione del disco padre. Questa opzione è l'opzione predefinita per gli snapshot del disco gestito. Advisor identifica gli snapshot archiviati in archiviazione Premium e consiglia di eseguire la migrazione da Premium ad archiviazione standard. [Scopri di più sui prezzi dei dischi gestiti.](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="use-lifecycle-management"></a>Usare la gestione del ciclo di vita
Con l'intelligence sul numero di oggetti di archiviazione BLOB di Azure, sulle dimensioni totali e sulle transazioni, Advisor rileva se è necessario abilitare la gestione del ciclo di vita per i dati di livello su uno o più account di archiviazione. Viene richiesto di creare regole di gestione del ciclo di vita per la suddivisione automatica dei dati in livelli di archiviazione ad accesso sporadico o Archivio per ottimizzare i costi di archiviazione mantenendo i dati nell'archiviazione BLOB di Azure per la compatibilità delle applicazioni.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Creare una raccomandazione del disco del sistema operativo temporaneo
Il [disco del sistema operativo temporaneo](../virtual-machines/ephemeral-os-disks.md) consente di: 
- Risparmiare sui costi di archiviazione per i dischi del sistema operativo. 
- Ottenere una latenza di lettura/scrittura più bassa per i dischi del sistema operativo. 
- Ottenere più velocemente operazioni di ricreazione dell'immagine della macchina virtuale reimpostando il sistema operativo e il disco temporaneo sullo stato originale.

È preferibile usare un disco del sistema operativo temporaneo per macchine virtuali IaaS di breve durata o macchine virtuali con carichi di lavoro senza stato. Advisor fornisce consigli per le risorse che possono trarre vantaggio dal disco del sistema operativo temporaneo.

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>Ridurre la cache della tabella Esplora dati di Azure-periodo (criteri) per l'ottimizzazione dei costi del cluster (anteprima)
Advisor identifica le risorse in cui la riduzione dei criteri di cache della tabella consente di liberare i nodi del cluster Esplora dati di Azure con un utilizzo ridotto della CPU, della memoria e di una configurazione della dimensione della cache elevata.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Come accedere ai consigli sui costi in Azure Advisor

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Cercare e selezionare [**Advisor**](https://aka.ms/azureadvisordashboard) da qualsiasi pagina.

1. Nel dashboard di **Advisor** selezionare la scheda **costo** .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione ad Advisor](advisor-get-started.md)
* [Raccomandazioni sulle prestazioni di Advisor](advisor-performance-recommendations.md)
* [Raccomandazioni per la disponibilità elevata di Advisor](advisor-high-availability-recommendations.md)
* [Raccomandazioni sulla sicurezza di Advisor](advisor-security-recommendations.md)
* [Raccomandazioni sull'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md)
