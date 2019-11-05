---
title: Creare contenitori e database di Azure Cosmos con la velocità effettiva in modalità Autopilot.
description: Informazioni sui vantaggi, sui casi d'uso e su come eseguire il provisioning di database e contenitori di Azure Cosmos in modalità Autopilot.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 598dc6394e8be8b3372f4ed61a522454830a22d6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512275"
---
# <a name="create-azure-cosmos-containers-and-databases-with-provisioned-throughput-in-autopilot-mode-preview"></a>Creare contenitori e database di Azure Cosmos con la velocità effettiva con provisioning in modalità Autopilot (anteprima)

Azure Cosmos DB consente di effettuare il provisioning della velocità effettiva nei contenitori in modalità manuale o automatico. Questo articolo descrive i vantaggi e i casi d'uso della modalità Autopilot.

> [!NOTE]
> La modalità Autopilot è attualmente disponibile in anteprima pubblica.

Oltre al provisioning manuale della velocità effettiva, è ora possibile configurare i contenitori di Azure Cosmos in modalità Autopilot. I contenitori e i database di Azure Cosmos configurati in modalità Autopilot consentono di **ridimensionare automaticamente e immediatamente la velocità effettiva con provisioning in base alle esigenze dell'applicazione senza compromettere i contratti** di contratto.

Non è più necessario gestire manualmente la velocità effettiva con provisioning o gestire i problemi di limitazione della frequenza. I contenitori di Azure Cosmos configurati in modalità Autopilot possono essere ridimensionati immediatamente in risposta al carico di lavoro senza alcun effetto sulla disponibilità, sulla latenza, sulla velocità effettiva o sulle prestazioni del carico di lavoro a livello globale. Con un utilizzo elevato, i contenitori di Azure Cosmos configurati in modalità Autopilot possono essere aumentati o ridotti senza compromettere le operazioni in corso.

Quando si configurano i contenitori e i database in modalità Autopilot, è necessario specificare la velocità effettiva massima `Tmax` non essere superata. I contenitori possono quindi essere ridimensionati immediatamente in base alle esigenze del carico di lavoro entro l'intervallo di `0.1*Tmax < T < Tmax`. In altre parole, i contenitori e i database si ridimensionano immediatamente in base alle esigenze del carico di lavoro, dal minimo del 10% del valore della velocità effettiva configurato, fino al valore massimo configurato specificato. È possibile modificare l'impostazione di velocità effettiva massima (tmax) per il database o il contenitore Autopilot in qualsiasi momento.

## <a name="benefits-of-autopilot-mode"></a>Vantaggi della modalità Autopilot

I contenitori di Azure Cosmos configurati in modalità Autopilot offrono i vantaggi seguenti:

* **Semplice:** I contenitori in modalità Autopilot eliminano la complessità per la gestione manuale della velocità effettiva con provisioning (UR) e della capacità per diversi contenitori.

* **Scalabile:** I contenitori in modalità Autopilot ridimensionano la capacità di velocità effettiva con provisioning in base alle esigenze. Non vi sono rotture per le connessioni client, le applicazioni e non influiscano sui contratti di contratto esistenti.

* **Conveniente:** Quando si usano i contenitori di Azure Cosmos configurati in modalità Autopilot, si pagano solo le risorse necessarie per i carichi di lavoro su base oraria.

* **Disponibilità elevata:** I contenitori di Azure Cosmos in modalità Autopilot usano lo stesso back-end distribuito a livello globale, a tolleranza di errore e a disponibilità elevata per garantire la durabilità dei dati e la disponibilità elevata sempre.

## <a name="use-cases-of-autopilot-mode"></a>Casi d'uso della modalità Autopilot

I casi d'uso per i contenitori di Azure Cosmos configurati in modalità Autopilot includono:

* **Carichi di lavoro variabili:** Quando si esegue un'applicazione di uso leggero con utilizzo massimo di 1 ora a diverse ore ogni giorno o più volte all'anno. Gli esempi includono le applicazioni per le risorse umane, il budget e la creazione di report operativi. Per questi scenari, è possibile usare i contenitori configurati in modalità Autopilot, non è più necessario eseguire manualmente il provisioning per la capacità massima o media.

* **Carichi di lavoro imprevedibili:** Quando si eseguono i carichi di lavoro in cui è presente un utilizzo del database durante il giorno, ma anche picchi di attività difficili da stimare. Un esempio include un sito di traffico che rileva un picco di attività quando le previsioni meteorologiche cambiano. I contenitori configurati in modalità Autopilot consentono di regolare la capacità in base alle esigenze del carico massimo dell'applicazione e di ridurne la scalabilità quando si verifica il sovraccarico dell'attività.

