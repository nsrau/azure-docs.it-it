---
title: Informazioni su Azure Migrate
description: Informazioni sul servizio Azure Migrate.
ms.topic: overview
ms.date: 12/29/2019
ms.custom: mvc
ms.openlocfilehash: c336b2f95a17896edcc7eaad2b2586c6859804f9
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772194"
---
# <a name="about-azure-migrate"></a>Informazioni su Azure Migrate

Questo articolo offre una rapida panoramica del servizio Azure Migrate.

Usare Azure Migrate per eseguire la migrazione ad Azure. Azure Migrate offre un hub centralizzato per la valutazione e la migrazione ad Azure dell'infrastruttura, delle applicazioni e dei dati locali e include le funzionalità seguenti:

- **Piattaforma di migrazione unificata**: per usare un singolo portale per avviare, eseguire e tenere traccia del percorso di migrazione ad Azure.
- **Gamma di strumenti**: Vari strumenti per la valutazione e la migrazione. L'hub include Azure Migrate: Valutazione server o Azure Migrate: Server Migration. Si integra con altri servizi di Azure, oltre che con strumenti e offerte di fornitori di software indipendenti (ISV).
- **Valutazione e migrazione**: nell'hub di Azure Migrate è possibile valutare ed eseguire la migrazione degli elementi seguenti.
    - **Server**: valutazione e migrazione di server locali a macchine virtuale di Azure.
    - **Database**: valutazione e migrazione di database locali al database SQL di Azure o a Istanza gestita di database SQL di Azure.
    - **Applicazioni Web**: valutazione e migrazione di applicazioni Web locali al Servizio app di Azure con App Service Migration Assistant.
    - **Desktop virtuali**: valutazione e migrazione dell'infrastruttura VDI (Virtual Desktop Infrastructure) locale a Desktop virtuale Windows in Azure.
    - **Data**: migrazione di grandi quantità di dati ad Azure in modo rapido ed economico con prodotti Azure Data Box. 


## <a name="integrated-tools"></a>Strumenti integrati

L'hub di Azure Migrate offre gli strumenti seguenti.

