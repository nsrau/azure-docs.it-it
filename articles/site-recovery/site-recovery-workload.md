---
title: Informazioni sul ripristino di emergenza per le app locali con Azure Site Recovery
description: Illustra i carichi di lavoro che possono essere protetti usando il ripristino di emergenza con il servizio Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062830"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Informazioni sul ripristino di emergenza per le app locali

Questo articolo descrive i carichi di lavoro locali e le app che è possibile proteggere per il ripristino di emergenza con il servizio [Azure Site Recovery.This](site-recovery-overview.md) article describes on-premises workloads and apps you can protect for disaster recovery with the Azure Site Recovery service.

## <a name="overview"></a>Panoramica

Le organizzazioni hanno bisogno di una strategia di continuità aziendale e ripristino di emergenza (BCDR) per mantenere i carichi di lavoro e i dati al sicuro e disponibili durante i tempi di inattività pianificati e non pianificati. E, recuperare a condizioni di lavoro regolari.

Site Recovery è un servizio di Azure che contribuisce all'attuazione della strategia BCDR. Tramite Site Recovery è possibile distribuire la replica compatibile con le applicazioni nel cloud o in un sito secondario. È possibile utilizzare Site Recovery per gestire la replica, eseguire test di ripristino di emergenza ed eseguire failover e failback. Le app possono essere eseguite su computer basati su Windows o Linux, server fisici, VMware o Hyper-V.

Site Recovery si integra con le applicazioni Microsoft come SharePoint, Exchange, Dynamics, SQL Server e Active Directory. Microsoft lavora a stretto contatto con i principali fornitori, tra cui Oracle, SAP e Red Hat. È possibile personalizzare le soluzioni di replica in base alle app.

## <a name="why-use-site-recovery-for-application-replication"></a>Perché usare Site Recovery per la protezione della replica?

Site Recovery contribuisce alla protezione e al ripristino a livello di applicazione come segue:

- Indipendente dall'app e fornisce la replica per tutti i carichi di lavoro in esecuzione in un computer supportato.
- Replica quasi sincrona, con obiettivi del punto di ripristino (RPO) fino a 30 secondi per soddisfare le esigenze delle app aziendali più critiche.
- Snapshot coerenti con l'app per le applicazioni a uno o più livelli.
- Integrazione con SQL Server AlwaysOn e collaborazione con altre tecnologie di replica a livello di applicazione. Ad esempio, la replica di Active Directory, SQL AlwaysOn e i gruppi di disponibilità del database di Exchange (DAG).
- Piani di ripristino flessibili che consentono di ripristinare un intero stack di applicazioni con un solo clic e di includere script esterni e azioni manuali nel piano.
- Gestione avanzata della rete in Site Recovery e Azure per semplificare i requisiti di rete delle app. Gestione della rete, ad esempio la possibilità di riservare indirizzi IP, configurare il bilanciamento del carico e l'integrazione con Gestione traffico di Azure per switchover di rete (RTO) con obiettivi di tempo di ripristino ridotti.
- Una libreria di automazione avanzata che fornisce script pronti per la produzione e specifici per ogni applicazione, che possono essere scaricati e integrati nei piani di ripristino.

## <a name="workload-summary"></a>Riepilogo dei carichi di lavoro

Site Recovery può replicare qualsiasi app in esecuzione in una macchina supportata. Abbiamo collaborato con i team di prodotto per eseguire ulteriori test per le app specificate nella tabella seguente.