* **Nuove applicazioni:** Se si distribuisce una nuova applicazione e non si è certi della velocità effettiva con provisioning (ad esempio, quante UR) è necessario. Con i contenitori configurati in modalità Autopilot è possibile ridimensionare automaticamente le esigenze e i requisiti di capacità dell'applicazione.

* **Applicazioni usate raramente:** Se si dispone di un'applicazione che viene utilizzata solo per alcune ore diverse volte al giorno o alla settimana o al mese, ad esempio un sito Web/Blog/applicazione a volume ridotto.

* **Database di sviluppo e test:** Gli sviluppatori usano gli account Azure Cosmos durante le ore lavorative, ma non sono necessari nelle notti o nei fine settimana. Con i contenitori configurati in modalità Autopilot, il ridimensionamento viene ridotto al minimo quando non è in uso.

* **Carichi di lavoro/query di produzione pianificati:** Quando si dispone di una serie di richieste/operazioni/query pianificate su un singolo contenitore e in caso di periodi di inattività in cui si desidera eseguire con una velocità effettiva bassa assoluta, è ora possibile eseguire questa operazione in modo semplice. Quando una query o una richiesta pianificata viene inviata a un contenitore configurato in modalità Autopilot, il ridimensionamento viene eseguito automaticamente in base alle esigenze e viene eseguita l'operazione.

Le soluzioni ai problemi precedenti non solo richiedono una quantità di tempo molto elevata nell'implementazione, ma introducono anche complessità nella configurazione o nel codice e spesso richiedono interventi manuali per risolverli. La modalità Autopilot consente di abilitare gli scenari precedenti, in modo che non sia più necessario preoccuparsi di questi problemi.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Confronto: contenitori configurati in modalità manuale rispetto alla modalità Autopilot

|  | Contenitori configurati in modalità manuale  | Contenitori configurati in modalità Autopilot |
|---------|---------|---------|
| **Velocità effettiva con provisioning** | Provisioning manuale | Ridimensionato in modo proattivo e reattivo in base ai modelli di utilizzo del carico di lavoro. |
| **Limitazione della frequenza delle richieste/operazioni (429)**  | Potrebbe verificarsi se il consumo supera la capacità sottoposta a provisioning. | Non si verificherà.  |
| **Pianificazione della capacità** |  È necessario eseguire una pianificazione iniziale della capacità e il provisioning della velocità effettiva necessaria. |    Non è necessario preoccuparsi della pianificazione della capacità. Il sistema occupa automaticamente la pianificazione della capacità e la gestione della capacità. |
| **Prezzi** | UR/sec di cui è stato effettuato manualmente il provisioning all'ora. | Per gli account con singola area di scrittura, si paga per la velocità effettiva usata su base oraria, usando la tariffa di Autopilot ur/sec per ora. <br/><br/>Per gli account con più aree di scrittura, non sono previsti costi aggiuntivi per Autopilot. Paghi per la velocità effettiva usata su base oraria usando la stessa tariffa per più master ur/sec per ogni ora. |
| **Ideale per i tipi di carico di lavoro** |  Carichi di lavoro prevedibili e stabili|   Carichi di lavoro imprevedibili e variabili  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Creazione di un database o di un contenitore con la modalità Autopilot

È possibile configurare Autopilot per i database o i contenitori durante la creazione. Usare i passaggi seguenti per un nuovo database o contenitore, abilitare Autopilot e specificare la velocità effettiva massima.

1. Accedere al [portale di Azure](https://portal.azure.com) o ad [Azure Cosmos Explorer.](https://cosmos.azure.com/)

1. Passare all'account Azure Cosmos e aprire la scheda **Esplora dati** .

1. Selezionare **nuovo database**, immettere un nome per il database. Per opzione **Autopilot** , scegliere **abilitato** e specificare la velocità effettiva massima che il database non può superare quando si utilizza l'opzione Autopilot.

   ![Creazione di un database in modalità Autopilot](./media/provision-throughput-autopilot/create-database-autopilot-mode.png)

1. Selezionare **OK**.

Con una procedura analoga, è anche possibile creare un contenitore con la velocità effettiva con provisioning in modalità Autopilot.

## <a name="next-steps"></a>Passaggi successivi

* [Partizionamento e scalabilità orizzontale in Azure Cosmos DB](partition-data.md)
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)