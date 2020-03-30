---
title: Creare contenitori e database Cosmos di Azure in modalità pilota automatico.
description: Informazioni sui vantaggi, i casi d'uso e su come eseguire il provisioning di database e contenitori di Azure Cosmos in modalità pilota automatico.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246656"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Creare contenitori e database di Azure Cosmos in modalità Autopilot (anteprima)

Azure Cosmos DB consente di effettuare il provisioning della velocità effettiva nei contenitori in modalità manuale o Autopilot. Questo articolo descrive i vantaggi e i casi d'uso della modalità Autopilot.

> [!NOTE]
> La modalità Pilota automatico è attualmente disponibile nell'anteprima pubblica. È possibile abilitare il pilota automatico solo [per nuovi database e contenitori.](#create-a-database-or-a-container-with-autopilot-mode) Non è disponibile per i contenitori e i database esistenti.

Oltre al provisioning manuale della velocità effettiva, è ora possibile configurare i contenitori di Azure Cosmos in modalità pilota automatico. I contenitori e i database configurati in modalità pilota automatico scaleranno automaticamente e istantaneamente la velocità effettiva di cui è stato eseguito **il provisioning in base alle esigenze dell'applicazione senza influire sulla disponibilità, la latenza, la velocità effettiva o le prestazioni del carico di lavoro a livello globale.**

Quando si configurano contenitori e database in modalità `Tmax` pilota automatico, è necessario specificare la velocità effettiva massima da non superare. I contenitori possono quindi `0.1*Tmax < T < Tmax`ridimensionare la velocità effettiva in modo che . In altre parole, i contenitori e i database vengono scalati istantaneamente in base alle esigenze del carico di lavoro, a partire dal 10% del valore di velocità effettiva massima configurato fino al valore di velocità effettiva massima configurato. È possibile modificare l'impostazione della velocità effettiva massima (`Tmax`) in un database o in un contenitore di pilota automatico in qualsiasi momento. Con l'opzione autopilot, la velocità effettiva minima di 400 RU/s per contenitore o database non è più applicabile.

Durante l'anteprima del pilota automatico, per la velocità effettiva massima specificata nel contenitore o nel database, il sistema consente di operare entro il limite di archiviazione calcolato. Se il limite di archiviazione viene superato, la velocità effettiva massima viene regolata automaticamente su un valore superiore. Quando si utilizza la velocità effettiva a livello di database con la `0.001*TMax`modalità pilota automatico, il numero di contenitori consentiti all'interno di un database viene calcolato come: . Ad esempio, se si esegue il provisioning di 20.000 RU/s con pilota automatico, il database può avere 20 contenitori.

## <a name="benefits-of-autopilot-mode"></a>Vantaggi della modalità pilota automatico

I contenitori Cosmos di Azure configurati in modalità pilota automatico presentano i vantaggi seguenti:Azure Cosmos containers that are configured in autopilot mode have the following benefits:

* **Semplice:** I contenitori in modalità pilota automatico eliminano manualmente la complessità per gestire la velocità effettiva con provisioning e la capacità per vari contenitori.

* **Scalabile:** I contenitori in modalità pilota automatico scalano senza soluzione di continuità la capacità di produzione di cui è stato eseguito il provisioning in base alle esigenze. Non si verifica alcuna interruzione delle connessioni client, delle applicazioni e non influiscono sui contratti di servizio esistenti.

* **Conveniente:** Quando si usano contenitori configurati in modalità pilota automatico, si paga solo per le risorse necessarie per i carichi di lavoro in base all'ora.

* **Altamente disponibile:** I contenitori in modalità pilota automatico utilizzano lo stesso back-end a livello globale, a tolleranza di errore e a disponibilità elevata per garantire la durata dei dati e l'elevata disponibilità.

## <a name="use-cases-of-autopilot-mode"></a>Casi d'uso della modalità pilota automatico

I casi d'uso per i contenitori Cosmos di Azure configurati in modalità pilota automatico includono:The use cases for Azure Cosmos containers configured in autopilot mode include:

* **Carichi di lavoro variabili:Variable workloads:** Quando si esegue un'applicazione leggermente utilizzata con utilizzo di picco di 1 ora a diverse ore un paio di volte ogni giorno o più volte all'anno. Gli esempi includono le applicazioni per le risorse umane, l'impostazione del budget e la creazione di report operativi. Per tali scenari, è possibile utilizzare i contenitori configurati in modalità pilota automatico e non è più necessario eseguire manualmente il provisioning per la capacità di picco o media.

* **Carichi di lavoro imprevedibili:** Quando si eseguono carichi di lavoro in cui è presente l'utilizzo del database durante il giorno, ma anche picchi di attività difficili da prevedere. Un esempio include un sito di traffico che vede un aumento dell'attività quando le previsioni del tempo cambiano. I contenitori configurati in modalità pilota automatico regolano la capacità per soddisfare le esigenze del carico di picco dell'applicazione e la scalabilità verso il basso quando l'ondata di attività è finita.

