<properties
	pageTitle="Indicazioni relative al carico di lavoro di Site Recovery | Microsoft Azure" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali e dei server fisici ubicati nei server locali in Azure o in un sito locale secondario." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="09/21/2015" 
	ms.author="pratshar"/>

# Indicazioni relative al carico di lavoro di Site Recovery

Azure Site Recovery consente ai clienti di distribuire soluzioni compatibili con le applicazioni. Sia nel caso di applicazioni basate su Windows Server o su Linux, di applicazioni aziendali proprietarie di Microsoft oppure di offerte di altri fornitori, è possibile usare Azure Site Recovery per abilitare il ripristino di emergenza, ambienti di sviluppo/test su richiesta o migrazioni cloud.

Microsoft vanta un'esperienza e conoscenze approfondite nello sviluppo di applicazioni aziendali all'avanguardia, ad esempio SharePoint, Exchange, Dynamics, SQL Server e Active Directory. Microsoft collabora attivamente anche con importanti fornitori, tra cui Oracle, SAP, IBM e Red Hat, per assicurare la migliore interoperabilità di applicazioni e servizi sulle piattaforme Microsoft, incluse Azure e Hyper-V. Sfruttando queste potenzialità esclusive è stato possibile conoscere a fondo i requisiti di disponibilità di ogni applicazione, per consentire ai clienti di distribuire soluzioni di ripristino di emergenza e disponibilità che possono essere personalizzare per ogni applicazione.


##Funzionalità principali
Le funzionalità di Azure Site Recovery sono state progettate tenendo presenti la protezione e il ripristino a livello di applicazione:

- Replica quasi sincrona, di soli 30 secondi, con gli RPO per soddisfare le esigenze delle applicazioni più critiche.
- Snapshot coerenti con l'applicazione per le applicazioni a uno o più livelli.
- Integrazione con la replica a livello di applicazione. Consente di sfruttare le migliori offerte a livello di applicazione, quali replica di AD, SQL AlwaysOn, gruppi di disponibilità del database di Exchange e Oracle Data Guard.
- Piani di ripristino flessibili abilitano il ripristino dell'intero stack di applicazioni con un singolo clic, inclusa l'esecuzione di script esterni o anche azioni manuali. 
- La gestione di rete avanzata in ASR e Azure automatizza tutte le configurazioni di rete specifiche della propria applicazione: riservare indirizzi IP, configurare i servizi di bilanciamento del carico o usare Gestione traffico di Microsoft per le transizioni di rete con RTO ridotto.
- Libreria di automazione avanzata che fornisce script per l'ambiente di produzione e specifici dell'applicazione. Scaricarli e integrarli nelle proprie soluzioni basate su ASR.


##Riepilogo delle indicazioni relative al carico di lavoro

Le tecnologie di replica ASR sono compatibili con qualsiasi applicazione in esecuzione in una macchina virtuale. Sono stati eseguiti ulteriori test in collaborazione con i team di prodotto dell’applicazione per ulteriore supporto per ogni applicazione.

**Carico di lavoro** | <p>**Replicare macchine virtuali Hyper-V**</p> <p>**(al sito secondario)**</p> | <p>**Replicare macchine virtuali Hyper-V**</p><p>**(ad Azure)**</p> | <p>**Replicare macchine virtuali VMware**</p> <p>**(al sito secondario)**</p> | <p>**Replicare macchine virtuali VMware * *</p><p>**(ad Azure)****</p>
---|---|---|---|---
Active Directory, DNS | S | S | S | S
app Web (IIS, SQL) | S | S | S | S
SCOM | S | S | S | S
SharePoint | S | S | S | S
<p>SAP </p><p>Replicare sito SAP in Azure per non cluster</p> | S (testato da Microsoft) | S (testato da Microsoft) | S (testato da Microsoft) | S (testato da Microsoft)
Exchange (non DAG) | S | Presto disponibile | S | S
Desktop remoto/VDI | S | S | S | N/D
<p>Linux</p> <p>(sistema operativo e app) </p> | S (testato da Microsoft) | S (testato da Microsoft) | S (testato da Microsoft) | S (testato da Microsoft)
Dynamics AX | S | S | S | S
Dynamics CRM | S | Presto disponibile | S | Presto disponibile
Oracle | S (testato da Microsoft) | S (testato da Microsoft) | S (testato da Microsoft) | S (testato da Microsoft)
File server Windows | S | S | S | S

