---
title: Creare contenitori e database di Azure Cosmos in modalità di scalabilità automatica.
description: Informazioni sui vantaggi, sui casi d'uso e su come eseguire il provisioning di database e contenitori di Azure Cosmos in modalità di scalabilità automatica.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 49fea2cc9a48d5afc794d6932208b61252bea424
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196491"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autoscale-mode"></a>Creare contenitori e database di Azure Cosmos in modalità di scalabilità automatica

Azure Cosmos DB consente di effettuare il provisioning della velocità effettiva nei contenitori in modalità manuale o con scalabilità automatica. Questo articolo descrive i vantaggi e i casi d'uso della modalità di scalabilità automatica.

> [!NOTE]
> È possibile [abilitare la scalabilità automatica solo per i nuovi database e per i contenitori](#create-db-container-autoscale) . Non è disponibile per i contenitori e i database esistenti.

Oltre al provisioning manuale della velocità effettiva, è ora possibile configurare i contenitori di Azure Cosmos in modalità di scalabilità automatica. I contenitori e i database configurati in modalità di scalabilità **automatica consentono di ridimensionare automaticamente la velocità effettiva con provisioning in base alle esigenze dell'applicazione senza influito sulla disponibilità, sulla latenza, sulla velocità effettiva o sulle prestazioni del carico di lavoro a livello globale.**

Quando si configurano i contenitori e i database in modalità di scalabilità automatica, `Tmax` è necessario specificare la velocità effettiva massima che non deve essere superata. I contenitori possono quindi ridimensionare la velocità `0.1*Tmax < T < Tmax`effettiva in modo tale che. In altre parole, i contenitori e i database si ridimensionano immediatamente in base alle esigenze del carico di lavoro, dal minimo del 10% del valore di velocità effettiva massimo configurato fino al valore massimo della velocità effettiva configurata. Dopo aver configurato la scalabilità automatica, è possibile modificare l'`Tmax`impostazione di velocità effettiva massima () in un database o in un contenitore in qualsiasi momento. Con l'opzione di scalabilità automatica, la velocità effettiva minima 400 ur/s per ogni contenitore o database non è più applicabile.

Per la velocità effettiva massima specificata nel contenitore o nel database, il sistema consente il funzionamento entro il limite di archiviazione calcolato. Se viene superato il limite di archiviazione, la velocità effettiva massima viene regolata automaticamente in un valore superiore. Quando si usa la velocità effettiva a livello di database con la modalità di ridimensionamento automatico, il numero di contenitori `0.001*TMax`consentiti in un database viene calcolato come:. Se, ad esempio, si esegue il provisioning delle unità di scalabilità automatica 20.000, il database può contenere 20 contenitori.

## <a name="benefits-of-autoscale-mode"></a><a id="autoscale-benefits"></a>Vantaggi della modalità di scalabilità automatica

I contenitori di Azure Cosmos configurati in modalità di scalabilità automatica offrono i vantaggi seguenti:

* **Semplice:** I contenitori in modalità di scalabilità automatica tolgono la complessità per gestire manualmente la velocità effettiva con provisioning (UR) e la capacità per diversi contenitori.

* **Scalabile:** I contenitori in modalità di scalabilità automatica ridimensionano la capacità di velocità effettiva con provisioning in base alle esigenze. Non vi sono rotture per le connessioni client, le applicazioni e non influiscano sui contratti di contratto esistenti.

* **Conveniente:** Quando si usano i contenitori configurati in modalità di scalabilità automatica, si pagano solo le risorse necessarie per i carichi di lavoro su base oraria.

* **Disponibilità elevata:** I contenitori in modalità di scalabilità automatica usano lo stesso back-end distribuito a livello globale, a tolleranza di errore e a disponibilità elevata per garantire la durabilità e la disponibilità elevata dei dati.

## <a name="use-cases-of-autoscale-mode"></a><a id="autoscale-usecases"></a>Casi di utilizzo della modalità di scalabilità automatica

I casi d'uso per i contenitori di Azure Cosmos configurati in modalità di scalabilità automatica includono:

* **Carichi di lavoro variabili:** Quando si esegue un'applicazione di uso leggero con utilizzo massimo di 1 ora per diverse ore ogni giorno o più volte all'anno. Gli esempi includono le applicazioni per le risorse umane, il budget e la creazione di report operativi. Per questi scenari, è possibile usare i contenitori configurati in modalità di scalabilità automatica e non è più necessario eseguire manualmente il provisioning per una capacità massima o media.

* **Carichi di lavoro imprevedibili:** Quando si eseguono i carichi di lavoro in cui è presente un utilizzo del database durante il giorno, ma anche picchi di attività difficili da stimare. Un esempio include un sito di traffico che rileva un picco di attività quando le previsioni meteorologiche cambiano. I contenitori configurati in modalità di scalabilità automatica regolano la capacità per soddisfare le esigenze del carico massimo dell'applicazione e la scalabilità verso il basso quando si verifica il sovraccarico dell'attività.

* **Nuove applicazioni:** Se si distribuisce una nuova applicazione e non si è certi della velocità effettiva con provisioning (ad esempio, quante UR) è necessario. Con i contenitori configurati in modalità di scalabilità automatica, è possibile ridimensionare automaticamente le esigenze e i requisiti di capacità dell'applicazione.

* **Applicazioni usate raramente:** Se si dispone di un'applicazione che viene utilizzata solo per alcune ore diverse volte al giorno o alla settimana o al mese, ad esempio un sito Web/Blog/applicazione a volume ridotto.

* **Database di sviluppo e test:** Se gli sviluppatori usano contenitori durante le ore lavorative, ma non sono necessari nelle notti o nei fine settimana. Con i contenitori configurati in modalità di scalabilità automatica, si riduce al minimo quando non è in uso.

* **Carichi di lavoro/query di produzione pianificati:** Quando si dispone di una serie di richieste/operazioni/query pianificate su un singolo contenitore e in caso di periodi di inattività in cui si desidera eseguire con una velocità effettiva bassa assoluta, è ora possibile eseguire questa operazione in modo semplice. Quando una query o una richiesta pianificata viene inviata a un contenitore configurato in modalità di ridimensionamento automatico, il ridimensionamento viene eseguito automaticamente in base alle esigenze ed è necessario eseguire l'operazione.

Le soluzioni ai problemi precedenti non solo richiedono una quantità di tempo molto elevata nell'implementazione, ma introducono anche complessità nella configurazione o nel codice e spesso richiedono interventi manuali per risolverli. La modalità di scalabilità automatica consente di usare gli scenari precedenti, in modo che non sia più necessario preoccuparsi di questi problemi.

## <a name="comparison--containers-configured-in-manual-mode-vs-autoscale-mode"></a>Confronto: contenitori configurati in modalità manuale rispetto alla modalità di scalabilità automatica

|  | Contenitori configurati in modalità manuale  | Contenitori configurati in modalità di scalabilità automatica |
|---------|---------|---------|
| **Velocità effettiva con provisioning** | Provisioning manuale. | Ridimensionato automaticamente e istantaneamente in base ai modelli di utilizzo del carico di lavoro. |
| **Limitazione della frequenza delle richieste/operazioni (429)**  | Potrebbe verificarsi se il consumo supera la capacità sottoposta a provisioning. | Non si verificherà se la velocità effettiva utilizzata rientra nella velocità effettiva massima scelta con la modalità di ridimensionamento automatico.   |
| **Pianificazione della capacità** |  È necessario eseguire una pianificazione iniziale della capacità e il provisioning della velocità effettiva necessaria. |    Non è necessario preoccuparsi della pianificazione della capacità. Il sistema occupa automaticamente la pianificazione della capacità e la gestione della capacità. |
| **Prezzi** | UR/sec di cui è stato effettuato manualmente il provisioning all'ora. | Per gli account con singola area di scrittura, si paga per la velocità effettiva usata su base oraria, usando la tariffa di ridimensionamento automatico ur/sec per ora. <br/><br/>Per gli account con più aree di scrittura, non sono previsti costi aggiuntivi per la scalabilità automatica. Paghi per la velocità effettiva usata su base oraria usando la stessa tariffa per più master ur/sec per ogni ora. |
| **Ideale per i tipi di carico di lavoro** |  Carichi di lavoro prevedibili e stabili|   Carichi di lavoro imprevedibili e variabili  |

## <a name="create-a-database-or-a-container-with-autoscale-mode"></a><a id="create-db-container-autoscale"></a>Creare un database o un contenitore con la modalità di scalabilità automatica

È possibile configurare la scalabilità automatica per nuovi database o contenitori durante la creazione tramite la portale di Azure. Usare la procedura seguente per creare un nuovo database o contenitore, abilitare la scalabilità automatica e specificare la velocità effettiva massima (UR/sec).

1. Accedere al [portale di Azure](https://portal.azure.com) o al [Azure Cosmos DB Explorer.](https://cosmos.azure.com/)

1. Passare all'account di Azure Cosmos DB e aprire la scheda **Esplora dati** .

1. Selezionare **nuovo contenitore.** Immettere un nome per il database, il contenitore e una chiave di partizione. In **velocità effettiva**selezionare l'opzione di **scalabilità** automatica e scegliere la velocità effettiva massima (UR/sec) che il database o il contenitore non può superare quando si usa l'opzione di scalabilità automatica.

   ![Creazione di un contenitore e configurazione della velocità effettiva di scalabilità automatica](./media/provision-throughput-autoscale/create-container-autoscale-mode.png)

1. Selezionare **OK**.

È possibile creare un database con velocità effettiva condivisa con la modalità di scalabilità automatica selezionando l'opzione **provisioning database throughput** .

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a>Limiti di archiviazione e velocità effettiva per la scalabilità automatica

La tabella seguente illustra il numero massimo di limiti di archiviazione e per le diverse opzioni in modalità di scalabilità automatica:

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