| **Carico di lavoro** |**Replicare le VM di Azure in Azure** |**Replicare le VM Hyper-V in un sito secondario** | **Replicare VM Hyper-V in Azure** | **Replicare VM VMware in un sito secondario** | **Replicare VM VMware in Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS  |Sì |Sì |Sì |Sì |Sì|
| App Web (IIS, SQL) |Sì |Sì |Sì |Sì |Sì|
| System Center Operations Manager |Sì |Sì |Sì |Sì |Sì|
| SharePoint |Sì |Sì |Sì |Sì |Sì|
| SAP<br/><br/>Replicare il sito SAP in Azure per non cluster |Sì (testato da Microsoft) |Sì (testato da Microsoft) |Sì (testato da Microsoft) |Sì (testato da Microsoft) |Sì (testato da Microsoft)|
| Exchange (non DAG) |Sì |Sì |Sì |Sì |Sì|
| Desktop remoto/VDI |Sì |Sì |Sì |Sì |Sì|
| Linux (sistema operativo e app) |Sì (testato da Microsoft) |Sì (testato da Microsoft) |Sì (testato da Microsoft) |Sì (testato da Microsoft) |Sì (testato da Microsoft)|
| Dynamics AX |Sì |Sì |Sì |Sì |Sì|
| File Server Windows |Sì |Sì |Sì |Sì |Sì|
| Citrix XenApp e XenDesktop |Sì|N/D |Sì |N/D |Sì |

## <a name="replicate-active-directory-and-dns"></a>Replicare Active Directory e DNS

Un'infrastruttura DNS e Active Directory sono essenziali per la maggior parte delle applicazioni aziendali. Durante il ripristino di emergenza, è necessario proteggere e ripristinare questi componenti dell'infrastruttura, prima di ripristinare carichi di lavoro e app.

È possibile usare Site Recovery per creare un piano di ripristino di emergenza interamente automatizzato per Active Directory e DNS. Ad esempio, per eseguire il failover di SharePoint e SAP da un sito primario a un sito secondario, è possibile impostare un piano di ripristino che esegue prima il failover di Active Directory. Utilizzare quindi un piano di ripristino aggiuntivo specifico dell'app per eseguire il failover delle altre app che si basano su Active Directory.

[Ulteriori informazioni](site-recovery-active-directory.md) sul ripristino di emergenza per Active Directory e DNS.

## <a name="protect-sql-server"></a>Proteggere SQL Server

SQL ServerSQL Server fornisce una base di servizi dati per molte app aziendali in un data center locale. Site Recovery can be used with SQL Server HA/DR technologies, to protect multi-tiered enterprise apps that use SQL Server.

Site Recovery offre:

- Una soluzione di ripristino di emergenza semplice ed economica per SQL Server. Consente di replicare più versioni ed edizioni di server SQL autonomi e cluster in Azure o in un sito secondario.
- Integrazione con i gruppi di disponibilità SQL AlwaysOn per gestire failover e failback con piani di ripristino in Azure Site Recovery.
- Piani di ripristino end-to-end per tutti i livelli di un'applicazione, inclusi i database SQL Server.
- Scalabilità di SQL Server per i carichi di picco con Site Recovery, trasformandoli in dimensioni di macchine virtuali IaaS più grandi in Azure.Scaling of SQL Server for peak loads with Site Recovery, by _bursting_ them into larger IaaS virtual machine sizes in Azure.
- Esecuzione facilitata dei test di ripristino di emergenza di SQL Server. È possibile eseguire failover di test per analizzare i dati ed eseguire controlli di conformità, senza influire sull'ambiente di produzione.

[Altre informazioni](site-recovery-sql.md) sul ripristino di emergenza per SQL Server.Learn more about disaster recovery for SQL Server.

## <a name="protect-sharepoint"></a>Proteggere SharePoint

Azure Site Recovery consente di proteggere le distribuzioni di SharePoint come indicato di seguito:

- Elimina l'esigenza e i costi di infrastruttura associati per una farm in standby per il ripristino di emergenza. Usare Site Recovery per replicare un'intera farm (livelli Web, app e database) in Azure o in un sito secondario.
- Semplifica la distribuzione di applicazioni e la gestione. Gli aggiornamenti distribuiti nel sito primario vengono replicati automaticamente. Gli aggiornamenti sono disponibili dopo il failover e il ripristino di una farm in un sito secondario. Riduce la complessità di gestione e i costi associati al mantenimento di una farm di stand-by aggiornata.
- Semplifica lo sviluppo e i test dell'applicazione SharePoint creando una copia su richiesta simile a quella in produzione per il test e il debug nell'ambiente di replica.
- Semplifica la transizione nel cloud usando Site Recovery per la migrazione di distribuzioni di SharePoint in Azure.

