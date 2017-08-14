---
title: "Quali carichi di lavoro è possibile proteggere con Azure Site Recovery?"
description: Azure Site Recovery protegge i carichi di lavoro e le applicazioni coordinando la replica, il failover e il ripristino di macchine virtuali locali e server fisici in Azure o in un sito locale secondario.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: 4953948f-26c0-4699-8fe7-59d3bfc1d3da
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/08/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 88f20287e2a6935b9694a25a21b607d658b8eb68
ms.contentlocale: it-it
ms.lasthandoff: 08/08/2017

---
# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Quali carichi di lavoro è possibile proteggere con Azure Site Recovery?
Questo articolo descrive i carichi di lavoro e applicazioni di cui è possibile eseguire la replica con il servizio Azure Site Recovery.

Per inviare commenti o domande, è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Overview
Le organizzazioni necessitano di una strategia per la continuità aziendale e il ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) per garantire la sicurezza e la disponibilità dei carichi di lavoro e dei dati durante i periodi di inattività pianificati e non pianificati e per ripristinare prima possibile le normali condizioni di lavoro.

Site Recovery è un servizio di Azure che contribuisce all'attuazione della strategia BCDR. Tramite Site Recovery è possibile distribuire la replica compatibile con le applicazioni nel cloud o in un sito secondario. Sia che si tratti di app basate su Windows o su Linux in esecuzione in server fisici, in VMware o Hyper-V, è possibile usare Site Recovery per orchestrare la replica, eseguire il test del piano di ripristino di emergenza test ed eseguire failover e failback.

Site Recovery si integra con numerose applicazioni Microsoft, tra cui SharePoint, Exchange, Dynamics, SQL Server e Active Directory. Microsoft collabora strettamente con fornitori leader, tra cui Oracle, SAP, IBM e Red Hat. È possibile personalizzare le soluzioni di replica in base alle app.

## <a name="why-use-site-recovery-for-application-replication"></a>Perché usare Site Recovery per la protezione della replica?
Site Recovery contribuisce alla protezione e al ripristino a livello di applicazione come segue:

* Indipendente dall'app, fornendo la replica per carichi di lavoro in esecuzione in una macchina supportata.
* Replica quasi sincrona con RPO di 30 secondi per soddisfare le esigenze delle app aziendali più critiche.
* Snapshot coerenti con l'app per le applicazioni a uno o più livelli.
* Integrazione con SQL Server AlwaysOn e interazione con altre tecnologie di replica a livello di applicazione, tra cui la replica di AD, SQL AlwaysOn, i gruppi di disponibilità dei database di Exchange e Oracle Data Guard.
* Piani di ripristino flessibili che consentono di ripristinare un intero stack di applicazioni con un singolo clic e includono script esterni o azioni manuali.
* Gestione di rete avanzata in Site Recovery e in Azure per semplificare i requisiti di rete per le app, incluse la prenotazione di indirizzi IP, la configurazione del servizio di bilanciamento del carico o l'integrazione di Gestione traffico di Azure per transizioni di rete con RTO ridotto.
* Una libreria di automazione avanzata che fornisce script pronti per la produzione e specifici per ogni applicazione, che possono essere scaricati e integrati nei piani di ripristino.

## <a name="workload-summary"></a>Riepilogo dei carichi di lavoro
Site Recovery può replicare qualsiasi app in esecuzione in una macchina supportata. Grazie anche alla collaborazione con i team di prodotto sono stati eseguiti test aggiuntivi specifici dell'applicazione.

| **Carico di lavoro** | **Replicare le VM Hyper-V in un sito secondario** | **Replicare VM Hyper-V in Azure** | **Replicare VM VMware in un sito secondario** | **Replicare VM VMware in Azure** |
| --- | --- | --- | --- | --- |
| Active Directory, DNS  |S |S |S |S |
| App Web (IIS, SQL) |S |S |S |S |
| System Center Operations Manager |S |S |S |S |
| SharePoint |S |S |S |S |
| SAP<br/><br/>Replicare il sito SAP in Azure per non cluster |Y (testato da Microsoft) |Y (testato da Microsoft) |Y (testato da Microsoft) |Y (testato da Microsoft) |
| Exchange (non DAG) |S |S |S |S |
| Desktop remoto/VDI |S |S |S |N/D |
| Linux (sistema operativo e app) |Y (testato da Microsoft) |Y (testato da Microsoft) |Y (testato da Microsoft) |Y (testato da Microsoft) |
| Dynamics AX |S |S |S |S |
| Dynamics CRM |S |Presto disponibile |S |Presto disponibile |
| Oracle |Y (testato da Microsoft) |Y (testato da Microsoft) |Y (testato da Microsoft) |Y (testato da Microsoft) |
| File Server Windows |S |S |S |S |
| Citrix XenApp e XenDesktop |N/D |S |N/D |S |

