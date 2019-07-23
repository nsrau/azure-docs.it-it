---
title: Informazioni su Azure Migrate | Microsoft Docs
description: Panoramica del servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5409ed799454a6bb64077ee884065fc518556142
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227818"
---
# <a name="about-azure-migrate"></a>Informazioni su Azure Migrate

Questo articolo offre una rapida panoramica di Azure Migrate.

Azure Migrate facilita la migrazione ad Azure. Azure Migrate offre un hub centralizzato per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure dell'infrastruttura, delle applicazioni e dei dati locali. L'hub fornisce gli strumenti di Azure per la valutazione e la migrazione, oltre a offerte di fornitori di software indipendenti (ISV) di terze parti. Fornisce:

- **Piattaforma di migrazione unificata**: per usare un singolo portale per avviare, eseguire e tenere traccia del percorso di migrazione ad Azure.
- **Gamma di strumenti**: Azure Migrate offre strumenti nativi e si integra con altri servizi di Azure, oltre che con strumenti ISV. Selezionare gli strumenti appropriati per la valutazione e la migrazione, in base ai requisiti dell'organizzazione. 
- **Valutazione server di Azure Migrate**: usare lo strumento Valutazione server per valutare le macchine virtuali VMware e le macchine virtuali Hyper-V locali per la migrazione ad Azure.
- **Migrazione server di Azure Migrate**: usare lo strumento Migrazione server per eseguire la migrazione ad Azure di macchine virtuali VMware locali, macchine virtuali Hyper-V, macchine virtuali nel cloud e server fisici.
- **Valutazione dei database di Azure Migrate**: valutare i database locali per la migrazione ad Azure.
- **Migrazione di database di Azure Migrate**: eseguire la migrazione di database locali ad Azure.


## <a name="azure-migrate-versions"></a>Versioni di Azure Migrate

Sono disponibili due versioni del servizio Azure Migrate:

- **Versione corrente**: usare questa versione per creare progetti di Azure Migrate, individuare i computer locali e orchestrare valutazioni e migrazioni. [Vedere altre informazioni](whats-new.md) sulle novità di questa versione.
- **Versione precedente**: se si usa la versione precedente di Azure Migrate (è supportata solo la valutazione delle macchine virtuali VMware locali), è ora necessario usare la versione corrente. Non è più possibile creare progetti di Azure Migrate usando la versione precedente e si consiglia di non eseguire nuove individuazioni. Per accedere ai progetti esistenti, nel portale di Azure cercare **Azure Migrate** in **Tutti i servizi**. Nel dashboard di Azure Migrate sono disponibili una notifica e un collegamento per accedere ai progetti di Azure Migrate precedenti.

## <a name="isv-integration"></a>Integrazione di strumenti ISV

Oltre agli strumenti nativi di Azure, Azure Migrate si integra con diverse offerte ISV. È sufficiente individuare lo strumento necessario e aggiungerlo a un progetto di Azure Migrate. È possibile tenere traccia in modo centralizzato del percorso di migrazione dall'interno del progetto di Azure Migrate, con strumenti di Azure e ISV.

