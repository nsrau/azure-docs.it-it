---
title: Creare contenitori e database di Azure Cosmos in modalità di scalabilità automatica.
description: Informazioni sui vantaggi, sui casi d'uso e su come eseguire il provisioning di database e contenitori di Azure Cosmos in modalità di scalabilità automatica.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: 20b0bcfe5043d4767199c36796fa1123ed779363
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791147"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>Creare contenitori e database di Azure Cosmos con velocità effettiva a scalabilità automatica

Azure Cosmos DB consente di impostare la velocità effettiva standard (manuale) o a scalabilità automatica per il provisioning in database e contenitori. Questo articolo descrive i vantaggi e i casi d'uso della velocità effettiva con provisioning a scalabilità automatica. 

La velocità effettiva con provisioning a scalabilità automatica è particolarmente adatta per carichi di lavoro cruciali con modelli di traffico variabili o imprevedibili e che richiedono contratti di servizio per prestazioni elevate e scalabilità. 

Con la scalabilità automatica, Azure Cosmos DB **ridimensiona automaticamente e istantaneamente la velocità effettiva (UR/sec)** del database o del contenitore in base all'utilizzo, senza alcun impatto su disponibilità, latenza e velocità effettiva né sulle prestazioni del carico di lavoro. 

## <a name="benefits-of-autoscale"></a>Vantaggi della scalabilità automatica

I database e i contenitori di Azure Cosmos configurati con la velocità effettiva con provisioning a scalabilità automatica offrono i vantaggi seguenti:

* **Simple:** La scalabilità automatica elimina la complessità della gestione di UR/sec basata su script personalizzati o sulla capacità di scalabilità manuale. 

* **Scalabilità**: I database e i contenitori ridimensionano automaticamente la velocità effettiva con provisioning in base alle esigenze, senza interruzioni delle connessioni client o delle applicazioni né alcun impatto sui contratti di servizio di Azure Cosmos DB.

* **Convenienza**: La scalabilità automatica consente di ottimizzare l'utilizzo di UR/sec e i costi grazie alla riduzione quando non è in uso. Si paga solo per le risorse necessarie per i carichi di lavoro su base oraria. Di tutte le ore in un mese, se si imposta la scalabilità automatica max ur/s (tmax) e si usa la quantità totale tmax per il 66% delle ore o meno, sarà possibile risparmiare con la scalabilità automatica. Per altre informazioni, vedere l'articolo [come scegliere tra la velocità effettiva con provisioning standard (manuale) e scalabilità](how-to-choose-offer.md) automatica.

* **Disponibilità elevata**: I database e i contenitori che usano la scalabilità automatica usano lo stesso back-end di Azure Cosmos DB distribuito a livello globale, a tolleranza di errore e a disponibilità elevata per garantire la durabilità e un'elevata disponibilità dei dati.

## <a name="use-cases-of-autoscale"></a>Casi d'uso di scalabilità automatica

I casi d'uso di scalabilità automatica includono:

* **Carichi di lavoro variabili o imprevedibili**: Quando i carichi di lavoro hanno picchi di utilizzo variabili o imprevedibili, la scalabilità automatica consente di aumentare o ridurre automaticamente la capacità in base all'utilizzo. Gli esempi includono i siti Web di vendita al dettaglio con modelli di traffico diversi a seconda della stagionalità, i carichi di lavoro IoT con picchi in diversi momenti della giornata, le applicazioni line-of-business per le quali i picchi si verificano alcune volte al mese o all'anno e così via. Con la scalabilità automatica non è più necessario eseguire manualmente il provisioning per la capacità massima o media. 

* **Nuove applicazioni**: Se si sta sviluppando una nuova applicazione e non si è certi della velocità effettiva (UR/sec) necessaria, la scalabilità automatica semplifica le operazioni iniziali. È possibile iniziare con un punto di ingresso per la scalabilità automatica di 400-4000 UR/sec, monitorare l'utilizzo e stabilire qual è il valore UR/sec corretto nel tempo.

* **Applicazioni usate raramente**: Se un'applicazione viene usata solo per poche ore alcune volte al giorno, alla settimana o al mese, ad esempio un sito applicazione, Web o blog con volume ridotto, la scalabilità automatica regola la capacità in modo da gestire il momento di utilizzo massimo e ridurre le prestazioni quando termina. 

* **Carichi di lavoro per sviluppo e test**: Se l'utente o il suo team usa i database e i contenitori di Azure Cosmos durante le ore di lavoro, ma non ne ha bisogno nelle ore notturne o nei fine settimana, la scalabilità automatica consente di risparmiare sui costi riducendo la capacità al minimo quando non è in uso. 

* **Carichi di lavoro e query di produzione pianificati**: Se si prevede di eseguire una serie di richieste, operazioni o query pianificate durante i periodi di inattività, la scalabilità automatica può essere molto utile. Quando è necessario eseguire il carico di lavoro, la velocità effettiva viene aumentata automaticamente in base alle esigenze e successivamente viene ridotta. 

La creazione di una soluzione personalizzata a questi problemi non solo richiede una quantità di tempo enorme, ma introduce anche complessità nella configurazione o nel codice dell'applicazione. La scalabilità automatica consente di gestire con facilità gli scenari precedenti ed elimina la necessità di procedure personalizzate o manuali di ridimensionamento della capacità. 

## <a name="how-autoscale-provisioned-throughput-works"></a>Funzionamento della velocità effettiva con provisioning a scalabilità automatica