[Altre informazioni](site-recovery-sharepoint.md) sul ripristino di emergenza per SharePoint.Learn more about disaster recovery for SharePoint.

## <a name="protect-dynamics-ax"></a>Proteggere Dynamics AX

Azure Site Recovery consente di proteggere la soluzione ERP Dynamics AX. In particolare, è possibile:

- Gestione della replica dell'intero ambiente Dynamics AX (livelli Web e AOS, livelli di database, SharePoint) in Azure o in un sito secondario.
- Semplificare della migrazione delle distribuzioni di Dynamics AX nel cloud (Azure).
- Semplificare lo sviluppo e il test dell'applicazione Dynamics AX creando una copia su richiesta simile a quella in produzione per il test e il debug.

[Ulteriori informazioni](site-recovery-dynamicsax.md) sul ripristino di emergenza per Ax dinamico.

## <a name="protect-remote-desktop-services"></a>Protezione di Servizi Desktop remoto

Servizi Desktop remoto (RDS) consente l'infrastruttura di desktop virtuali (VDI), desktop basati su sessione e applicazioni, che consentono agli utenti di lavorare ovunque.

Con Azure Site Recovery è possibile replicare i servizi seguenti:With Azure Site Recovery you can replicate the following services:

- Replicare i desktop virtuali in pool gestiti o non gestiti in un sito secondario.
- Replicare le applicazioni e le sessioni remote in un sito secondario o in Azure.Replica remote applications and sessions to a secondary site or Azure.

Nella tabella seguente vengono illustrate le opzioni di replica:

| **Rds** |**Replicare le VM di Azure in Azure** | **Replicare le VM Hyper-V in un sito secondario** | **Replicare VM Hyper-V in Azure** | **Replicare VM VMware in un sito secondario** | **Replicare VM VMware in Azure** | **Replicare di server fisici in un sito secondario** | **Replicare i server fisici in Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Desktop virtuale in pool (non gestito)** |No|Sì |No |Sì |No |Sì |No |
| **Desktop virtuale in pool (gestito e senza UPD)** |No|Sì |No |Sì |No |Sì |No |
| **Applicazioni remote e le sessioni Desktop (senza UPD)** |Sì|Sì |Sì |Sì |Sì |Sì |Sì |

[Altre informazioni](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) sul ripristino di emergenza per Servizi Desktop remoto.

## <a name="protect-exchange"></a>Proteggere Exchange

Site Recovery consente di proteggere Exchange come segue:

- Per le piccole distribuzioni di Exchange, ad esempio server singolo o autonomo, Site Recovery consente la replica e il failover in Azure o in un sito secondario.
- Per distribuzioni estese, Site Recovery si integra con i gruppi di disponibilità dei database di Exchange.
- I gruppi di disponibilità del database di Exchange costituiscono la soluzione consigliata per il ripristino di emergenza di Exchange all'interno di un'organizzazione.  I piani di ripristino di Site Recovery possono includere i gruppi di disponibilità dei database per orchestrare il relativo failover tra siti.

Per ulteriori informazioni sul ripristino di emergenza per Exchange, vedere [DAG di Exchange](/Exchange/high-availability/database-availability-groups/database-availability-groups) e [ripristino di emergenza](/Exchange/high-availability/disaster-recovery/disaster-recovery)di Exchange .

## <a name="protect-sap"></a>Proteggere SAP

Usare Site Recovery per proteggere la distribuzione SAP. In particolare, è possibile:

