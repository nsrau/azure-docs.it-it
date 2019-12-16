---
title: Informazioni su Azure Migrate
description: Informazioni sulla valutazione e la migrazione di server con il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 54aea9fac946713ee6c1709e952e3962bb7288e9
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951769"
---
# <a name="about-azure-migrate"></a>Informazioni su Azure Migrate

Questo articolo offre una rapida panoramica di Azure Migrate.

Azure Migrate consente di eseguire la migrazione dall'organizzazione dall'ambiente locale ad Azure. Azure Migrate offre un hub centralizzato per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure dell'infrastruttura, delle applicazioni e dei dati locali.  Azure Migrate offre:

- **Piattaforma di migrazione unificata**: per usare un singolo portale per avviare, eseguire e tenere traccia del percorso di migrazione ad Azure.
- **Gamma di strumenti**: l'hub fornisce gli strumenti di valutazione e migrazione di Azure Migrate e si integra con altri servizi di Azure, oltre che con altri strumenti e offerte di fornitori di software indipendenti (ISV).
- **Carichi di lavoro**: Azure Migrate fornisce strumenti di valutazione e migrazione per:
    - **Server**: usare i servizi Valutazione server e Migrazione server di Azure Migrate, oltre ad altri strumenti, per la valutazione e la migrazione dei server alle VM di Azure.
    - **Database**: usare gli strumenti di Microsoft e degli ISV per la valutazione e la migrazione dei database locali al database SQL di Azure o a Istanza gestita di database SQL di Azure.
    - **Applicazioni Web**: usare Migration Assistant per Servizio app di Azure per valutare ed eseguire la migrazione delle applicazioni Web locali al Servizio app di Azure.
    - **Desktop virtuali**: usare gli strumenti ISV per valutare ed eseguire la migrazione dell'infrastruttura VDI (Virtual Desktop Infrastructure) locale a Desktop virtuale Windows in Azure.
    - **Dati**: usare la famiglia di prodotti Azure Data Box per eseguire la migrazione di grandi quantità di dati ad Azure in modo rapido e a costi contenuti.

## <a name="azure-migrate-versions"></a>Versioni di Azure Migrate

Sono attualmente disponibili due versioni del servizio Azure Migrate:

- **Versione corrente**: usare questa versione per creare progetti di Azure Migrate, individuare i computer locali e orchestrare valutazioni e migrazioni. [Vedere altre informazioni](whats-new.md) sulle novità di questa versione.
- **Versione precedente**: se è stata usata la versione precedente di Azure Migrate (che supporta solo la valutazione delle macchine virtuali VMware locali), è ora consigliabile passare alla versione corrente. Non è più possibile creare progetti di Azure Migrate usando la versione precedente e si consiglia di non eseguire nuove individuazioni. Per accedere ai progetti esistenti, nel portale di Azure cercare e selezionare **Azure Migrate**. Nel dashboard di **Azure Migrate** sono disponibili una notifica e un collegamento per accedere ai progetti di Azure Migrate precedenti.



## <a name="isv-integration"></a>Integrazione di strumenti ISV

Oltre agli strumenti nativi di Azure, Azure Migrate si integra con diverse offerte ISV. 

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

## <a name="azure-tool-integration"></a>Integrazione di strumenti di Azure

La tabella contiene un riepilogo di altri strumenti integrati in Azure Migrate.

**Strumento** | **Dettagli**
--- | ---
Azure Migrate: Server Assessment | Valutazione server
Azure Migrate: Server Migration | Migrazione server
Database Migration Assistant (DMA) | Valutazione database
Servizio Migrazione del database | Eseguire la migrazione dei database
Movere | Valutazione server
Web App Migration Assistant | Valutazione e migrazione di app Web



### <a name="selecting-a-tool"></a>Selezione di uno strumento

Identificare lo strumento necessario e aggiungerlo a un progetto di Azure Migrate.

- Se si aggiunge uno strumento ISV o Movere:
    - Per iniziare, ottenere una licenza o iscriversi per ricevere una versione di valutazione gratuita, in base ai requisiti dello strumento. Le licenze per gli strumenti sono conformi al modello di licenza dell'ISV o dello strumento.
    - In ogni strumento è disponibile un'opzione per connettersi ad Azure Migrate. Per connettere lo strumento ad Azure Migrate, seguire le istruzioni e la documentazione per lo strumento.
- È possibile tenere traccia centralmente del percorso di migrazione dall'interno del progetto di Azure Migrate, con strumenti di Azure e di altro tipo.



## <a name="azure-migrate-server-assessment-tool"></a>Strumento Valutazione server di Azure Migrate

Azure Migrate: Lo strumento Valutazione server consente di individuare e valutare le macchine virtuali VMware, le macchine virtuali Hyper-V e i server fisici per la migrazione ad Azure. Consente di valutare gli aspetti seguenti:

- **Idoneità per Azure:** valutare se i computer locali sono pronti per la migrazione ad Azure.
- **Definizione delle dimensioni per Azure:** dimensioni stimate delle macchine virtuali di Azure dopo la migrazione.
- **Stima dei costi di Azure:** costi stimati per l'esecuzione di server locali in Azure.
- **Visualizzazione delle dipendenze:** dipendenze tra server (se la visualizzazione delle dipendenze è abilitata) e modi ottimali per spostare i server dipendenti in Azure.

