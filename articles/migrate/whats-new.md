---
title: Novità di Azure Migrate
description: Informazioni sulle novità e gli aggiornamenti recenti del servizio Azure Migrate.
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: be617590ce0c96c3ec8ea705732534c8395ac0ce
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052321"
---
# <a name="whats-new-in-azure-migrate"></a>Novità di Azure Migrate

[Azure Migrate](migrate-services-overview.md) consente di individuare, valutare ed eseguire la migrazione di server locali, app e dati nel cloud Microsoft Azure. Questo articolo riepiloga le nuove versioni e funzionalità di Azure Migrate.

## <a name="update-june-2020"></a>Aggiornamento (giugno 2020)

- Supporto per più credenziali nell'appliance per l'individuazione di server fisici.
- Supporto per consentire l'accesso di Azure dall'appliance per il tenant in cui è stata configurata la restrizione.
- Nuove versioni dell'appliance Azure Migrate elencate di seguito.

### <a name="azure-public-cloud"></a>Cloud pubblico di Azure

**Scenario** | **Scaricare** | **SHA256**
--- | --- | ---
Hyper-V (8,93 GB) | [Versione più recente](https://aka.ms/migrate/appliance/hyperv) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1
VMware (10,9 GB) | [Versione più recente](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6ce7402b79f234bc0fe69663dd
Dispositivi fisici (63,1 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0


### <a name="azure-government"></a>Azure Government

**Scenario** | **Scaricare** | **SHA256**
--- | --- | ---
Hyper-V (63,1 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae468934408e43ab55ff397b7da200b92121972e683f9aa3
VMware (63,1 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de
Dispositivi fisici (63,1 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1



## <a name="update-april-2020"></a>Aggiornamento (aprile 2020)

Azure Migrate supporta le distribuzioni in Azure per enti pubblici. 

- È possibile individuare e valutare macchine virtuali VMware, macchine virtuali Hyper-V e server fisici.
- È possibile eseguire la migrazione di macchine virtuali VMware, macchine virtuali Hyper-V e server fisici in Azure.
- Per la migrazione di virtuali VMware, è possibile usare la migrazione senza agente o basata su agente. [Altre informazioni](server-migrate-overview.md)
- [Controllare](migrate-support-matrix.md#supported-geographies-azure-government) le aree geografiche e le aree supportate per Azure per enti pubblici.
- [L'analisi delle dipendenze basate su agenti](concepts-dependency-visualization.md#agent-based-analysis) non è supportata in Azure per enti pubblici.
- Le funzionalità in anteprima sono supportate in Azure per enti pubblici, in particolare [analisi delle dipendenze senza agenti](concepts-dependency-visualization.md#agentless-analysis)e [individuazione delle applicazioni](how-to-discover-applications.md).


## <a name="update-march-2020"></a>Aggiornamento (marzo 2020)

È ora disponibile un'installazione basata su script per configurare l'[appliance di Azure Migrate](migrate-appliance.md):

- L'installazione basata su script è un'alternativa all'installazione tramite OVA (VMware)/VHD (Hyper-V) dell'appliance.
- Fornisce uno script del programma di installazione di PowerShell che può essere usato per configurare l'appliance per VMware/Hyper-V in un computer esistente che esegue Windows Server 2016.

## <a name="update-november-2019"></a>Aggiornamento (novembre 2019)

Sono state aggiunte alcune nuove funzionalità in Azure Migrate:

- **Valutazione dei server fisici**: è ora supportata la valutazione dei server fisici locali, oltre alla migrazione dei server fisici già supportata.
- **Valutazione basata sull'importazione**: è ora supportata la valutazione dei computer tramite metadati e dati sulle prestazioni disponibili in un file CSV.
- **Individuazione delle applicazioni**: Azure Migrate supporta ora l'individuazione a livello di applicazione di app, ruoli e funzionalità con l'appliance di Azure Migrate. Questa funzionalità è attualmente supportata solo per le macchine virtuali VMware ed è limitata all'individuazione, mentre la valutazione non è supportata al momento. [Altre informazioni](how-to-discover-applications.md)
- **Visualizzazione delle dipendenze senza agente**: non è più necessario installare in modo esplicito agenti per la visualizzazione delle dipendenze. Sono ora supportate sia le funzionalità senza agente che quelle basate su agenti.
- **Desktop virtuale**: usare gli strumenti ISV per valutare ed eseguire la migrazione dell'infrastruttura VDI (Virtual Desktop Infrastructure) locale a Desktop virtuale Windows in Azure.
- **App Web**: Migration Assistant di Servizio app di Azure, usato per la valutazione e la migrazione di app Web, è ora integrato in Azure Migrate.

In Azure Migrate sono stati aggiunti nuovi strumenti di valutazione e migrazione:

- **Rackware**: per la migrazione al cloud.
- **Movere**: per la valutazione.

[Altre informazioni](migrate-services-overview.md) sull'uso di strumenti e offerte ISV per la valutazione e la migrazione in Azure Migrate.

## <a name="azure-migrate-current-version"></a>Versione corrente di Azure Migrate

La versione corrente di Azure Migrate (rilasciata a luglio 2019) offre numerose nuove funzionalità:

- **Piattaforma di migrazione unificata**: Azure Migrate offre ora un unico portale per centralizzare, gestire e tenere traccia del percorso di migrazione ad Azure, con miglioramenti per il flusso di distribuzione e l'esperienza del portale.
- **Strumenti di valutazione e migrazione**: Azure Migrate offre strumenti nativi e si integra con altri servizi di Azure, oltre che con strumenti di fornitori di software indipendenti (ISV). [Vedere altre informazioni](migrate-services-overview.md#isv-integration) sull'integrazione ISV.
- **Valutazione di Azure Migrate**: lo strumento Valutazione server di Azure Migrate consente di valutare le macchine virtuali VMware e le macchine virtuali Hyper-V per la migrazione ad Azure. È anche possibile valutare la migrazione usando altri servizi di Azure e strumenti ISV.
- **Migrazione con Azure Migrate**: lo strumento Migrazione server di Azure Migrate consente di eseguire la migrazione ad Azure di macchine virtuali VMware locali, macchine virtuali Hyper-V, server fisici, altri server virtualizzati e macchine virtuali del cloud privato/pubblico. È anche possibile eseguire la migrazione ad Azure usando strumenti ISV.
- **Appliance di Azure Migrate**: Azure Migrate distribuisce un'appliance leggera per l'individuazione e la valutazione delle macchine virtuali VMware e delle macchine virtuali Hyper-V locali.
    - Questa appliance viene usata dagli strumenti Valutazione server e Migrazione server di Azure Migrate per la migrazione senza agenti.
    - L'appliance individua continuamente i metadati e i dati sulle prestazioni dei server, ai fini della valutazione e della migrazione.  
- **Migrazione di macchine virtuali VMware**:  Lo strumento Migrazione server di Azure Migrate offre un paio di metodi per la migrazione di macchine virtuali VMware locali ad Azure.  Una migrazione senza agenti usando l'appliance di Azure Migrate e una migrazione basata su agente che usa un'appliance di replica e distribuisce un agente in ogni macchina virtuale di cui si vuole eseguire la migrazione. [Altre informazioni](server-migrate-overview.md)
 - **Valutazione e migrazione dei database**: da Azure Migrate è possibile valutare i database locali per la migrazione ad Azure usando Migration Assistant per il database di Azure. È possibile eseguire la migrazione di database usando Servizio Migrazione del database di Azure.
- **Migrazione di app Web**: è possibile valutare le app Web usando un URL di endpoint pubblico con Servizio app di Azure. Per la migrazione di app .NET interne, è possibile scaricare ed eseguire Migration Assistant per il servizio app.
- **Data Box**: importazione di grandi quantità di dati offline in Azure usando Azure Data Box in Azure Migrate.

## <a name="azure-migrate-previous-version"></a>Versione precedente di Azure Migrate

se si usa la versione precedente di Azure Migrate (è supportata solo la valutazione delle macchine virtuali VMware locali), è ora consigliabile passare alla versione corrente. Nella versione precedente non è più possibile creare nuovi progetti di Azure Migrate o eseguire nuove individuazioni. È ancora possibile accedere ai progetti esistenti. Per effettuare questa operazione nel portale di Azure > **Tutti i servizi**, cercare **Azure Migrate**. Tra le notifiche di Azure Migrate è disponibile un collegamento per accedere ai progetti di Azure Migrate precedenti.



## <a name="next-steps"></a>Passaggi successivi

- Vedere [altre informazioni](https://azure.microsoft.com/pricing/details/azure-migrate/) sui prezzi di Azure Migrate.
- [Vedere le domande frequenti](resources-faq.md) su Azure Migrate.
- Provare le esercitazioni per valutare le [macchine virtuali VMware](tutorial-assess-vmware.md) e le [macchine virtuali Hyper-V](tutorial-assess-hyper-v.md).