## <a name="replicate-active-directory-and-dns"></a>Replicare Active Directory e DNS
Un'infrastruttura DNS e Active Directory sono essenziali per la maggior parte delle applicazioni aziendali. Durante il ripristino di emergenza è necessario proteggere e ripristinare questi componenti di infrastruttura prima di eseguire il ripristino di carichi di lavoro e applicazioni.

È possibile usare Site Recovery per creare un piano di ripristino di emergenza interamente automatizzato per Active Directory e DNS. Ad esempio, se si vuole eseguire il failover di SharePoint e SAP da un sito primario a uno secondario, è possibile configurare un piano di ripristino che esegue prima di tutto il failover di Active Directory, quindi un piano di ripristino aggiuntivo specifico dell'app per eseguire il failover delle altre app che si basano su Active Directory.

[Altre informazioni](site-recovery-active-directory.md) sulla protezione di Active Directory e DNS.

## <a name="protect-sql-server"></a>Proteggere SQL Server
SQL Server fornisce un base per i servizi dati di molte app aziendali in un data center locale.  Site Recovery può essere usato con le tecnologie a disponibilità elevata/ripristino di emergenza di SQL Server per proteggere le app aziendali a più livelli che usano SQL Server. Site Recovery offre:

* Una soluzione di ripristino di emergenza semplice ed economica per SQL Server. Consente di replicare più versioni ed edizioni di server SQL autonomi e cluster in Azure o in un sito secondario.  
* Integrazione con i gruppi di disponibilità SQL AlwaysOn per gestire failover e failback con piani di ripristino in Azure Site Recovery.
* Piani di ripristino end-to-end per tutti i livelli di un'applicazione, inclusi i database SQL Server.
* Possibilità di ridimensionamento di SQL Server per i picchi di carico con Site Recovery, espandendoli in macchine virtuali IaaS di dimensioni maggiori in Azure.
* Esecuzione facilitata dei test di ripristino di emergenza di SQL Server. È possibile eseguire failover di test per analizzare i dati ed eseguire controlli di conformità, senza influire sull'ambiente di produzione.

[Altre informazioni](site-recovery-sql.md) sulla protezione di SQL server.

## <a name="protect-sharepoint"></a>Proteggere SharePoint
Azure Site Recovery consente di proteggere le distribuzioni di SharePoint come indicato di seguito:

* Elimina l'esigenza e i costi di infrastruttura associati per una farm in standby per il ripristino di emergenza. Usare Site Recovery per replicare un'intera farm (livelli Web, app e database) in Azure o in un sito secondario.
* Semplifica la distribuzione di applicazioni e la gestione. Gli aggiornamenti distribuiti al sito primario vengono replicati automaticamente e sono quindi disponibili dopo il failover e il ripristino di una farm in un sito secondario. In questo modo si eliminano le complessità di gestione per mantenere aggiornata una farm in standby.
* Semplifica lo sviluppo e i test dell'applicazione SharePoint creando una copia su richiesta simile a quella in produzione per il test e il debug nell'ambiente di replica.
* Semplifica la transizione nel cloud usando Site Recovery per la migrazione di distribuzioni di SharePoint in Azure.

[Altre informazioni](site-recovery-sharepoint.md) sulla protezione di SharePoint.

## <a name="protect-dynamics-ax"></a>Proteggere Dynamics AX
Azure Site Recovery consente di proteggere la soluzione ERP Dynamics AX. In particolare, è possibile:

* Orchestrare la replica dell'intero ambiente Dynamics AX (livelli Web e AOS, database e SharePoint) in Azure o in un sito secondario.
* Semplificare della migrazione delle distribuzioni di Dynamics AX nel cloud (Azure).
* Semplificare lo sviluppo e il test dell'applicazione Dynamics AX creando una copia su richiesta simile a quella in produzione per il test e il debug.

[Altre informazioni](site-recovery-dynamicsax.md) sulla protezione di Dynamic AX.

## <a name="protect-rds"></a>Proteggere RDS
Servizi Desktop remoto abilita l'uso di un'infrastruttura VDI (Virtual Desktop Infrastructure), di desktop basati su sessione e di applicazioni, consentendo agli utenti di lavorare ovunque. Con Azure Site Recovery è possibile:

* Replicare desktop virtuali in pool gestiti o non gestiti in un sito secondario, oltre che applicazioni e sessioni remote in un sito secondario o in Azure.
* Ecco cosa è possibile replicare:

| **SERVIZI DESKTOP REMOTO** | **Replicare le VM Hyper-V in un sito secondario** | **Replicare VM Hyper-V in Azure** | **Replicare VM VMware in un sito secondario** | **Replicare VM VMware in Azure** | **Replicare di server fisici in un sito secondario** | **Replicare i server fisici in Azure** |
| --- | --- | --- | --- | --- | --- | --- |
| **Desktop virtuale in pool (non gestito)** |Sì |No |Sì |No |Sì |No |
| **Desktop virtuale in pool (gestito e senza UPD)** |Sì |No |Sì |No |Sì |No |
| **Applicazioni remote e le sessioni Desktop (senza UPD)** |Sì |Sì |Sì |Sì |Sì |Sì |