**Strumento** | **Valutazione/migrazione** | **Dettagli**
--- | --- | ---
**Azure Migrate: Valutazione server** | Valutazione server | Consente di individuare e valutare le macchine virtuali VMware locali, le macchine virtuali Hyper-V e i server fisici per verificare se sono predisposti per la migrazione ad Azure.
**Azure Migrate: Migrazione server** | Migrazione server | Consente di eseguire la migrazione ad Azure di macchine virtuali VMware, macchine virtuali Hyper-V, server fisici, oltre ad altre macchine virtualizzate e macchine virtuali del cloud pubblico. 
**Database Migration Assistant (DMA)** | Consente di valutare i database di SQL Server locali per la migrazione a database SQL di Azure, Istanza gestita di database SQL di Azure o macchine virtuali di Azure che eseguono SQL Server. | DMA fornisce informazioni sui potenziali problemi che possono causare un blocco per la migrazione. Identifica le funzionalità non supportate, oltre alle nuove funzionalità di cui è possibile trarre vantaggio dopo la migrazione e consente di identificare il percorso corretto per la migrazione del database. [Altre informazioni](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)
**Servizio Migrazione del database** | Consente di eseguire la migrazione di database locali a macchine virtuali di Azure che eseguono SQL, il database SQL di Azure e Istanze gestite di database SQL di Azure. | [Altre informazioni](https://docs.microsoft.com/azure/dms/dms-overview) su Servizio Migrazione del database.
**Movere** | Valutazione server | [Altre informazioni](#movere) su Movere.
**Web App Migration Assistant** | Consente di valutare ed eseguire la migrazione di app Web locali ad Azure. |  Usare Migration Assistant per Servizio app di Azure per valutare i siti Web locali per la migrazione a Servizio app di Azure.<br/><br/> eseguire la migrazione di app Web .NET e PHP in Azure, usando Migration Assistant di Servizio app di Azure. [Altre informazioni](https://appmigration.microsoft.com/) su Migration Assistant.
**Azure Data Box** | Migrazione dei dati offline. | Usare i prodotti Azure Data Box per spostare grandi quantità di dati offline in Azure. [Altre informazioni](https://docs.microsoft.com/azure/databox/)

## <a name="isv-integration"></a>Integrazione di strumenti ISV

Azure Migrate si integra con diverse offerte ISV. 

**ISV** | **Funzionalità**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrazione server
[Cloudamize](https://www.cloudamize.com/platform) | Valutazione server
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Valutare ed eseguire la migrazione dei server
[Device 42](https://docs.device42.com/) | Valutazione server
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Valutazione VDI
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrazione server
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Valutazione server
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Valutazione server e database


## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: Valutazione server

Lo strumento Azure Migrate: Lo strumento Valutazione server consente di individuare e valutare le macchine virtuali VMware, le macchine virtuali Hyper-V e i server fisici per la migrazione ad Azure. Consente di valutare gli aspetti seguenti:

- **Idoneità per Azure:** valutare se i computer locali sono pronti per la migrazione ad Azure.
- **Definizione delle dimensioni per Azure:** dimensioni stimate delle macchine virtuali di Azure dopo la migrazione.
- **Stima dei costi di Azure:** costi stimati per l'esecuzione di server locali in Azure.
- **Visualizzazione delle dipendenze:** se si usa Valutazione server con la visualizzazione delle dipendenze, è possibile identificare in modo efficace le dipendenze tra server e modi ottimali per spostare server dipendenti in Azure.

Valutazione server usa un'appliance leggera distribuita in locale e registrata per Valutazione server.

- L'appliance viene eseguita in un server fisico o in una macchina virtuale ed è facilmente installabile usando un modello scaricato.
- L'appliance individua i computer locali, invia continuamente metadati e dati delle prestazioni dei computer ad Azure Migrate.
- L'individuazione dell'appliance è senza agente. Non è necessario installare alcun software nelle macchine virtuali individuate.
- Dopo l'individuazione, i computer individuati vengono raccolti in gruppi e viene eseguita la valutazione dei gruppi per la migrazione.


## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Strumento Migrazione server

Lo strumento Azure Migrate: lo strumento Migrazione server consente di eseguire la migrazione ad Azure di macchine virtuali VMware locali, macchine virtuali Hyper-V, server fisici, altre macchine virtualizzate e macchine virtuali del cloud pubblico. È possibile eseguire la migrazione dei computer dopo averli valutati o senza una valutazione.


## <a name="select-a-tool"></a>Selezionare uno strumento

Nell'hub di Azure Migrate selezionare lo strumento da usare per la valutazione e aggiungerlo a un progetto Azure Migrate. Se si aggiunge uno strumento ISV o Movere:

- Per iniziare, ottenere una licenza o iscriversi per ricevere una versione di valutazione gratuita, in base alle istruzioni dello strumento. Le licenze degli strumenti sono determinate dall'ISV o dallo strumento. 
- In ogni strumento è disponibile un'opzione per connettersi ad Azure Migrate. Seguire le istruzioni per connettersi.
- È possibile tenere traccia del percorso di migrazione dall'interno del progetto Azure Migrate con tutti gli strumenti.


## <a name="movere"></a>Movere

Movere è una piattaforma SaaS che incrementa la business intelligence presentando accuratamente interi ambienti IT nell'arco di una sola giornata. Con la crescita, il cambiamento e l'ottimizzazione delle organizzazioni per la trasformazione digitale, la soluzione offre alle aziende la fiducia necessaria per avere visibilità e controllo degli ambienti, indipendentemente dalla piattaforma, dall'applicazione o dall'area geografica. Movere è stata [acquisita](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) da Microsoft e non viene più venduta come offerta autonoma.  Movere è disponibile tramite i programmi Microsoft Solution Assessment e Cloud Economics. [Altre informazioni](https://www.movere.io) su Movere. In caso di domande, inviarle a movereq@microsoft.com o contattare il rappresentante Microsoft.

Si consiglia inoltre di esaminare Azure Migrate, il servizio di migrazione predefinito di Microsoft. Azure Migrate offre un hub centrale per semplificare la migrazione al cloud. L'hub include il supporto completo per carichi di lavoro diversi, tra cui server fisici e virtuali, database e applicazioni. La visibilità end-to-end consente di tenere traccia facilmente dello stato di avanzamento durante le fasi di individuazione, valutazione e migrazione. Con gli strumenti predefiniti di Azure e dei partner ISV, Azure Migrate offre anche una vasta gamma di funzionalità, tra cui l'individuazione di server fisici e virtuali, il dimensionamento corretto basato sulle prestazioni, la pianificazione dei costi, le valutazioni basate sull'importazione e l'analisi delle dipendenze tra applicazioni senza agente. Se si vuole assistenza qualificata per iniziare, Microsoft propone diversi [provider di servizi gestiti esperti di Azure](https://azure.microsoft.com/partners) a cui affidarsi durante il percorso. Visitare il [sito Web di Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Versioni di Azure Migrate

Sono disponibili due versioni del servizio Azure Migrate:

- **Versione corrente**: usare questa versione per creare progetti di Azure Migrate, individuare i computer locali e orchestrare valutazioni e migrazioni. [Vedere altre informazioni](whats-new.md) sulle novità di questa versione.
- **Versione precedente**: se è stata usata la versione precedente di Azure Migrate (che supporta solo la valutazione delle macchine virtuali VMware locali), è ora consigliabile passare alla versione corrente. Non è più possibile creare progetti di Azure Migrate usando la versione precedente e si consiglia di non eseguire nuove individuazioni. Per accedere ai progetti esistenti, nel portale di Azure cercare e selezionare **Azure Migrate**. Nel dashboard di **Azure Migrate** sono disponibili una notifica e un collegamento per accedere ai progetti di Azure Migrate precedenti.



## <a name="next-steps"></a>Passaggi successivi

- Provare le esercitazioni per valutare le [macchine virtuali VMware](tutorial-prepare-vmware.md), le [macchine virtuali Hyper-V](tutorial-prepare-hyper-v.md) e i [server fisici](tutorial-prepare-physical.md).
- [Vedere le domande frequenti](resources-faq.md) su Azure Migrate.
