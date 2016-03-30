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
	ms.date="03/18/2016" 
	ms.author="raynew"/>

# Quali carichi di lavoro è possibile proteggere con Azure Site Recovery?

Azure Site Recovery consente ai clienti di distribuire soluzioni di disponibilità basate sul riconoscimento delle applicazioni che contribuiscono alla strategia di continuità aziendale e ripristino di emergenza (BCDR). Nel caso di app basate su Windows Server o Linux, applicazioni aziendali Microsoft oppure offerte di altri fornitori, è possibile usare Azure Site Recovery per abilitare il ripristino di emergenza, ambienti di sviluppo e test su richiesta e migrazione cloud.

Site Recovery si integra con numerose applicazioni Microsoft, tra cui SharePoint, Exchange, Dynamics, SQL Server e Active Directory. Microsoft collabora attivamente anche con altri fornitori software, tra cui Oracle, SAP, IBM e Red Hat, per assicurare la migliore interoperabilità di applicazioni e servizi sulle piattaforme Microsoft, inclusi Azure e Hyper-V. È possibile personalizzare la soluzione di ripristino di emergenza per qualsiasi tipo di applicazione.


##Funzionalità principali
Le funzionalità di Azure Site Recovery che contribuiscono alla strategia di protezione e ripristino a livello di applicazione includono:

- Replica quasi sincrona (30 secondi) con RPO per soddisfare le esigenze delle applicazioni critiche.
- Snapshot coerenti con l'app per le applicazioni a uno o più livelli.
- Integrazione con SQL Server AlwaysOn e interazione con altre tecnologie di replica a livello di applicazione, tra cui la replica di AD, SQL AlwaysOn, i gruppi di disponibilità dei database di Exchange e Oracle Data Guard.
- Piani di ripristino flessibili, che consentono di ripristinare un intero stack di applicazioni con un singolo clic e possono includere script esterni o azioni manuali. 
- Gestione di rete avanzata in Site Recovery e, in Azure, requisiti di rete più semplici per le app, quali la prenotazione di indirizzi IP, la configurazione di servizi di bilanciamento del carico o l'integrazione di Gestione traffico di Microsoft Azure per transizioni di rete con RTO ridotto.
-  Una libreria di automazione avanzata, in grado di fornire script generici per l'ambiente di produzione e specifici per ogni applicazione, che possono essere scaricati e integrati con Site Recovery.



##Riepilogo dei carichi di lavoro

Le tecnologie di replica di Site Recovery sono compatibili con qualsiasi applicazione in esecuzione su una macchina virtuale. Sono stati eseguiti anche test aggiuntivi in collaborazione con i team di prodotto dell'applicazione per fornire un supporto completo per ogni applicazione.

**Carico di lavoro** | <p>**Replicare macchine virtuali Hyper-V**</p> <p>**(per un sito secondario)**</p> | <p>**Replicare le macchine virtuali Hyper-V**</p> <p>**(per Azure)**</p> | <p>**Replicare le macchine virtuali VMware**</p> <p>**(per un sito secondario)**</p> | <p>**Replicare le macchine virtuali VMware**</p><p>**(per Azure)****</p>
---|---|---|---|---
Active Directory, DNS | S | S | S | S 
App Web (IIS, SQL) | S | S | S | S
SCOM | S | S | S | S
SharePoint | S | S | S | S
<p>SAP</p><p>Replicare il sito SAP in Azure per non cluster</p> | Y (testato da Microsoft) | Y (testato da Microsoft) | Y (testato da Microsoft) | Y (testato da Microsoft)
Exchange (non DAG) | S | Presto disponibile | S | S
Desktop remoto/VDI | S | S | S | N/D 
<p>Linux</p> <p>(sistema operativo e app)</p> | Y (testato da Microsoft) | Y (testato da Microsoft) | Y (testato da Microsoft) | Y (testato da Microsoft) 
Dynamics AX | S | S | S | S
Dynamics CRM | S | Presto disponibile | S | Presto disponibile
Oracle | Y (testato da Microsoft) | Y (testato da Microsoft) | Y (testato da Microsoft) | Y (testato da Microsoft)
File Server Windows | S | S | S | S

##Active Directory e DNS

Tutte le applicazioni aziendali, incluse SharePoint, Dynamics AX e SAP, dipendono da un'infrastruttura di Active Directory e DNS. Nell'ambito della soluzione di continuità aziendale e ripristino di emergenza, è necessario proteggere e ripristinare questi componenti di infrastruttura prima di eseguire il ripristino di carichi di lavoro e applicazioni.

Con Site Recovery è possibile creare un piano di ripristino di emergenza interamente automatizzato per Active Directory e DNS. Ad esempio, se si usa Active Directory per più applicazioni, come SharePoint e SAP nel sito primario, e si vuole eseguire il failover del sito completo, è possibile eseguire prima il failover di Active Directory tramite un piano di ripristino e quindi il failover delle applicazioni che si basano su Active Directory usando piani di ripristino specifici per ogni applicazione.

