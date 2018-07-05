---
title: Panoramica della migrazione Contoso ad Azure | Microsoft Docs
description: Presenta una panoramica della strategia e degli scenari usati da Contoso per eseguire la migrazione del proprio data center locale ad Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: ec0308bb2e39c3801305748f19783e70d58b0b7e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232214"
---
# <a name="contoso-migration-overview"></a>Migrazione Contoso: panoramica


Questo è il primo di una serie di articoli che illustra come l'organizzazione fittizia Contoso esegue la migrazione della propria infrastruttura locale al cloud di [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/). 

Gli articoli della serie presentano distribuzioni di esempio che consentono di comprendere e provare diverse opzioni per la migrazione ad Azure e per l'uso di un ambiente ibrido. Negli articoli viene illustrata la strategia adottata da Contoso per completare la migrazione, ma al tempo stesso vengono fornite anche nozioni di carattere più generale e istruzioni specifiche.

## <a name="introduction"></a>Introduzione

Azure offre l'accesso a un set completo di servizi cloud. Sviluppatori e professionisti IT possono usare questi servizi per creare, distribuire e gestire applicazioni in un'ampia gamma di strumenti e framework, attraverso una rete globale di data center. Il cloud di Azure consente alle aziende che affrontano le sfide associate al passaggio al digitale di determinare come ottimizzare le risorse e le operazioni, interagire con clienti e dipendenti e trasformare i prodotti offerti.

Nonostante tutti i vantaggi offerti dal cloud in termini di velocità e flessibilità, riduzione al minimo dei costi, prestazioni e affidabilità, tuttavia, Azure riconosce che molte organizzazioni dovranno eseguire data center locali ancora per un certo periodo. In risposta agli ostacoli all'adozione del cloud, Azure offre una strategia di cloud ibrido che collega i data center locali e il cloud pubblico di Azure, ad esempio usando risorse del cloud di Azure come il backup per proteggere le risorse locali oppure usando le funzionalità di analisi di Azure per ottenere informazioni significative sui carichi di lavoro locali. 

Nell'ambito della strategia di cloud ibrido, Azure offre sempre più soluzioni per eseguire la migrazione al cloud di app e carichi di lavoro locali. Con semplici procedure, è possibile valutare in modo completo le risorse locali per determinare come verranno eseguite nel cloud di Azure. Con una valutazione approfondita a disposizione, si può quindi eseguire in tutta sicurezza la migrazione delle risorse ad Azure. Quando saranno operative in Azure, le risorse potranno essere ottimizzate per mantenere e migliorare l'accesso, la flessibilità, la sicurezza e l'affidabilità.

## <a name="migration-strategies"></a>Strategie di migrazione

Le strategie per la migrazione al cloud possono essere suddivise in quattro grandi categorie: rehosting, refactoring, riprogettazione e ricostruzione. La strategia adottata dipende dai fattori chiave dello sviluppo aziendale e dagli obiettivi di migrazione prefissati. È possibile adottare più strategie. Si può ad esempio scegliere di eseguire il rehosting (lift-and-shift) di app semplici, o che non hanno particolare rilevanza per l'azienda, ma di riprogettare app più complesse e business critical. La tabella seguente illustra le possibili strategie.