**ISV** | **Funzionalità**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Valutare
[Device 42](https://docs.device42.com/) | Valutare
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Valutare
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Valutare
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Valutazione e migrazione
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrazione

### <a name="selecting-an-isv-tool"></a>Selezione di uno strumento ISV

Dopo aver aggiunto uno strumento ISV a un progetto di Azure Migrate, per iniziare a usarlo ottenere la licenza o iscriversi per una versione di valutazione gratuita, in base ai requisiti dell'ISV. In ogni strumento è disponibile un'opzione per connettersi ad Azure Migrate. Per connettere lo strumento ad Azure Migrate, seguire le istruzioni e la documentazione per lo strumento.

## <a name="azure-migrate-server-assessment"></a>Valutazione server di Azure Migrate

Valutazione server di Azure Migrate consente di individuare e valutare le macchine virtuali VMware e le macchine virtuali Hyper-V locali per la migrazione ad Azure. Consente di valutare gli aspetti seguenti:

- **Idoneità per Azure:** valutare se i computer locali sono pronti per la migrazione ad Azure.
- **Definizione delle dimensioni per Azure:** stimare le dimensioni delle macchine virtuali di Azure dopo la migrazione.
- **Stima dei costi di Azure:** ottenere una stima dei costi per l'esecuzione di server locali in Azure.
- **Visualizzazione delle dipendenze:** identificare le dipendenze tra server e il modo migliore per spostare i server dipendenti in Azure. 

Valutazione server usa un'appliance leggera distribuita in locale e registrata per Valutazione server.

- L'appliance individua i computer locali, si connette a Valutazione server e invia continuamente metadati e dati correlati alle prestazioni ad Azure Migrate.
- L'individuazione è senza agente. Non è necessario installare alcun componente nelle macchine virtuali individuate.
- Dopo l'individuazione dei computer, è possibile raccoglierli in gruppi che in genere sono costituiti da macchine virtuali di cui si vuole eseguire la migrazione insieme.
- Creare una valutazione per un gruppo. È quindi possibile analizzare la valutazione per definire la strategia di migrazione.

## <a name="azure-migrate-server-migration"></a>Migrazione server di Azure Migrate

Il servizio Migrazione server di Azure Migrate consente di eseguire la migrazione ad Azure di macchine virtuali VMware locali, macchine virtuali Hyper-V, server fisici, altre macchine virtualizzate e macchine virtuali del cloud pubblico. È possibile eseguire la migrazione dei computer dopo averli valutati o senza una valutazione. 

## <a name="azure-migrate-database-assessment"></a>Valutazione dei database di Azure Migrate

Azure Migrate si integra con Data Migration Assistant (DMA) per valutare i database di SQL Server locali per la migrazione a database SQL di Azure, Istanza gestita di database SQL di Azure o macchine virtuali di Azure che eseguono SQL Server. DMA fornisce informazioni sui potenziali problemi che possono causare un blocco per la migrazione. Identifica le funzionalità non supportate, oltre alle nuove funzionalità di cui è possibile trarre vantaggio dopo la migrazione e consente di identificare il percorso corretto per la migrazione del database. [Altre informazioni](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)


## <a name="azure-migrate-database-migration"></a>Migrazione di database di Azure Migrate

Azure Migrate si integra con Servizio Migrazione del database di Azure per eseguire la migrazione dei database locali ad Azure. Usare Servizio Migrazione del database per eseguire la migrazione di database locali a macchine virtuali di Azure che eseguono SQL, il database SQL di Azure e istanze gestite di database SQL di Azure. [Altre informazioni](https://docs.microsoft.com/azure/dms/dms-overview)

## <a name="web-app-assessment-and-migration"></a>Valutazione e migrazione delle app Web

Dall'hub Azure Migrate è possibile valutare ed eseguire la migrazione di app Web locali in Azure.

- **Valutazione delle app Web online**: usare Migration Assistant per Servizio app di Azure per valutare i siti Web locali per la migrazione a Servizio app di Azure.
- **Eseguire la migrazione di app Web**: eseguire la migrazione di app Web .NET e PHP in Azure, usando Migration Assistant di Servizio app di Azure.

[Altre informazioni.](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>Migrazione dei dati offline

È possibile usare la famiglia di prodotti Data Box offline per spostare grandi quantità di dati in Azure. [Altre informazioni](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Passaggi successivi

- Provare le esercitazioni per valutare le [macchine virtuali VMware](tutorial-assess-vmware.md) e le [macchine virtuali Hyper-V](tutorial-assess-hyper-v.md).
- Vedere [altre informazioni](https://azure.microsoft.com/pricing/details/azure-migrate/) sui prezzi di Azure Migrate.
- [Vedere le domande frequenti](resources-faq.md) su Azure Migrate.
