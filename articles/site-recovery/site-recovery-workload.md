<properties
	pageTitle="Quali carichi di lavoro è possibile proteggere con Azure Site Recovery?" 
	description="Azure Site Recovery protegge i carichi di lavoro e le applicazioni coordinando la replica, il failover e il ripristino di macchine virtuali locali e server fisici in Azure o in un sito locale secondario." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="07/06/2016" 
	ms.author="raynew"/>

# Quali carichi di lavoro è possibile proteggere con Azure Site Recovery?


Questo articolo descrive i carichi di lavoro e applicazioni che è possibile proteggere con Azure Site Recovery.

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Panoramica

Le organizzazioni necessitano di una strategia per la continuità aziendale e per il ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) che consente di determinare il modo in cui le app, i carichi di lavoro e i dati rimangono disponibili durante periodi di inattività pianificati e come ripristinare le condizioni di lavoro normali il prima possibile.

Il servizio Azure Site Recovery contribuisce alla strategia BCDR consentendo di distribuire soluzioni di resilienza compatibili con le applicazioni e orchestrando la replica dei server fisici locali e delle macchine virtuali sul cloud (Azure) o in un sito secondario. Sia che si tratti di app basate su Windows o su Linux in esecuzione in server fisici, in VM VMware o Hyper-V, è possibile usare Site Recovery per orchestrare la replica, eseguire il test del piano di ripristino di emergenza test ed eseguire failover e failback.


Site Recovery si integra con numerose applicazioni Microsoft, tra cui SharePoint, Exchange, Dynamics, SQL Server e Active Directory. Microsoft collabora attivamente anche con importanti fornitori, tra cui Oracle, SAP, IBM e Red Hat, per assicurare la migliore interoperabilità di applicazioni e servizi sulle piattaforme Microsoft, inclusa Azure. È possibile personalizzare una soluzione per ogni app.

## Perché usare Site recovery per la protezione dell'applicazione

Site Recovery contribuisce alla protezione e al ripristino a livello di applicazione come segue:

- Replica quasi sincrona con RPO di 30 secondi per soddisfare le esigenze delle app aziendali più critiche.
- Snapshot coerenti con l'app per le applicazioni a uno o più livelli.
- Integrazione con SQL Server AlwaysOn e interazione con altre tecnologie di replica a livello di applicazione, tra cui la replica di AD, SQL AlwaysOn, i gruppi di disponibilità dei database di Exchange e Oracle Data Guard.
- Piani di ripristino flessibili che consentono di ripristinare un intero stack di applicazioni con un singolo clic e includono script esterni o azioni manuali.
- Gestione di rete avanzata in Site Recovery e in Azure per semplificare i requisiti di rete per le app, incluse la prenotazione di indirizzi IP, la configurazione del servizio di bilanciamento del carico o l'integrazione di Gestione traffico di Azure per transizioni di rete con RTO ridotto.
-  Una libreria di automazione avanzata che fornisce script pronti per la produzione e specifici per ogni applicazione, che possono essere scaricati e integrati nei piani di ripristino.



##Riepilogo dei carichi di lavoro

Site Recovery può replicare qualsiasi app in esecuzione in una macchina virtuale supportata. Grazie anche alla collaborazione con i team di prodotto sono stati eseguiti test aggiuntivi specifici dell'applicazione.

**Carico di lavoro** | **Replicare le VM Hyper-V in un sito secondario** | **Replicare VM Hyper-V in Azure** | **Replicare VM VMware in un sito secondario** | **Replicare VM VMware in Azure**
---|---|---|---|---
Active Directory, DNS | S | S | S | S 
App Web (IIS, SQL) | S | S | S | S
SCOM | S | S | S | S
SharePoint | S | S | S | S
SAP<br/><br/>Replicare il sito SAP in Azure per non cluster | Y (testato da Microsoft) | Y (testato da Microsoft) | Y (testato da Microsoft) | Y (testato da Microsoft)
Exchange (non DAG) | S | Presto disponibile | S | S
Desktop remoto/VDI | S | S | S | N/D 
Linux (sistema operativo e app) | Y (testato da Microsoft) | Y (testato da Microsoft) | Y (testato da Microsoft) | Y (testato da Microsoft) 
Dynamics AX | S | S | S | S
Dynamics CRM | S | Presto disponibile | S | Presto disponibile
Oracle | Y (testato da Microsoft) | Y (testato da Microsoft) | Y (testato da Microsoft) | Y (testato da Microsoft)
File Server Windows | S | S | S | S


## Replicare Active Directory e DNS

Un'infrastruttura DNS e Active Directory sono essenziali per la maggior parte delle applicazioni aziendali. Durante il ripristino di emergenza è necessario proteggere e ripristinare questi componenti di infrastruttura prima di eseguire il ripristino di carichi di lavoro e applicazioni.

È possibile usare Site Recovery per creare un piano di ripristino di emergenza interamente automatizzato per Active Directory e DNS. Ad esempio, se si vuole eseguire il failover di SharePoint e SAP da un sito primario a uno secondario, è possibile configurare un piano di ripristino che esegue prima di tutto il failover di Active Directory, quindi un piano di ripristino aggiuntivo specifico dell'app per eseguire il failover delle altre app che si basano su Active Directory.

[Altre informazioni](site-recovery-active-directory.md) sulla protezione di Active Directory e DNS.

## Proteggere SQL Server