**Strategia** | **Definizione** | **Quando usarla** 
--- | --- | --- 
**Rehosting** | Nota anche come migrazione in modalità "lift-and-shift". Adottando questa strategia non è necessario apportare modifiche al codice ed è possibile eseguire rapidamente la migrazione delle app esistenti ad Azure. Ogni app viene migrata così com'è, in modo da sfruttare i vantaggi del cloud senza i rischi e i costi associati alle modifiche del codice. | Quando è necessario spostare rapidamente le app nel cloud.<br/><br/> Quando si vuole spostare un'app senza modificarla.<br/><br/> Quando le app sono progettate in modo da sfruttare la scalabilità dell'[infrastruttura distribuita come servizio di Azure](https://azure.microsoft.com/overview/what-is-iaas/) dopo la migrazione.<br/><br/> Quando le app sono importanti per l'azienda, ma non è necessario apportare modifiche immediate alle loro funzionalità.
**Refactoring** | Nota anche come "riassemblaggio", la strategia di refactoring richiede modifiche minime alle app, in modo che possano connettersi alla [piattaforma distribuita come servizio di Azure](https://azure.microsoft.com/overview/what-is-paas/) e sfruttare le offerte del cloud.<br/><br/> È ad esempio possibile eseguire la migrazione delle app esistenti al Servizio app di Azure o al servizio Kubernetes di Azure.<br/><br/> Si può anche eseguire il refactoring di database relazionali e non relazionali in opzioni come Istanza gestita di database SQL di Azure, Database di Azure per MySQL, Database di Azure per PostgreSQL e Azure Cosmos DB. | Se l'app può essere facilmente riassemblata in modo da funzionare in Azure.<br/><br/> Se si vogliono applicare le procedure DevOps innovative offerte da Azure o si sta valutando l'opportunità di investire in DevOps adottando una strategia basata su contenitori per carichi di lavoro.<br/><br/> Per il refactoring, è necessario considerare la portabilità della codebase esistente e le competenze di sviluppo disponibili.
**Riprogettazione** | La riprogettazione per la migrazione è incentrata sulla modifica e sull'estensione della codebase e delle funzionalità delle app al fine di ottimizzarne l'architettura per la scalabilità nel cloud.<br/><br/> Può ad esempio essere opportuno suddividere un'applicazione monolitica in un gruppo di microservizi che funzionano insieme e sono facilmente scalabili.<br/><br/> È anche possibile riprogettare database relazionali e non relazionali e trasformarli in soluzioni DBaaS completamente gestite, come Istanza gestita di database SQL di Azure, Database di Azure per MySQL, Database di Azure per PostgreSQL e Azure Cosmos DB. | Quando si devono apportare revisioni significative alle app per incorporare nuove funzionalità o per migliorarne il funzionamento su una piattaforma cloud.<br/><br/> Quando si vogliono sfruttare gli investimenti esistenti per le applicazioni, rispettare i requisiti di scalabilità, applicare le procedure DevOps di Azure innovative e ridurre al minimo l'uso di macchine virtuali.
**Ricostruzione** | Questa strategia consente di compiere un passo in avanti ricostruendo un'app da zero con le tecnologie cloud di Azure.<br/><br/> È ad esempio possibile creare app greenfield con tecnologie native del cloud come l'opzione senza server, il servizio di Azure per l'intelligenza artificiale, Istanza gestita di database SQL di Azure, Azure Cosmos DB e altro ancora. | Quando si vogliono sviluppare app rapidamente e le app esistenti hanno funzionalità e ciclo di vita limitati.<br/><br/> Quando si è pronti ad accelerare l'innovazione aziendale (adottando anche le procedure DevOps fornite da Azure), creare nuove applicazioni con tecnologie native del cloud e sfruttare i miglioramenti delle tecnologie di intelligenza artificiale, blockchain e IoT.

## <a name="migration-articles"></a>Articoli sulla migrazione

La tabella seguente presenta un riepilogo degli articoli inclusi nella serie relativa alla migrazione di Contoso.  

- Gli scenari presentano una complessità crescente e nel tempo ne verranno aggiunti altri.
- Ogni scenario di migrazione è guidato da obiettivi aziendali leggermente diversi che determinano la strategia di migrazione.
- Ogni scenario di distribuzione presenta informazioni sui fattori chiave per lo sviluppo e gli obiettivi aziendali, propone un'architettura, illustra i passaggi per la migrazione, fornisce indicazioni per la pulizia e suggerisce i passaggi successivi da eseguire al termine della migrazione.

**Articolo** | **Dettagli** | **Status**
--- | --- | ---
Articolo 1: Panoramica (questo articolo) | Offre una panoramica della strategia di migrazione di Contoso e presenta la serie di articoli e le app di esempio usate. | Disponibile
[Articolo 2: Distribuire un'infrastruttura di Azure](contoso-migration-overview.md) | Descrive in che modo Contoso prepara l'infrastruttura locale e di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli scenari di migrazione Contoso. | Disponibile
[Articolo 3: Valutare le risorse locali](contoso-migration-assessment.md) | Spiega in che modo Contoso esegue una valutazione dell'app SmartHotel a due livelli locale in esecuzione su VMware. Valuta le VM dell'app con il servizio [Azure Migrate](migrate-overview.md) e il database SQL Server dell'app con [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponibile
[Articolo 4: Eseguire il rehosting in VM di Azure e in un'istanza gestita di SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel ad Azure, usando [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) per la migrazione della VM Web dell'app e il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview) per la migrazione del database dell'app a un'istanza gestita di SQL. | Disponibile
[Articolo 5: Eseguire il rehosting in macchine virtuali di Azure](contoso-migration-rehost-vm.md) | Spiega in che modo Contoso esegue la migrazione delle VM dell'app SmartHotel usando il servizio Site Recovery.
[Articolo 6: Eseguire il rehosting in VM di Azure e nei gruppi di disponibilità di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel, usando Site Recovery per la migrazione della VM dell'app e il Servizio Migrazione del database per la migrazione del database dell'app a un gruppo di disponibilità AlwaysOn di SQL Server. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure](contoso-migration-rehost-linux-vm.md) | Spiega in che modo Contoso esegue la migrazione delle VM dell'app ad Azure mediante Site Recovery. | Pianificata
[Articolo 8: Eseguire il rehosting di un'app Linux in VM di Azure e in Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Spiega in che modo Contoso esegue la migrazione della VM dell'app mediante Site Recovery e usa MySQL Workbench per la migrazione a un'istanza di Azure MySQL Server. | Pianificata



### <a name="demo-apps"></a>App demo

Negli articoli vengono usate due app demo, SmartHotel e osTicket.

- L'app **SmartHotel360** è stata sviluppata da Microsoft come app di test che è possibile usare con Azure. È disponibile per l'uso in modalità open source e può essere scaricata da [GitHub](https://github.com/Microsoft/SmartHotel360). È un'app ASP.NET connessa a un database di SQL Server. Attualmente l'app viene usata su due macchine virtuali VMware che eseguono Windows Server 2008 R2 e SQL Server 2008 R2. Le VM dell'app vengono ospitate in locale e gestite da un server vCenter.
- **osTicket** è un'app open source per la gestione di ticket di assistenza che viene eseguita su Linux. È possibile scaricarlo da [GitHub](https://github.com/osTicket/osTicket). Attualmente l'app viene usata su due macchine virtuali VMware che eseguono Ubuntu 16.04LTS con Apache 2, PHP 7.0 e MySQL 5.7.
    

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come](contoso-migration-infrastructure.md) Contoso configura l'infrastruttura locale e di Azure nella fase preliminare alla migrazione.