* **Nuove applicazioni:** Se si distribuisce una nuova applicazione e non si è certi della velocità effettiva di cui è stato eseguito il provisioning, ovvero il numero di RU necessarie. Con i contenitori configurati in modalità pilota automatico, è possibile eseguire automaticamente la scalabilità in base alle esigenze di capacità e ai requisiti dell'applicazione.

* **Applicazioni usate raramente:** Se si dispone di un'applicazione che viene utilizzata solo per alcune ore più volte al giorno o alla settimana o al mese, ad esempio un sito di applicazioni/web/blog a basso volume.

* **Database di sviluppo e test:** Se si dispone di sviluppatori che utilizzano i contenitori durante l'orario di lavoro, ma non sono necessari durante la notte o nei fine settimana. Con i contenitori configurati in modalità pilota automatico, si riduce al minimo quando non sono in uso.

* **Carichi di lavoro/query di produzione pianificati:Scheduled production workloads/queries:** Quando si dispone di una serie di richieste/operazioni/query pianificate in un singolo contenitore e se sono presenti periodi di inattività in cui si desidera eseguire a una velocità effettiva bassa assoluta, è ora possibile farlo facilmente. Quando una query/richiesta pianificata viene inviata a un contenitore configurato in modalità pilota automatico, verrà automaticamente aumentato quanto necessario ed eseguire l'operazione.

Le soluzioni ai problemi precedenti non solo richiedono un'enorme quantità di tempo nell'implementazione, ma introducono anche complessità nella configurazione o nel codice e spesso richiedono un intervento manuale per risolverli. La modalità pilota automatico consente gli scenari di cui sopra fuori dalla scatola, in modo che non c'è bisogno di preoccuparsi di questi problemi più.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Confronto – Contenitori configurati in modalità manuale rispetto alla modalità pilota automatico

|  | Contenitori configurati in modalità manuale  | Contenitori configurati in modalità pilota automatico |
|---------|---------|---------|
| **Velocità effettiva di provisioning** | Provisioning manuale. | Scalabilità automatica e istantanea in base ai modelli di utilizzo del carico di lavoro. |
| **Limitare la frequenza delle richieste/operazioni (429)**  | Può accadere, se il consumo supera la capacità di cui è stato eseguito il provisioning. | Non si verifica se la velocità effettiva consumata rientra nella velocità effettiva massima scelta con la modalità di pilota automatico.   |
| **Pianificazione della capacità** |  È necessario eseguire una pianificazione iniziale della capacità e la fornitura della velocità effettiva necessaria. |    Non devi preoccuparti della pianificazione della capacità. Il sistema si occupa automaticamente della pianificazione della capacità e della gestione della capacità. |
| **Prezzi** | Aggiornamento/s all'ora con provisioning manuale. | Per i conti con singole aree di scrittura, si paga la velocità effettiva utilizzata su base oraria, utilizzando la tariffa RU/s per ora del pilota automatico. <br/><br/>Per gli account con più aree di scrittura, non è previsto alcun costo aggiuntivo per il pilota automatico. Si paga per la velocità effettiva utilizzata su base oraria utilizzando la stessa ora/RU/s multimaster/i. |
| **Ideale per i tipi di carico di lavoro** |  Carichi di lavoro prevedibili e stabili|   Carichi di lavoro imprevedibili e variabili  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Creare un database o un contenitore con la modalità pilota automaticoCreate a database or a container with autopilot mode

È possibile configurare il pilota automatico per nuovi database o contenitori durante la creazione tramite il portale di Azure.You can configure autopilot for new databases or containers when creating them through the Azure portal. Utilizzare la procedura seguente per creare un nuovo database o contenitore, abilitare il pilota automatico e specificare la velocità effettiva massima (RU/s).

1. Accedere al [portale di Azure](https://portal.azure.com) o all'esploratore di database [di Azure Cosmos.Sign](https://cosmos.azure.com/) in to the Azure portal or the Azure Cosmos DB explorer.

1. Passare all'account azure Cosmos DB e aprire la scheda **Esplora dati.**

1. Selezionare **Nuovo contenitore.** Immettere un nome per il database, il contenitore e una chiave di partizione. In **Velocità effettiva**selezionare l'opzione **Autopilot** e scegliere la velocità effettiva massima (RU/s) che il database o il contenitore non può superare quando si utilizza l'opzione autopilot.

   ![Creazione di un contenitore e configurazione della velocità effettiva di AutopilotCreating a container and configuring Autopilot throughput](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Selezionare **OK**.

È possibile creare un database della velocità effettiva condivisa con la modalità pilota automatico selezionando l'opzione Effettuare il **provisioning della velocità effettiva del database.**

## <a name="throughput-and-storage-limits-for-autopilot"></a><a id="autopilot-limits"></a>Limiti di velocità effettiva e archiviazione per il pilota automatico

La tabella seguente mostra i limiti massimi di intutto e di archiviazione per le diverse opzioni in modalità pilota automatico:

|Limite massimo di velocità effettiva  |Limite massimo di archiviazione  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20.000 RU/s  |  200 GB  |
|100.000 RU/s    |  1 TB   |
|500.000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Passaggi successivi

* Consultare le [domande frequenti sul pilota automatico](autopilot-faq.md).
* Ulteriori informazioni sulle [partizioni logiche](partition-data.md).
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