[Ulteriori informazioni](http://aka.ms/asr-ad)

##SQL Server

SQL Server costituisce la base per i servizi dati di numerose applicazioni aziendali nell'ambito di un data center locale. Site Recovery e le tecnologie per l'alta disponibilità e il ripristino di emergenza di SQL Server sono complementari e possono essere combinati per fornire la protezione end-to-end ad applicazioni aziendali multilivello. Site Recovery offre i vantaggi seguenti per gli ambienti SQL Server:

- Semplicità di protezione e replica in Azure o in un sito secondario di server SQL autonomi o in cluster. Fornisce una soluzione di ripristino di emergenza semplice ed economica per molte versioni ed edizioni di SQL Server.
- Integrazione con i gruppi di disponibilità di SQL AlwaysOn e gestione dei processi di failover e failback con piani di ripristino in Azure Site Recovery.
- Piani di ripristino end-to-end per l'intero stack di applicazioni, inclusi i database SQL Server.
- Possibilità di scalare SQL Server per i picchi di carico tramite Azure Site Recovery, espandendoli in macchine virtuali IaaS di dimensioni più grandi in Azure.
- Testing di SQL Server in Azure o in un sito secondario usando Azure Site Recovery. I failover di test consentono di eseguire controlli di conformità dei dati a scopo di analisi, senza influire sull'ambiente di produzione.

[Altre informazioni](http://aka.ms/asr-sql)

##SharePoint

Azure Site Recovery consente di proteggere la distribuzione di SharePoint. Con Site Recovery, in particolare, è possibile:

- Eliminare l'esigenza e i costi di infrastruttura associati per una farm in standby per il ripristino di emergenza. Con Site Recovery è possibile abilitare la protezione dell'intera farm (livelli Web, app e database) in Azure o in un sito secondario.
- Semplificare la distribuzione dell'applicazione e la gestibilità. Gli aggiornamenti distribuiti nel sito primario vengono replicati automaticamente e saranno disponibili quando si ripristina la farm nel sito secondario. In questo modo si eliminano le complessità di gestione per mantenere una farm in standby aggiornata, riducendo i costi.
- Semplificare lo sviluppo e il test dell'applicazione SharePoint creando una copia su richiesta simile a quella in produzione per il test e il debug nell'ambiente di replica.
- Semplificare la transizione nel cloud usando Site Recovery per la migrazione di distribuzioni di SharePoint in Azure.

[Altre informazioni](http://aka.ms/asr-sharepoint)


##Dynamics AX

Azure Site Recovery consente di proteggere la soluzione ERP Dynamics AX. In particolare, è possibile:

- Abilitare la protezione dell'intero ambiente Dynamics AX (livelli Web e AOS, database e SharePoint) in Azure o in un sito secondario usando Site Recovery.
- Semplificare la migrazione nel cloud usando Site Recovery per la migrazione di distribuzioni di Dynamics AX in Azure.
- Semplificare lo sviluppo e il test dell'applicazione Dynamics AX creando una copia su richiesta simile a quella in produzione per il test e il debug.

[Altre informazioni](http://aka.ms/asr-dynamics)

##Servizi desktop remoto 
Servizi Desktop remoto abilita l'uso di un'infrastruttura VDI, di desktop basati su sessione e di applicazioni, consentendo agli utenti di lavorare ovunque. Con Site Recovery è possibile abilitare la protezione di desktop virtuali in pool gestiti o non gestiti in un sito secondario, oltre che di applicazioni e sessioni remote in un sito secondario o in Azure.

[Altre informazioni](http://aka.ms/asr-rds)


##Exchange

Microsoft Exchange include il supporto incorporato per l'alta disponibilità e il ripristino di emergenza. Azure Site Recovery può interagire con i gruppi di disponibilità del database di Exchange.

- I gruppi di disponibilità del database di Exchange costituiscono la soluzione consigliata per il ripristino di emergenza di Exchange all'interno di un'organizzazione. I piani di ripristino di Site Recovery possono includere i gruppi di disponibilità del database per coordinarne il failover tra più siti.
- Per le piccole distribuzioni, ad esempio un singolo server o server non in cluster, Site Recovery consente la protezione e il failover in Azure o in un sito secondario.

[Altre informazioni](http://aka.ms/asr-exchange)

##SAP

Site Recovery consente di proteggere la distribuzione SAP. In particolare, è possibile:

- Abilitare la protezione dell'intera distribuzione SAP con vari livelli in Azure o in un sito secondario.
- Semplificare la migrazione nel cloud usando Site Recovery per eseguire la migrazione della distribuzione SAP in Azure.
- Semplificare lo sviluppo e il test dell'applicazione SAP creando una copia su richiesta simile a quella in produzione per il test e il debug.

[Altre informazioni](http://aka.ms/asr-sap)

<!---HONumber=AcomDC_0323_2016-->