- Abilitare la protezione di applicazioni di produzione SAP NetWeaver e non NetWeaver in esecuzione in locale, eseguendo la replica dei componenti in Azure.
- Abilitare la protezione di applicazioni di produzione SAP NetWeaver e non NetWeaver in esecuzione in Azure, eseguendo la replica dei componenti in un altro data center di Azure.
- Semplificare la migrazione nel cloud usando Site Recovery per eseguire la migrazione della distribuzione SAP in Azure.
- Semplificare gli aggiornamenti, i test e la creazione di prototipi dei progetti SAP, creando un clone di produzione on demand per i test delle applicazioni SAP.

[Altre informazioni](site-recovery-sap.md) sul ripristino di emergenza per SAP.

## <a name="protect-internet-information-services"></a>Proteggere Internet Information Services

Utilizzare Site Recovery per proteggere la distribuzione di Internet Information Services (IIS), come indicato di seguito:

Azure Site Recovery assicura il ripristino di emergenza replicando i componenti critici dell'ambiente in un sito remoto non attivo o in un cloud pubblico, ad esempio Microsoft Azure. Poiché le macchine virtuali con il server Web e il database vengono replicate nel sito di ripristino, non è necessario un backup separato per i file di configurazione o i certificati. I mapping e le associazioni dell'applicazione dipendenti dalle variabili di ambiente modificate dopo il failover possono essere aggiornati tramite gli script integrati nei piani di ripristino di emergenza. Le macchine virtuali vengono visualizzate nel sito di ripristino solo durante un failover. Azure Site Recovery consente inoltre di orchestrare il failover end-to-end fornendo le funzionalità seguenti:Azure Site Recovery also helps you orchestrate the end-to-end failover by providing you the following capabilities:

- Sequenza di arresto e avvio delle macchine virtuali nei diversi livelli.
- Aggiunta di script per consentire gli aggiornamenti delle dipendenze e delle associazioni dell'applicazione nelle macchine virtuali dopo l'avvio. Gli script possono essere usati anche per aggiornare il server DNS in modo che punti al sito di ripristino.
- Allocare gli indirizzi IP alle macchine virtuali prima del failover eseguendo il mapping delle reti primarie e di ripristino e usare script che non devono essere aggiornati dopo il failover.
- Possibilità di failover con un clic per più applicazioni Web che elimina l'ambito di confusione durante un'emergenza.
- Possibilità di testare i piani di ripristino in un ambiente isolato per le esercitazioni sul ripristino di emergenza.

[Ulteriori informazioni](site-recovery-iis.md) sul ripristino di emergenza per IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Proteggere Citrix XenApp e XenDesktop

Usare Site Recovery per proteggere le distribuzioni Citrix XenApp e XenDesktop come illustrato di seguito:

- Abilitare la protezione della distribuzione Citrix XenApp e XenDesktop. Replicare i diversi livelli di distribuzione in Azure: Active Directory, server DNS, server di database SQL, Citrix Delivery Controller, StoreFront server, XenApp Master (VDA), Citrix XenApp License Server.
- Semplificare la migrazione cloud usando Site Recovery per eseguire la migrazione della distribuzione Citrix XenApp e XenDesktop in Azure.
- Semplificare i test di Citrix XenApp/XenDesktop creando una copia su richiesta simile a quella di produzione per i test e il debug.
- Questa soluzione si applica solo ai desktop virtuali di Windows Server e non ai desktop virtuali client. Client virtual desktops aren't yet supported for licensing in Azure. [Altre informazioni](https://azure.microsoft.com/pricing/licensing-faq/) sulle licenze per computer desktop client/server in Azure.

[Ulteriori informazioni](site-recovery-citrix-xenapp-and-xendesktop.md) sul ripristino di emergenza per le distribuzioni Citrix XenApp e XenDesktop. In alternativa, è possibile fare riferimento al [whitepaper Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr).

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](azure-to-azure-quickstart.md) sul ripristino di emergenza per una macchina virtuale di Azure.Learn more about disaster recovery for an Azure VM.