SQL Server fornisce un base per i servizi dati di molte app aziendali in un data center locale. Site Recovery può essere usato con le tecnologie a disponibilità elevata/ripristino di emergenza di SQL Server per proteggere le app aziendali a più livelli che usano SQL Server. Site Recovery offre:

- Una soluzione di ripristino di emergenza semplice ed economica per SQL Server. Consente di replicare più versioni ed edizioni di server SQL autonomi e cluster in Azure o in un sito secondario.
- Integrazione con i gruppi di disponibilità SQL AlwaysOn per gestire failover e failback con piani di ripristino in Azure Site Recovery.
- Piani di ripristino end-to-end per tutti i livelli di un'applicazione, inclusi i database SQL Server.
- Possibilità di ridimensionamento di SQL Server per i picchi di carico con Site Recovery, espandendoli in macchine virtuali IaaS di dimensioni maggiori in Azure.
- Esecuzione facilitata dei test di ripristino di emergenza di SQL Server. È possibile eseguire failover di test per analizzare i dati ed eseguire controlli di conformità, senza influire sull'ambiente di produzione.

[Altre informazioni](site-recovery-sql.md) sulla protezione di SQL server.

##Proteggere SharePoint

Azure Site Recovery consente di proteggere le distribuzioni di SharePoint come indicato di seguito:

- Elimina l'esigenza e i costi di infrastruttura associati per una farm in standby per il ripristino di emergenza. Usare Site Recovery per replicare un'intera farm (livelli Web, app e database) in Azure o in un sito secondario.
- Semplifica la distribuzione di applicazioni e la gestione. Gli aggiornamenti distribuiti al sito primario vengono replicati automaticamente e sono quindi disponibili dopo il failover e il ripristino di una farm in un sito secondario. In questo modo si eliminano le complessità di gestione per mantenere aggiornata una farm in standby.
- Semplifica lo sviluppo e i test dell'applicazione SharePoint creando una copia su richiesta simile a quella in produzione per il test e il debug nell'ambiente di replica.
- Semplifica la transizione nel cloud usando Site Recovery per la migrazione di distribuzioni di SharePoint in Azure.

[Altre informazioni](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) sulla protezione di SharePoint.


## Proteggere Dynamics AX

Azure Site Recovery consente di proteggere la soluzione ERP Dynamics AX. In particolare, è possibile:

- Orchestrare la replica dell'intero ambiente Dynamics AX (livelli Web e AOS, database e SharePoint) in Azure o in un sito secondario.
- Semplificare della migrazione delle distribuzioni di Dynamics AX nel cloud (Azure).
- Semplificare lo sviluppo e il test dell'applicazione Dynamics AX creando una copia su richiesta simile a quella in produzione per il test e il debug.

[Altre informazioni](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) sulla protezione di Dynamic AX.

## Proteggere RDS 

Servizi Desktop remoto abilita l'uso di un'infrastruttura VDI (Virtual Desktop Infrastructure), di desktop basati su sessione e di applicazioni, consentendo agli utenti di lavorare ovunque. Con Azure Site Recovery è possibile:

- Replicare desktop virtuali in pool gestiti o non gestiti in un sito secondario, oltre che applicazioni e sessioni remote in un sito secondario o in Azure.
- Ecco cosa è possibile replicare:

**SERVIZI DESKTOP REMOTO** | **Replicare le VM Hyper-V in un sito secondario** | **Replicare VM Hyper-V in Azure** | **Replicare VM VMware in un sito secondario** | **Replicare VM VMware in Azure** | **Replicare di server fisici in un sito secondario** | **Replicare i server fisici in Azure**
---|---|---|---|---|---|---
**Desktop virtuale in pool (non gestito)** | Sì | No | Sì | No | Sì | No
**Desktop virtuale in pool (gestito e senza UPD)** | Sì | No | Sì | No | Sì | No
**Applicazioni remote e le sessioni Desktop (senza UPD)** | Sì | Sì | Sì | Sì | Sì | Sì


[Altre informazioni](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sulla protezione di RDS.


## Proteggere Exchange

Site Recovery consente di proteggere Exchange come segue:

- Per le piccole distribuzioni di Exchange, ad esempio un singolo server o server non in cluster, Site Recovery consente la replica e il failover in Azure o in un sito secondario.
- Per distribuzioni estese Site Recovery si integra con i gruppi di disponibilità dei database di Exchange.
- I gruppi di disponibilità del database di Exchange costituiscono la soluzione consigliata per il ripristino di emergenza di Exchange all'interno di un'organizzazione. I piani di ripristino di Site Recovery possono includere i gruppi di disponibilità dei database per orchestrare il relativo failover tra siti.


[Altre informazioni](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) sulla protezione di Exchange.

## Proteggere SAP

Usare Site Recovery per proteggere la distribuzione SAP. In particolare, è possibile:

- Abilitare la protezione dell'intera distribuzione SAP replicando vari livelli di distribuzione in Azure o in un sito secondario.
- Semplificare la migrazione nel cloud usando Site Recovery per eseguire la migrazione della distribuzione SAP in Azure.
- Semplificare lo sviluppo e il test dell'applicazione SAP creando una copia su richiesta simile a quella in produzione per il test e il debug.

[Altre informazioni](http://aka.ms/asr-sap) sulla protezione di SAP.

## Passaggi successivi

[Preparare](site-recovery-best-practices.md) la distribuzione di Site Recovery

<!---HONumber=AcomDC_0706_2016-->