---
title: Novità di Azure Migrate
description: Informazioni sulle novità e gli aggiornamenti recenti del servizio Azure Migrate.
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: a46519d434061ebfdedbabe70a3e044cb69ca552
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322224"
---
# <a name="whats-new-in-azure-migrate"></a>Novità di Azure Migrate

[Azure Migrate](migrate-services-overview.md) consente di individuare, valutare ed eseguire la migrazione di server locali, app e dati nel cloud Microsoft Azure. Questo articolo riepiloga le nuove versioni e funzionalità di Azure Migrate.
## <a name="update-september-2020"></a>Aggiornamento (settembre 2020)
- Azure Migrate consente ora di eseguire la migrazione dei server a zone di disponibilità.
- Azure Migrate consente ora di eseguire la migrazione di macchine virtuali basate su UEFI e server fisici alle macchine virtuali di Azure di seconda generazione. 

## <a name="update-august-2020"></a>Aggiornamento (agosto 2020)

- Esperienza di onboarding migliorata in cui una chiave del progetto di Azure Migrate viene generata dal portale e usata per completare la registrazione dell'appliance.
- Opzione per scaricare i file OVA/VHD o gli script del programma di installazione dal portale per configurare rispettivamente le appliance VMware e Hyper-V.
- Gestione configurazione dell'appliance aggiornata con esperienza utente migliorata.
- Supporto di più credenziali per l'individuazione di macchine virtuali Hyper-V.
- Miglioramento delle funzionalità di ricerca, ordinamento e filtro per le credenziali e le origini di individuazione aggiunte.
- Opzioni utente per l'inserimento di singoli elementi, l'inserimento di più elementi e l'importazione in formato CSV per aggiungere origini di individuazione per server fisici e host/cluster Hyper-V.
- Esperienza di errore migliorata con aggiornamenti di stato per le operazioni di convalida e individuazione su ogni origine aggiunta nella tabella. 

## <a name="update-june-2020"></a>Aggiornamento (giugno 2020)

- Sono ora supportate le valutazioni per la migrazione di macchine virtuali VMware locali alla [soluzione Azure VMware](https://go.microsoft.com/fwlink/?linkid=2132637). [Altre informazioni](how-to-create-azure-vmware-solution-assessment.md)
- Supporto per più credenziali nell'appliance per l'individuazione di server fisici.
- Supporto per consentire l'accesso di Azure dall'appliance per il tenant in cui è stata configurata la restrizione.


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
