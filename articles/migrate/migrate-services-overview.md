---
title: Informazioni su Azure Migrate | Microsoft Docs
description: Panoramica del servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 10/22/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: def0832898170e0a278c403349eab1bd89050a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498746"
---
# <a name="about-azure-migrate"></a>Informazioni su Azure Migrate

Questo articolo offre una rapida panoramica di Azure Migrate.

Azure Migrate consente di eseguire la migrazione dall'ambiente locale ad Azure. Azure Migrate offre un hub centralizzato per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure dell'infrastruttura, delle applicazioni e dei dati locali. L'hub fornisce gli strumenti e i servizi di Azure per la valutazione e la migrazione, oltre a offerte di fornitori di software indipendenti (ISV) di terze parti. Azure Migrate offre:

- **Piattaforma di migrazione unificata**: per usare un singolo portale per avviare, eseguire e tenere traccia del percorso di migrazione ad Azure.
- **Gamma di strumenti**: strumenti nativi e integrazione con altri servizi di Azure, oltre che con strumenti ISV. Selezionare gli strumenti appropriati per la valutazione e la migrazione, in base ai requisiti dell'organizzazione.
- **Carichi di lavoro**: Azure Migrate fornisce strumenti di valutazione e migrazione per:
    - **Server**: usare gli strumenti Microsoft o ISV per la valutazione e la migrazione dei server alle macchine virtuali di Azure.
    - **Database**: usare gli strumenti Microsoft e ISV per la valutazione e la migrazione dei database locali al database SQL di Azure o all'Istanza gestita di database SQL di Azure.
    - **Applicazioni Web**: usare Migration Assistant per Servizio app di Azure per valutare ed eseguire la migrazione delle applicazioni Web locali al Servizio app di Azure.
    - **Desktop virtuali**: usare gli strumenti ISV per valutare ed eseguire la migrazione dell'infrastruttura VDI (Virtual Desktop Infrastructure) locale a Desktop virtuale Windows in Azure.
    - **Dati**: usare la famiglia di prodotti Azure Data Box per eseguire la migrazione dei dati ad Azure in modo veloce ed economico.

## <a name="azure-migrate-versions"></a>Versioni di Azure Migrate

Sono attualmente disponibili due versioni del servizio Azure Migrate:

- **Versione corrente**: usare questa versione per creare progetti di Azure Migrate, individuare i computer locali e orchestrare valutazioni e migrazioni. [Vedere altre informazioni](whats-new.md) sulle novità di questa versione.
- **Versione precedente**: se è stata usata la versione precedente di Azure Migrate (che supporta solo la valutazione delle macchine virtuali VMware locali), è ora consigliabile passare alla versione corrente. Non è più possibile creare progetti di Azure Migrate usando la versione precedente e si consiglia di non eseguire nuove individuazioni. Per accedere ai progetti esistenti, nel portale di Azure cercare **Azure Migrate** in **Tutti i servizi**. Nel dashboard di Azure Migrate sono disponibili una notifica e un collegamento per accedere ai progetti di Azure Migrate precedenti.

## <a name="isv-integration"></a>Integrazione di strumenti ISV

Oltre agli strumenti nativi di Microsoft, Azure Migrate si integra con diverse offerte ISV. 

**ISV** | **Funzionalità**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Valutare
[Device 42](https://docs.device42.com/) | Valutare
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Valutare
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Valutare
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Valutazione e migrazione
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrazione
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Valutare



### <a name="selecting-an-isv-tool"></a>Selezione di uno strumento ISV

È sufficiente individuare lo strumento necessario e aggiungerlo a un progetto di Azure Migrate.

- Dopo aver aggiunto uno strumento ISV, per iniziare a usarlo è necessario ottenere la licenza o iscriversi per una versione di valutazione gratuita, in base ai requisiti dell'ISV. Le licenze per gli strumenti ISV sono conformi al modello di licenza ISV.
- In ogni strumento è disponibile un'opzione per connettersi ad Azure Migrate. Per connettere lo strumento ad Azure Migrate, seguire le istruzioni e la documentazione per lo strumento.
- È possibile tenere traccia in modo centralizzato del percorso di migrazione dall'interno del progetto di Azure Migrate, con strumenti di Azure e ISV.


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


## <a name="database-assessment"></a>Valutazione del database

Azure Migrate si integra con Microsoft Data Migration Assistant (DMA) per valutare i database SQL Server locali per la migrazione a database SQL di Azure, Istanza gestita di database SQL di Azure o macchine virtuali di Azure che eseguono SQL Server. DMA fornisce informazioni sui potenziali problemi che possono causare un blocco per la migrazione. Identifica le funzionalità non supportate, oltre alle nuove funzionalità di cui è possibile trarre vantaggio dopo la migrazione e consente di identificare il percorso corretto per la migrazione del database. [Altre informazioni](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)

## <a name="database-migration"></a>Migrazione di database

Azure Migrate si integra con Servizio Migrazione del database di Azure per eseguire la migrazione dei database locali ad Azure. Usare Servizio Migrazione del database per eseguire la migrazione di database locali a macchine virtuali di Azure che eseguono SQL, il database SQL di Azure e istanze gestite di database SQL di Azure. [Altre informazioni](https://docs.microsoft.com/azure/dms/dms-overview)

## <a name="web-app-migration"></a>Migrazione di app Web

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