Valutazione server usa un'appliance leggera distribuita in locale e registrata per Valutazione server.

- L'appliance individua i computer locali.
- Si connette a Valutazione server e invia continuamente metadati e dati sulle prestazioni ad Azure Migrate.
- L'individuazione dell'appliance è senza agente. Non è necessario installare alcun software nelle macchine virtuali individuate.
- Dopo l'individuazione, i computer individuati vengono raccolti in gruppi. In genere si raccolgono i computer di cui si vuole eseguire la migrazione in gruppo.
- Creare una valutazione per un gruppo. Analizzare quindi la valutazione per definire la strategia di migrazione.

## <a name="azure-migrate-server-migration-tool"></a>Strumento Migrazione server di Azure Migrate

Azure Migrate: lo strumento Migrazione server consente di eseguire la migrazione ad Azure di macchine virtuali VMware locali, macchine virtuali Hyper-V, server fisici, altre macchine virtualizzate e macchine virtuali del cloud pubblico. È possibile eseguire la migrazione dei computer dopo averli valutati o senza una valutazione.


## <a name="database-migration-assistant"></a>Database Migration Assistant

Azure Migrate si integra con Microsoft Data Migration Assistant (DMA) per valutare i database SQL Server locali per la migrazione a database SQL di Azure, Istanza gestita di database SQL di Azure o macchine virtuali di Azure che eseguono SQL Server. DMA fornisce informazioni sui potenziali problemi che possono causare un blocco per la migrazione. Identifica le funzionalità non supportate, oltre alle nuove funzionalità di cui è possibile trarre vantaggio dopo la migrazione e consente di identificare il percorso corretto per la migrazione del database. [Altre informazioni](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)

## <a name="database-migration-service"></a>Servizio Migrazione del database

Azure Migrate si integra con Servizio Migrazione del database di Azure per eseguire la migrazione dei database locali ad Azure. Usare Servizio Migrazione del database per eseguire la migrazione di database locali a macchine virtuali di Azure che eseguono SQL, il database SQL di Azure e istanze gestite di database SQL di Azure. [Altre informazioni](https://docs.microsoft.com/azure/dms/dms-overview)

## <a name="movere"></a>Movere

 
Movere è una piattaforma SaaS che incrementa la business intelligence presentando accuratamente interi ambienti IT nell'arco di una sola giornata. Con la crescita, il cambiamento e l'ottimizzazione delle organizzazioni per la trasformazione digitale, la soluzione offre alle aziende la fiducia necessaria per avere visibilità e controllo degli ambienti, indipendentemente dalla piattaforma, dall'applicazione o dall'area geografica. Movere è stata [acquisita](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) da Microsoft e non viene più venduta come offerta autonoma.  Movere è disponibile tramite i programmi Microsoft Solution Assessment e Cloud Economics. [Altre informazioni](https://www.movere.io) su Movere. In caso di domande, inviarle a movereq@microsoft.com o contattare il rappresentante Microsoft.

Si consiglia inoltre di esaminare Azure Migrate, il servizio di migrazione predefinito di Microsoft. Azure Migrate offre un hub centrale per semplificare la migrazione al cloud. L'hub include il supporto completo per carichi di lavoro diversi, tra cui server fisici e virtuali, database e applicazioni. La visibilità end-to-end consente di tenere traccia facilmente dello stato di avanzamento durante le fasi di individuazione, valutazione e migrazione. Con gli strumenti predefiniti di Azure e dei partner ISV, Azure Migrate offre anche una vasta gamma di funzionalità, tra cui l'individuazione di server fisici e virtuali, il dimensionamento corretto basato sulle prestazioni, la pianificazione dei costi, le valutazioni basate sull'importazione e l'analisi delle dipendenze tra applicazioni senza agente. Se si vuole assistenza qualificata per iniziare, Microsoft propone diversi [provider di servizi gestiti esperti di Azure](https://azure.microsoft.com/partners) a cui affidarsi durante il percorso. Visitare il [sito Web di Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="web-app-migration-assistant"></a>Web App Migration Assistant

Azure Migrate si integra con Migration Assistant del Servizio app di Azure. Dall'hub di Azure Migrate è possibile valutare ed eseguire la migrazione di app Web locali in Azure usando Migration Assistant, come illustrato di seguito:

- **Valutazione delle app Web online**: usare Migration Assistant per Servizio app di Azure per valutare i siti Web locali per la migrazione a Servizio app di Azure.
- **Eseguire la migrazione di app Web**: eseguire la migrazione di app Web .NET e PHP in Azure, usando Migration Assistant di Servizio app di Azure.

[Altre informazioni](https://appmigration.microsoft.com/) su Migration Assistant.



## <a name="offline-data-migration"></a>Migrazione dei dati offline

È possibile usare i prodotti Azure Data Box per spostare grandi quantità di dati offline in Azure. [Altre informazioni](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Passaggi successivi

- Provare le esercitazioni per valutare le [macchine virtuali VMware](tutorial-assess-vmware.md) e le [macchine virtuali Hyper-V](tutorial-assess-hyper-v.md).
- Vedere [altre informazioni](https://azure.microsoft.com/pricing/details/azure-migrate/) sui prezzi di Azure Migrate.
- [Vedere le domande frequenti](resources-faq.md) su Azure Migrate.