Quando si configurano i contenitori e i database con la scalabilità automatica, è necessario specificare la velocità effettiva massima `Tmax` richiesta. Azure Cosmos DB ridimensiona la velocità effettiva `T` come `0.1*Tmax <= T <= Tmax`. Se ad esempio si imposta la velocità effettiva massima su 20.000 UR/sec, la velocità effettiva verrà ridimensionata tra 2000 e 20.000 UR/sec. Poiché il ridimensionamento è automatico e istantaneo, in qualsiasi momento è possibile usare fino al valore `Tmax` di cui è stato effettuato il provisioning senza alcun ritardo. 

Ogni ora viene addebitata la velocità effettiva più elevata `T` a cui il sistema è stato ridimensionato nel corso dell'ora.

Il punto di ingresso per la velocità effettiva massima a scalabilità automatica `Tmax` inizia a 4000 UR/sec, che si ridimensiona tra 400 e 4000 UR/sec. È possibile impostare `Tmax` con incrementi di 1000 UR/sec e modificare il valore in qualsiasi momento.  

## <a name="enable-autoscale-on-existing-resources"></a>Abilitare la scalabilità automatica per le risorse esistenti

Usare il [portale di Azure](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) per abilitare la scalabilità automatica per un database o un contenitore esistente. È possibile passare dalla velocità effettiva con provisioning a scalabilità automatica alla velocità effettiva con provisioning standard (manuale) e viceversa in qualsiasi momento. Per altre informazioni, vedere questa [documentazione](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work). Attualmente, per tutte le API, è possibile usare la portale di Azure solo per abilitare la scalabilità automatica sulle risorse esistenti.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a> Limiti di archiviazione e velocità effettiva per la scalabilità automatica

Per qualsiasi valore di `Tmax`, il database o il contenitore può archiviare un totale di `0.01 * Tmax GB`. Raggiunta questa quantità di spazio di archiviazione, il valore massimo di UR/sec verrà aumentato automaticamente in base al nuovo valore di archiviazione, senza alcun impatto sull'applicazione. 

Se ad esempio si inizia con un numero massimo di UR/sec pari a 50.000 (con scalabilità compresa tra 5000 e 50.000 UR/sec), è possibile archiviare fino a 500 GB di dati. Se si superano i 500 GB, se ad esempio l'archiviazione è ora pari a 600 GB, il nuovo numero massimo di UR/sec è 60.000 UR/sec (con scalabilità compresa tra 6000 e 60.000 UR/sec).

Quando si usa la velocità effettiva a livello di database con scalabilità automatica, è possibile fare in modo che i primi 25 contenitori condividano un numero massimo di UR/sec di scalabilità automatica pari a 4000 (con scalabilità compresa tra 400 e 4000 UR/sec), purché non si superino 40 GB di spazio di archiviazione. Per altre informazioni, vedere questa [documentazione](autoscale-faq.md#can-i-change-the-max-rus-on-the-database-or-container).

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>Confronto tra contenitori configurati con velocità effettiva manuale e a scalabilità automatica
Per informazioni dettagliate, vedere la [documentazione](how-to-choose-offer.md) su come scegliere tra la velocità effettiva standard (manuale) e quella a scalabilità automatica.  

|| Contenitori con velocità effettiva standard (manuale)  | Contenitori con velocità effettiva a scalabilità automatica |
|---------|---------|---------|
| **Provisioning velocità effettiva (UR/sec)** | Con provisioning manuale. | Ridimensionamento automatico e istantaneo in base ai modelli di utilizzo del carico di lavoro. |
| **Limitazione della frequenza delle richieste/operazioni (429)**  | Può verificarsi se il consumo supera la capacità sottoposta a provisioning. | Non si verifica se il consumo di UR/sec rientra nell'intervallo impostato per la velocità effettiva a scalabilità automatica.    |
| **Pianificazione della capacità** |  È necessario eseguire la pianificazione della capacità ed effettuare il provisioning della velocità effettiva esatta necessaria. |    Il sistema provvede automaticamente alla pianificazione della capacità e alla gestione della capacità. |
| **Prezzi** | Si paga per le UR/sec con provisioning manuale per ora, usando la [tariffa oraria delle UR/sec standard (manuale)](https://azure.microsoft.com/pricing/details/cosmos-db/). | Si paga ogni ora per i valori massimi di UR/sec raggiunti dal sistema entro l'ora. <br/><br/> Per gli account con singola area di scrittura, si paga per le UR/sec usate su base oraria, applicando la [tariffa oraria delle UR/sec di scalabilità automatica](https://azure.microsoft.com/pricing/details/cosmos-db/). <br/><br/>Per gli account con più aree di scrittura, non sono previsti costi aggiuntivi per la scalabilità automatica. Si paga per la velocità effettiva usata su base oraria applicando la stessa [tariffa oraria delle UR/sec multimaster](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| **Ideale per tipi di carico di lavoro** |  Carichi di lavoro prevedibili e stabili|   Carichi di lavoro imprevedibili e variabili  |

## <a name="next-steps"></a>Passaggi successivi

* Vedere le [domande frequenti sulla scalabilità automatica](autoscale-faq.md).
* Informazioni su come [scegliere tra la velocità effettiva manuale e a scalabilità automatica](how-to-choose-offer.md).
* Informazioni su come [effettuare il provisioning della velocità effettiva a scalabilità automatica in un database o un contenitore di Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Altre informazioni sul [partizionamento](partition-data.md) in Azure Cosmos DB.