##Active Directory e DNS

Tutte le applicazioni aziendali, ad esempio SharePoint, Dynamics AX e SAP, dipendono dall'infrastruttura di AD e DNS per funzionare correttamente. Quando si crea una soluzione di ripristino di emergenza per una di queste applicazioni, nel caso di un'interruzione è importante proteggere e ripristinare AD prima della riattivazione degli altri componenti dell'applicazione.

Con Azure Site Recovery è possibile creare un piano di ripristino di emergenza completo e automatizzato per AD. Se nel sito primario è disponibile una singola istanza di AD per più applicazioni, ad esempio SharePoint e SAP, e si sceglie di eseguire il failover dell'intero sito, è possibile farlo prima con AD usando un piano di ripristino di AD e quindi procedere al failover delle altre applicazioni usando piani ripristino specifici di quelle applicazioni.

Per istruzioni dettagliate sulla [distribuzione di Azure Site Recovery per AD](http://aka.ms/asr-ad), vedere il documento collegato.

##SQL Server
Microsoft SQL Server costituisce la base per molte applicazioni line-of-business proprietarie, di terze parti e personalizzate di livello aziendale eseguite all'interno del data center locale di un cliente. Applicazioni come SharePoint, Dynamics e SAP usano SQL Server per fornire i servizi dati. Azure Site Recovery e le tecnologie di disponibilità elevata e ripristino di emergenza di SQL Server sono complementari e possono essere usate per fornire la protezione end-to-end per le applicazioni aziendali multilivello. Azure Site Recovery offre i vantaggi seguenti per gli ambienti SQL Server:

- Proteggere facilmente i server SQL autonomi o in cluster in Azure o in un sito secondario. Estendere una soluzione di ripristino di emergenza semplice ed economica per qualsiasi versione ed edizione di SQL Server.
- Integrarsi con i gruppi di disponibilità di SQL AlwaysOn, una soluzione di ripristino di emergenza avanzata, e gestire l'operazione di failover/failback con i piani di ripristino di ASR.
- Creare un piano di ripristino end-to-end per l'intero stack di applicazioni, inclusi i database SQL.
- Usare Azure Site Recovery per aumentare il numero di istanze di SQL Server durante i carichi di picco "espandendoli" in VM IaaS di dimensioni maggiori in Azure.
- Usare Azure Site Recovery per creare copie di test di SQL Server in Azure o in un sito secondario. Usare questa copia per le analisi e per i controlli di conformità dei dati, senza influire sull'ambiente di produzione.

Per istruzioni dettagliate sulla [distribuzione di Azure Site Recovery per SQL](http://aka.ms/asr-sql), vedere il documento collegato.

##SharePoint
SharePoint è un'applicazione comune che consente alle organizzazioni di collaborare, fornendo portali Intranet, gestione documenti e file, social network, siti Web e funzionalità di ricerca contenuti aziendali. È anche una piattaforma applicativa che facilita la distribuzione di applicazioni personalizzate e flussi di lavoro. Con Azure Site Recovery per SharePoint è possibile:

- Eliminare l'esigenza e i costi di infrastruttura associati per una farm in standby per il ripristino di emergenza. Con ASR è possibile abilitare la protezione dell'intera farm (livelli Web, app e database) in Azure o in un sito secondario.
- Semplificare la distribuzione dell'applicazione e la gestibilità. Gli aggiornamenti distribuiti al sito primario vengono replicati automaticamente e sono disponibili quando si ripristina la farm nel sito secondario. In questo modo si eliminano le complessità di gestione per mantenere una farm in standby aggiornata, riducendo anche il costo totale di proprietà.
- Facilitare la distribuzione e i test dell'applicazione SharePoint creando una copia su richiesta simile a quella in produzione per i test e il debug.
- Semplificare il percorso verso il cloud usando ASR per la migrazione delle distribuzioni di SharePoint in Azure.

Per istruzioni dettagliate sulla [distribuzione di Azure Site Recovery per SharePoint](http://aka.ms/asr-sharepoint), vedere il documento collegato.


##Dynamics AX
Microsoft Dynamics AX è una soluzione all'avanguardia per la pianificazione delle risorse aziendali (ERP, Enterprise Resource Planning) semplice da apprendere e usare, che consente di fornire valore aggiunto più rapidamente, sfruttare le opportunità commerciali e favorire il coinvolgimento degli utenti e l'innovazione in tutta l'organizzazione.

- Con ASR è possibile abilitare la protezione della soluzione Dynamics Ax (livelli Web e AOS, database e SharePoint) in Azure o in un sito secondario.
- Semplificare il percorso verso il cloud usando ASR per la migrazione delle distribuzioni di Dynamics Ax in Azure.
- Facilitare la distribuzione e i test dell'applicazione Dynamics creando una copia su richiesta simile a quella in produzione per i test e il debug.

Per istruzioni dettagliate sulla [distribuzione di Azure Site Recovery per Dynamics AX](http://aka.ms/asr-dynamics), vedere il documento collegato.

## Servizi desktop remoto 
Servizi Desktop remoto di Windows Server velocizza ed estende le distribuzioni di desktop e applicazioni a qualsiasi dispositivo, migliorando l'efficienza dei lavoratori remoti e proteggendo la proprietà intellettuale critica. Semplifica anche il rispetto della conformità alle normative. Servizi Desktop remoto abilita l'uso di un'infrastruttura VDI, di desktop basati su sessione e di applicazioni, consentendo agli utenti di lavorare ovunque.

Con ASR è possibile abilitare la protezione di desktop virtuali in pool gestiti o non gestiti in un sito secondario, oltre che di applicazioni e sessioni remote in un sito secondario o in Azure.

Per istruzioni dettagliate sulla [distribuzione di Azure Site Recovery per Servizi Desktop remoto/VDI](http://aka.ms/asr-rds), vedere il documento collegato.


## Exchange
Microsoft Exchange è il software preferito usato dalle aziende per ospitare i servizi di messaggistica e posta elettronica. Exchange assicura l'accesso alle comunicazioni su PC, telefono o browser, offrendo un livello ineguagliato di affidabilità, gestibilità e protezione.

Microsoft Exchange supporta in modalità nativa soluzioni a disponibilità elevata e di ripristino di emergenza di livello aziendale. I gruppi di disponibilità del database di Exchange e le tecnologie Azure Site Recovery sono complementari.

- I gruppi di disponibilità del database di Exchange rappresentano l'opzione di distribuzione consigliata per abilitare il miglior livello di ripristino di emergenza per le distribuzioni di Exchange. I piani di ripristino di ASR possono includere i gruppi di disponibilità del database per orchestrarne i failover tra più siti.
- Per le piccole distribuzioni, ad esempio un singolo server o server non in cluster, Azure Site Recovery consente la protezione e il failover di singoli server in Azure o in un sito secondario.

Per istruzioni dettagliate sulla [distribuzione di Azure Site Recovery per Exchange](http://aka.ms/asr-exchange), vedere il documento collegato.

## SAP

SAP è un software all'avanguardia per la pianificazione delle risorse aziendali (ERP, Enterprise Resource Planning) usato da numerose organizzazioni in tutto il mondo. SAP è spesso considerata una delle applicazioni più cruciali nelle aziende. L'architettura e le operazioni di queste applicazioni sono in genere molto complesse ed è quindi molto importante assicurare il rispetto dei requisiti di ripristino di emergenza e continuità aziendale.

- Con ASR è possibile abilitare la protezione dell'intera distribuzione SAP con diversi livelli in Azure o in un sito secondario.
- Semplificare il percorso verso il cloud usando ASR per la migrazione delle distribuzioni di SAP in Azure.
- Facilitare la distribuzione e i test dell'applicazione SAP creando una copia su richiesta simile a quella in produzione per i test e il debug.

Per istruzioni dettagliate sulla [distribuzione di Azure Site Recovery per SAP NetWeaver](http://aka.ms/asr-sap), vedere il documento collegato.

<!---HONumber=Oct15_HO3-->