[Altre informazioni](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sulla protezione di RDS.

## <a name="protect-exchange"></a>Proteggere Exchange
Site Recovery consente di proteggere Exchange come segue:

* Per le piccole distribuzioni di Exchange, ad esempio server singolo o autonomi, Site Recovery consente la replica e il failover in Azure o in un sito secondario.
* Per distribuzioni estese, Site Recovery si integra con i gruppi di disponibilità dei database di Exchange.
* I gruppi di disponibilità del database di Exchange costituiscono la soluzione consigliata per il ripristino di emergenza di Exchange all'interno di un'organizzazione.  I piani di ripristino di Site Recovery possono includere i gruppi di disponibilità dei database per orchestrare il relativo failover tra siti.

[Altre informazioni](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) sulla protezione di Exchange.

## <a name="protect-sap"></a>Proteggere SAP
Usare Site Recovery per proteggere la distribuzione SAP. In particolare, è possibile:

* Abilitare la protezione di applicazioni di produzione SAP NetWeaver e non NetWeaver in esecuzione in locale, eseguendo la replica dei componenti in Azure.
* Abilitare la protezione di applicazioni di produzione SAP NetWeaver e non NetWeaver in esecuzione in Azure, eseguendo la replica dei componenti in un altro data center di Azure.
* Semplificare la migrazione nel cloud usando Site Recovery per eseguire la migrazione della distribuzione SAP in Azure.
* Semplificare gli aggiornamenti, i test e la creazione di prototipi dei progetti SAP, creando un clone di produzione on demand per i test delle applicazioni SAP.

[Altre informazioni](site-recovery-sap.md) sulla protezione di SAP.

## <a name="protect-iis"></a>Proteggere IIS
Usare Site Recovery per proteggere la distribuzione IIS. In particolare, è possibile:

Azure Site Recovery assicura il ripristino di emergenza replicando i componenti critici dell'ambiente in un sito remoto non attivo o in un cloud pubblico, ad esempio Microsoft Azure. Poiché la macchina virtuale con il server Web e il database vengono replicati nel sito di ripristino, non è necessario eseguire il backup separato dei file di configurazione o dei certificati. I mapping e le associazioni dell'applicazione dipendenti dalle variabili di ambiente modificate dopo il failover possono essere aggiornati tramite gli script integrati nei piani di ripristino di emergenza. Le macchine virtuali vengono spostate nel sito di ripristino solo in caso di failover. Azure Site Recovery consente anche di orchestrare il failover end-to-end con le funzionalità seguenti:

-   Sequenza di arresto e avvio delle macchine virtuali nei diversi livelli.
-   Aggiunta di script per consentire l'aggiornamento delle associazioni e delle dipendenze tra applicazioni nelle macchine virtuali dopo che sono state avviate. Gli script possono essere usati anche per aggiornare il server DNS in modo che punti al sito di ripristino.
-   Allocare gli indirizzi IP alle macchine virtuali prima del failover eseguendo il mapping delle reti primarie e di ripristino e quindi usare script che non è necessario aggiornare dopo il failover.
-   Possibilità di failover con un clic per più applicazioni Web nei server Web, eliminando così la confusione derivante da un'emergenza.
-   Possibilità di testare i piani di ripristino in un ambiente isolato per le esercitazioni sul ripristino di emergenza.

[Altre informazioni](https://aka.ms/asr-iis) sulla protezione di una Web farm IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Proteggere Citrix XenApp e XenDesktop
Usare Site Recovery per proteggere le distribuzioni Citrix XenApp e XenDesktop come illustrato di seguito:

* Abilitare la protezione della distribuzione Citrix XenApp e XenDesktop replicando diversi livelli di distribuzione, tra cui server DNS di AD, server di database SQL, Citrix Delivery Controller, server StoreFront, XenApp Master (VDA), server licenze di Citrix XenApp, in Azure.
* Semplificare la migrazione cloud usando Site Recovery per eseguire la migrazione della distribuzione Citrix XenApp e XenDesktop in Azure.
* Semplificare i test di Citrix XenApp/XenDesktop creando una copia su richiesta simile a quella di produzione per i test e il debug.
* Questa soluzione è applicabile solo per computer desktop virtuali con sistema operativo Windows Server e non per computer desktop virtuali client, poiché questi ultimi non sono ancora supportati per le licenze in Azure.
[Altre informazioni](https://azure.microsoft.com/pricing/licensing-faq/) sulle licenze per computer desktop client/server in Azure.

[Altre informazioni](site-recovery-citrix-xenapp-and-xendesktop.md) sulla protezione di distribuzioni Citrix XenApp e XenDesktop. In alternativa, è possibile vedere il [white paper di Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr) contenente informazioni dettagliate.

## <a name="next-steps"></a>Passaggi successivi
[Verifica dei prerequisiti](site-recovery-prereq.md)

