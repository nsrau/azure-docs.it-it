---
title: Creare contenitori e database di Azure Cosmos nella velocità effettiva con provisioning automatico.
description: Informazioni sui vantaggi, sui casi d'uso e su come eseguire il provisioning di database e contenitori di Azure Cosmos nella velocità effettiva con provisioning automatico.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 81a13dcb7955a7d46f485416bf9b7e4e7be4d9ac
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791715"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-provisioned-throughput"></a>Creare contenitori e database di Azure Cosmos con scalabilità automatica con provisioning della velocità effettiva

Azure Cosmos DB consente di configurare i contenitori con la velocità effettiva con provisioning standard (manuale) o con scalabilità automatica con provisioning. Questo articolo descrive i vantaggi e i casi d'uso della scalabilità automatica.

> [!NOTE]
> È possibile [abilitare la scalabilità automatica solo per i nuovi database e per i contenitori](#create-db-container-autoscale) . Non è disponibile per i contenitori e i database esistenti.

Oltre al provisioning standard della velocità effettiva, è ora possibile configurare i contenitori di Azure Cosmos con la velocità effettiva con provisioning di ridimensionamento automatico. I contenitori e i database configurati nella velocità effettiva con provisioning **automatico consentono di ridimensionare automaticamente e immediatamente la velocità effettiva con provisioning in base alle esigenze dell'applicazione senza influito sulla disponibilità, sulla latenza, sulla velocità effettiva o sulle prestazioni del carico di lavoro a livello globale.**

Quando si configurano i contenitori e i database in scalabilità automatica, è `Tmax` necessario specificare la velocità effettiva massima che non deve essere superata. I contenitori possono quindi ridimensionare la velocità `0.1*Tmax < T < Tmax`effettiva in modo tale che. In altre parole, i contenitori e i database si ridimensionano immediatamente in base alle esigenze del carico di lavoro, dal minimo del 10% del valore di velocità effettiva massimo configurato fino al valore massimo della velocità effettiva configurata. Dopo aver configurato la scalabilità automatica, è possibile modificare l'`Tmax`impostazione di velocità effettiva massima () in un database o in un contenitore in qualsiasi momento. Con l'opzione di scalabilità automatica, la velocità effettiva minima 400 ur/s per ogni contenitore o database non è più applicabile.

Per la velocità effettiva massima specificata nel contenitore o nel database, il sistema consente il funzionamento entro il limite di archiviazione calcolato. Se viene superato il limite di archiviazione, la velocità effettiva massima viene regolata automaticamente in un valore superiore. Quando si usa la velocità effettiva a livello di database con scalabilità automatica, il numero di contenitori consentiti all'interno di un database viene calcolato come: `0.001*TMax`. Se, ad esempio, si esegue il provisioning delle unità di scalabilità automatica 20.000, il database può contenere 20 contenitori.

## <a name="benefits-of-autoscale-provisioned-throughput"></a><a id="autoscale-benefits"></a>Vantaggi della velocità effettiva con provisioning con scalabilità automatica

I contenitori di Azure Cosmos configurati con la scalabilità automatica offrono i vantaggi seguenti:

* **Semplice:** I contenitori con scalabilità automatica tolgono la complessità per gestire manualmente la velocità effettiva con provisioning (UR) e la capacità per diversi contenitori.

* **Scalabile:** I contenitori con scalabilità automatica scalano senza difficoltà la capacità di velocità effettiva con provisioning, se necessario. Non vi sono rotture per le connessioni client, le applicazioni e non influiscano sui contratti di contratto esistenti.

* **Conveniente:** Quando si usano i contenitori configurati con la scalabilità automatica, si paga solo per le risorse necessarie per i carichi di lavoro su base oraria.

* **Disponibilità elevata:** I contenitori con scalabilità automatica usano lo stesso back-end distribuito a livello globale, a tolleranza di errore e a disponibilità elevata per garantire la durabilità e la disponibilità elevata dei dati.

## <a name="use-cases-of-autoscale-provisioned-throughput"></a><a id="autoscale-usecases"></a>Casi d'uso della velocità effettiva con provisioning automatico

I casi d'uso per i contenitori di Azure Cosmos configurati con la scalabilità automatica includono:

* **Carichi di lavoro variabili:** Quando si esegue un'applicazione di uso leggero con utilizzo massimo di 1 ora per diverse ore ogni giorno o più volte all'anno. Gli esempi includono le applicazioni per le risorse umane, il budget e la creazione di report operativi. Per questi scenari, è possibile usare i contenitori configurati con la scalabilità automatica e non è più necessario eseguire manualmente il provisioning per una capacità massima o media.

* **Carichi di lavoro imprevedibili:** Quando si eseguono i carichi di lavoro in cui è presente un utilizzo del database durante il giorno, ma anche picchi di attività difficili da stimare. Un esempio include un sito di traffico che rileva un picco di attività quando le previsioni meteorologiche cambiano. I contenitori configurati con la scalabilità automatica regolano la capacità per soddisfare le esigenze del carico massimo dell'applicazione e la scalabilità verso il basso quando si verifica il sovraccarico dell'attività.

* **Nuove applicazioni:** Se si distribuisce una nuova applicazione e non si è certi della velocità effettiva con provisioning (ad esempio, quante UR) è necessario. Con i contenitori configurati con la scalabilità automatica, è possibile ridimensionare automaticamente le esigenze e i requisiti di capacità dell'applicazione.

* **Applicazioni usate raramente:** Se si dispone di un'applicazione che viene utilizzata solo per alcune ore diverse volte al giorno o alla settimana o al mese, ad esempio un sito Web/Blog/applicazione a volume ridotto.

* **Database di sviluppo e test:** Se gli sviluppatori usano contenitori durante le ore lavorative, ma non sono necessari nelle notti o nei fine settimana. Con i contenitori configurati con la scalabilità automatica, si riduce al minimo quando non è in uso.

* **Carichi di lavoro/query di produzione pianificati:** Quando si dispone di una serie di richieste/operazioni/query pianificate su un singolo contenitore e in caso di periodi di inattività in cui si desidera eseguire con una velocità effettiva bassa assoluta, è ora possibile eseguire questa operazione in modo semplice. Quando una query o una richiesta pianificata viene inviata a un contenitore configurato con la scalabilità automatica, il ridimensionamento viene eseguito automaticamente in base alle esigenze e viene eseguita l'operazione.

Le soluzioni ai problemi precedenti non solo richiedono una quantità di tempo molto elevata nell'implementazione, ma introducono anche complessità nella configurazione o nel codice e spesso richiedono interventi manuali per risolverli. La scalabilità automatica consente di usare gli scenari precedenti, in modo che non sia più necessario preoccuparsi di questi problemi.

## <a name="comparison--standard-manual-vs-autoscale-provisioned-throughput"></a>Confronto: standard (manuale) rispetto alla velocità effettiva con provisioning con scalabilità automatica

|  | Contenitori configurati con la velocità effettiva con provisioning standard  | Contenitori configurati con la velocità effettiva con provisioning con scalabilità automatica |
|---------|---------|---------|
| **Velocità effettiva con provisioning** | Provisioning manuale. | Ridimensionato automaticamente e istantaneamente in base ai modelli di utilizzo del carico di lavoro. |
| **Limitazione della frequenza delle richieste/operazioni (429)**  | Potrebbe verificarsi se il consumo supera la capacità sottoposta a provisioning. | Non si verificherà se la velocità effettiva utilizzata rientra nella velocità effettiva massima scelta con la scalabilità automatica.   |
| **Pianificazione della capacità** |  È necessario eseguire una pianificazione iniziale della capacità e il provisioning della velocità effettiva necessaria. |    Non è necessario preoccuparsi della pianificazione della capacità. Il sistema occupa automaticamente la pianificazione della capacità e la gestione della capacità. |
| **Prezzi** | UR/sec di cui è stato effettuato manualmente il provisioning all'ora. | Per gli account con singola area di scrittura, si paga per la velocità effettiva usata su base oraria, usando la tariffa di ridimensionamento automatico ur/sec per ora. <br/><br/>Per gli account con più aree di scrittura, non sono previsti costi aggiuntivi per la scalabilità automatica. Paghi per la velocità effettiva usata su base oraria usando la stessa tariffa per più master ur/sec per ogni ora. |
| **Ideale per i tipi di carico di lavoro** |  Carichi di lavoro prevedibili e stabili|   Carichi di lavoro imprevedibili e variabili  |

## <a name="create-a-database-or-a-container-with-autoscale"></a><a id="create-db-container-autoscale"></a>Creare un database o un contenitore con scalabilità automatica

È possibile configurare la scalabilità automatica per nuovi database o contenitori durante la creazione tramite la portale di Azure. Usare la procedura seguente per creare un nuovo database o contenitore, abilitare la scalabilità automatica e specificare la velocità effettiva massima (UR/sec).

1. Accedere al [portale di Azure](https://portal.azure.com) o al [Azure Cosmos DB Explorer.](https://cosmos.azure.com/)

1. Passare all'account di Azure Cosmos DB e aprire la scheda **Esplora dati** .

1. Selezionare **nuovo contenitore.** Immettere un nome per il database, il contenitore e una chiave di partizione. In **velocità effettiva**selezionare l'opzione di **scalabilità** automatica e scegliere la velocità effettiva massima (UR/sec) che il database o il contenitore non può superare quando si usa l'opzione di scalabilità automatica.

   ![Creazione di un contenitore e configurazione della velocità effettiva di scalabilità automatica](./media/provision-throughput-autoscale/create-container-autoscale-mode.png)

1. Selezionare **OK**.

È possibile creare un database con velocità effettiva condivisa con scalabilità automatica selezionando l'opzione **provisioning database throughput** .

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a>Limiti di archiviazione e velocità effettiva per la scalabilità automatica

La tabella seguente illustra i limiti di archiviazione e massimi per le diverse opzioni di scalabilità automatica:

|Limite massimo di velocità effettiva  |Limite massimo di archiviazione  |
|---------|---------|
|4000 ur/sec  |   50 GB    |
|20.000 UR/sec  |  200 GB  |
|100.000 UR/sec    |  1 TB   |
|500.000 UR/sec    |  5 TB  |

## <a name="next-steps"></a>Passaggi successivi

* Esaminare le [domande frequenti sulla scalabilità](autoscale-faq.md)automatica.
* Altre informazioni sulle [partizioni logiche](partition-data.md).
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
