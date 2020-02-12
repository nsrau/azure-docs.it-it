---
title: Migrazione di SAP HANA in Azure (istanze large) a macchine virtuali di Azure | Microsoft Docs
description: Come eseguire la migrazione di SAP HANA in Azure (istanze large) in macchine virtuali di Azure
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dd01bf60104939fcf1f9bd1ccec0e7d72710041
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154920"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA sulla migrazione di istanze large di Azure alle macchine virtuali di Azure
Questo articolo descrive i possibili scenari di distribuzione di istanze large di Azure e offre un approccio di pianificazione e migrazione con tempi di inattività della transizione minimi

## <a name="overview"></a>Panoramica
Poiché l'annuncio delle istanze di grandi dimensioni di Azure per SAP HANA (HLI) nel 2016 settembre, molti clienti hanno adottato questo hardware come servizio per la piattaforma di calcolo in memoria.  Negli ultimi anni, l'estensione per la scalabilità delle VM di Azure combinata con il supporto del modello di distribuzione con scalabilità orizzontale di HANA ha superato la richiesta di capacità del database ERP dei clienti aziendali.  Si inizia a vedere i clienti che esprimono la volontà di migrare il carico di lavoro SAP HANA da server fisici ad macchine virtuali di Azure.
Questa guida non è un documento di configurazione dettagliata, bensì descrive i modelli di distribuzione comuni e offre una pianificazione e la migrazione consiglia di richiamare le considerazioni necessarie per la preparazione al fine di ridurre al minimo i tempi di inattività della transizione.

## <a name="assumptions"></a>Presupposti
L'articolo presuppone quanto segue:
- L'unico interesse considerato è una migrazione del servizio di calcolo del database HANA omogeneo dalle istanze large di Hana (HLI) alla macchina virtuale di Azure senza l'aggiornamento o l'applicazione di patch significative. Questi aggiornamenti secondari includono l'uso di una versione più recente del sistema operativo o di una versione di HANA esplicitamente specificata come supportata dalle note SAP pertinenti. 
- Tutte le attività di aggiornamenti/aggiornamenti, se necessario, devono essere eseguite prima o dopo la migrazione.  Ad esempio, SAP HANA MCOS la conversione nella distribuzione di MDC. 
- L'approccio alla migrazione che offrirebbe il minor tempo di inattività è SAP HANA la replica di sistema. Altri metodi di migrazione non fanno parte dell'ambito di questo documento.
- Questa operazione è applicabile sia per gli SKU rev3 che rev4 di HLI.
- L'architettura di distribuzione HANA rimane principalmente invariata durante la migrazione.  In altre termini, un sistema con una sola istanza di ripristino di emergenza resterà nello stesso modo nella destinazione.
- I clienti hanno rivisto e compreso il Contratto di servizio (SLA) dell'architettura di destinazione (to-be). 
- A causa delle differenze nei termini commerciali tra HLIs e VM, i clienti dovrebbero monitorare l'uso delle macchine virtuali per la gestione dei costi.
- I clienti comprendono e riconoscono che HLI è una piattaforma di calcolo dedicata. Tuttavia, le macchine virtuali vengono eseguite nell'infrastruttura condivisa, che è sicura e isolata dagli altri tenant.
- I clienti hanno convalidato che le macchine virtuali di destinazione supportano l'architettura desiderata. Per visualizzare tutti gli SKU di VM supportati certificati per la distribuzione di SAP HANA, controllare la [directory hardware SAP Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- I clienti hanno convalidato il piano di progettazione e migrazione.
- Pianificare il nodo di ripristino di emergenza insieme al sito primario.  I clienti non saranno in grado di usare il nodo ripristino di emergenza HLI per il sito primario in esecuzione sulle VM dopo la migrazione.
- I clienti hanno copiato i file di backup necessari per le macchine virtuali di destinazione, in base ai requisiti di conformità e ripristino delle aziende. Questo consente di coprire il recupero temporizzato necessario durante il periodo di transizione.
- Per la disponibilità elevata di HSR, i clienti devono installare e configurare il dispositivo STONITH per ogni SAP HANA guide a disponibilità elevata per [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) e [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker).  Non è preconfigurato come il caso di HLI.
- Questo approccio alla migrazione non copre gli SKU HLI con la configurazione di Optane.

## <a name="deployment-scenarios"></a>Scenari di distribuzione
I modelli di distribuzione comuni con i clienti di HLI sono riepilogati nella tabella seguente.  È possibile eseguire la migrazione alle macchine virtuali di Azure per tutti gli scenari HLI.  Alcuni scenari possono richiedere una lieve modifica all'architettura per ottenere i vantaggi delle attuali offerte di servizi di Azure.

| ID dello scenario | Scenario HLI | Eseguire la migrazione alla VM Verbatim? | Commento |
| --- | --- | --- | --- |
| 1 | [Singolo nodo con un SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | Sì | - |
| 2 | [Singolo nodo con MCOS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | Sì | - |
| 3 | [Singolo nodo con ripristino di emergenza che usa la replica di archiviazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | No | La replica di archiviazione non è disponibile con la piattaforma virtuale di Azure, modificare la soluzione di ripristino di emergenza corrente in HSR o backup/restore |
| 4 | [Singolo nodo con ripristino di emergenza (Multipurpose) con replica di archiviazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | No | La replica di archiviazione non è disponibile con la piattaforma virtuale di Azure, modificare la soluzione di ripristino di emergenza corrente in HSR o backup/restore |
| 5 | [HSR con STONITH per la disponibilità elevata](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | Sì | Nessun SBD preconfigurato per le macchine virtuali di destinazione.  Selezionare e distribuire una soluzione STONITH.  Opzioni possibili: agente di schermatura di Azure (supportato per [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker), [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)), SBD |
| 6 | [Disponibilità elevata con HSR, ripristino di emergenza con replica di archiviazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | No | Sostituire la replica di archiviazione per le esigenze di ripristino di emergenza con HSR o backup/ripristino |
| 7 | [Failover automatico dell'host (1 + 1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | Sì | Usare e per l'archiviazione condivisa con macchine virtuali di Azure |
| 8 | [Scalabilità orizzontale con standby](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | Sì | BW/4HANA con M128s, M416s, M416ms VM che usano e solo per l'archiviazione |
| 9 | [Scalabilità orizzontale senza standby](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | Sì | BW/4HANA con M128s, M416s, M416ms VM (con o senza usare e per l'archiviazione) |
| 10 | [Scalabilità orizzontale con ripristino di emergenza tramite la replica di archiviazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | No | Sostituire la replica di archiviazione per le esigenze di ripristino di emergenza con HSR o backup/ripristino |
| 11 | [Singolo nodo con ripristino di emergenza con HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | Sì | - |
| 12 | [HSR a nodo singolo al ripristino di emergenza (costo ottimizzato)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | Sì | - |
| 13 | [Disponibilità elevata e ripristino di emergenza con HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | Sì | - |
| 14 | [Disponibilità elevata e ripristino di emergenza con HSR (costo ottimizzato)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Sì | - |
| 15 | [Scalabilità orizzontale con il ripristino di emergenza con HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | Sì | BW/4HANA con M128s. M416s, M416ms VM (con o senza usare e per l'archiviazione) |


## <a name="source-hli-planning"></a>Pianificazione origine (HLI)
Quando si carica un server HLI, la gestione dei servizi Microsoft e i clienti hanno eseguito la pianificazione delle impostazioni di calcolo, rete, archiviazione e specifiche del sistema operativo per l'esecuzione del database SAP HANA.  Per la migrazione alla macchina virtuale di Azure è necessario eseguire una pianificazione simile.

### <a name="sap-hana-housekeeping"></a>SAP HANA pulizie 
Prima di eseguire la migrazione del database HANA, è consigliabile riordinare il contenuto del database in modo che i dati non desiderati, quelli obsoleti o i log non aggiornati non vengano migrati nel nuovo database.  Le pulizie in genere implicano l'eliminazione o l'archiviazione di dati obsoleti, scaduti o inattivi.  Di conseguenza, le azioni di igiene dei dati devono essere testate nei sistemi non di produzione per convalidare la validità della riduzione dei dati prima dell'utilizzo di produzione.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Consenti la connettività di rete per le nuove macchine virtuali e o per la rete virtuale 
Nella distribuzione HLI di un cliente è stata stabilita la connettività di rete da Azure reti virtuali in base alle informazioni descritte nell'articolo [SAP Hana architettura di rete (istanze large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Il routing del traffico di rete viene inoltre eseguito in modo analogo a quanto descritto nella sezione "routing in Azure".
1. Per configurare una nuova macchina virtuale come destinazione HSR per la migrazione, se la nuova VM di Azure viene inserita nella rete virtuale esistente con intervalli di indirizzi IP, che dispongono già dell'autorizzazione per la connessione al nodo HLI, non è richiesto alcun ulteriore aggiornamento della connettività HLI
2. Se la nuova VM di Azure è posizionata in una nuova rete virtuale (potrebbe trovarsi in un'altra area) e con peering con la rete virtuale esistente, è possibile usare la chiave del servizio ExpressRoute e l'ID di risorsa del provisioning HLI originale per consentire l'accesso a questo nuovo intervallo IP di rete virtuale.  Coordinarsi con gestione dei servizi Microsoft per consentire alla rete virtuale di HLI la connettività.  Nota: per ridurre al minimo la latenza di rete tra i livelli dell'applicazione e del database, i livelli dell'applicazione e del database devono trovarsi nella stessa rete virtuale.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group"></a>Set di disponibilità del livello app esistente, zone di disponibilità e gruppo di posizionamento vicino
Il modello di distribuzione corrente viene eseguito per soddisfare determinati obiettivi del livello di servizio.  In questo spostamento, assicurarsi che l'infrastruttura di destinazione soddisfi o superi gli obiettivi impostati.  
Più probabilmente, i server applicazioni SAP dei clienti vengono inseriti in un set di disponibilità.  Se il livello di servizio di distribuzione corrente è soddisfacente e 
- Se la sostituzione del server SAP HANA viene eseguita tramite la risoluzione dei nomi scambiando gli indirizzi IP per il nome host del database, non è necessario eseguire altre operazioni.  
- Se non si usa PPG, assicurarsi di collocare tutti i server applicazioni e di database nella stessa area per ridurre al minimo la latenza di rete.
- Se si usa PPG, fare riferimento alla sezione relativa alla pianificazione della destinazione del documento, al set di disponibilità, alla zone di disponibilità e al gruppo di posizionamento vicino (PPG)

### <a name="storage-replication-discontinuance-process-if-used"></a>Processo di discontinuità della replica di archiviazione (se usato)
Se viene usata la replica di archiviazione come soluzione di ripristino di emergenza, la replica deve essere terminata (non pianificata) dopo che l'applicazione SAP è stata arrestata e l'ultimo catalogo SAP HANA, il file di log e i backup dei dati sono stati replicati nei volumi di archiviazione remota.  Questa azione è una tattica precauzione in cui è presente un database corrente nel HLI di ripristino di emergenza nel caso in cui si verifichi un'emergenza durante la transizione fisica alla macchina virtuale di Azure.

### <a name="data-backups-preservation-consideration"></a>Considerazioni sulla conservazione dei backup di dati
Dopo la SAP HANA di cutover in una macchina virtuale di Azure, tutti i backup di dati o log basati su snapshot in HLI non sono facilmente accessibili o ripristinabili in una macchina virtuale, se necessario. Per il periodo di transizione anticipato, prima che il backup basato su Azure crei una cronologia sufficiente per soddisfare i requisiti di ripristino temporizzato, è consigliabile eseguire backup a livello di file oltre agli snapshot in HLI, giorni/settimane prima di cutover.  Fare in modo che questi backup vengano copiati in un account di archiviazione di Azure accessibile dalla nuova VM SAP HANA. Oltre a eseguire il backup del contenuto di HLI, è consigliabile fare in modo che i backup completi del panorama applicativo SAP siano facilmente accessibili nel caso in cui sia necessario eseguire il rollback.

### <a name="adjusting-system-monitoring"></a>Regolazione del monitoraggio del sistema 
I clienti usano molti strumenti diversi per monitorare e inviare notifiche di avviso per i sistemi all'interno dell'ambiente SAP.  Questo elemento è semplicemente una semplice chiamata per l'azione appropriata quando si aggiungono le nuove macchine virtuali che incorporano qualsiasi adattamento per il monitoraggio e si aggiornano i destinatari delle notifiche di avviso, se necessario.

### <a name="microsoft-operations-team-involvement"></a>Coinvolgimento del team operativo Microsoft 
Aprire un ticket dal portale di Azure basi nell'istanza di HLI esistente.  Dopo aver creato il ticket di supporto, un tecnico del supporto contatterà l'utente tramite posta elettronica.  

### <a name="engage-microsoft-account-team"></a>Coinvolgere il team dell'account Microsoft
Pianificare la migrazione vicino al tempo di rinnovo dell'anniversario del contratto HLI per ridurre al minimo la spesa necessaria per le risorse di calcolo. Per rimuovere le autorizzazioni del pannello HLI, è necessario coordinare la terminazione del contratto e l'arresto effettivo dell'unità.

## <a name="destination-planning"></a>Pianificazione della destinazione
La creazione di una nuova infrastruttura per la sostituzione di una nuova infrastruttura merita una certa idea per garantire che la nuova aggiunta rientri nel grande schema di cose.  Di seguito sono riportati alcuni punti chiave per la contemplazione.

### <a name="resource-availability-in-the-target-region"></a>Disponibilità di risorse nell'area di destinazione 
L'area di distribuzione del server applicazioni SAP corrente si trova in genere in prossimità del HLIs associato.  Tuttavia, i HLIs vengono offerti in un minor numero di posizioni rispetto alle aree di Azure disponibili.  Quando si esegue la migrazione dal HLI fisico alla macchina virtuale di Azure, viene presentata l'opportunità di "ottimizzare" la distanza di prossimità di tutti i servizi correlati per ottimizzare le prestazioni.  Quando si esegue questa operazione, è opportuno assicurarsi che l'area scelta abbia le risorse di interesse.  Ad esempio, la disponibilità di una determinata famiglia di VM o l'offerta di zone di Azure per la valutazione della disponibilità elevata.

### <a name="virtual-network"></a>Rete virtuale 
L'architetto dell'infrastruttura avrà la possibilità di scegliere se eseguire il nuovo database HANA all'interno della rete virtuale dell'applicazione SAP esistente o crearne uno nuovo.  Il fattore decisivo principale è il layout di rete corrente per il panorama applicativo SAP.  Nel caso in cui venga presa in considerazione una modifica dell'infrastruttura di distribuzione, ad esempio, passare da una distribuzione a una zona a due zone e sfruttare i vantaggi di PPG. Questo miglioramento della disponibilità impone una modifica dell'architettura. Per altre informazioni, vedere l'articolo [di Azure PPG per la latenza di rete ottimale con l'applicazione SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).   

### <a name="security"></a>Sicurezza
Se la nuova macchina virtuale SAP HANA che si trova in una VNET/subnet nuova o esistente, rappresenta un nuovo servizio aziendale cruciale che richiede la protezione.  Il controllo di accesso appropriato conforme ai criteri di sicurezza delle informazioni aziendali per la nuova classe di servizio deve essere valutato e distribuito. 

### <a name="vm-sizing-recommendation"></a>Raccomandazione per il dimensionamento delle VM
Questa migrazione è anche un'opportunità di ridimensionare il motore di calcolo HANA.  Uno può sfruttare le [visualizzazioni di sistema](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) Hana insieme a Hana Studio per comprendere l'utilizzo delle risorse di sistema, che consente di dimensionare correttamente l'efficienza di spesa.

### <a name="storage"></a>Archiviazione 
Le prestazioni di archiviazione sono uno dei fattori che influiscono sull'esperienza utente dell'applicazione SAP.  Base su uno SKU di VM specificato, è stata pubblicata una raccomandazione per il layout di archiviazione minima [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). È consigliabile esaminare queste specifiche minime e confrontate con le statistiche di sistema HLI esistenti per garantire la capacità di i/o e le prestazioni adeguate per la nuova macchina virtuale HANA.

Se si configura PPG per la nuova macchina virtuale HANA e il relativo valore, inviare un ticket di supporto per controllare e garantire la collocazione dell'archiviazione e della VM HANA.  
Poiché potrebbe essere necessario modificare la soluzione di backup, è necessario rivisitare anche il costo di archiviazione per evitare sorprese di spesa operative. 

### <a name="storage-replication-for-disaster-recovery"></a>Replica di archiviazione per il ripristino di emergenza
In HLI, la replica di archiviazione è stata offerta come opzione predefinita per la replica di ripristino di emergenza per il database HANA. Questa funzionalità non è l'opzione predefinita con la macchina virtuale di Azure. Prendere in considerazione HSR, backup/ripristino o altre soluzioni supportate che soddisfino le esigenze aziendali.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Set di disponibilità, zone di disponibilità e gruppi di posizionamento di prossimità 
Per ridurre al minimo la distanza tra il livello dell'applicazione e SAP HANA per tenere almeno la latenza di rete, la nuova VM del database e i server applicazioni SAP correnti devono essere posizionati in un gruppo di posizionamento vicino (PPG). Per informazioni, vedere il [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) di Azure e il zone di disponibilità usare PPG per le distribuzioni SAP.
Se i membri del sistema HANA di destinazione vengono distribuiti in più aree di Azure, i clienti devono avere una visualizzazione chiara del profilo di latenza delle zone scelte. Il posizionamento dei componenti di sistema SAP è ottimale in relazione alla distanza prossimale tra l'applicazione SAP e il database.  Lo strumento di [test della latenza della zona di disponibilità](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) del dominio pubblico semplifica la misurazione.  


### <a name="backup-strategy"></a>Strategia di backup
Molti clienti usano già soluzioni di backup di terze parti per SAP HANA in HLI.  In tal caso, è necessario configurare solo un'altra VM protetta e database HANA.  I processi di backup HLI in corso per ora non vengono pianificati se il computer è in fase di rimozione delle autorizzazioni dopo la migrazione.
Backup di Azure per SAP HANA nella VM è ora disponibile a livello generale.  Vedere questi collegamenti per informazioni dettagliate su: [backup](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database), [ripristino](https://docs.microsoft.com/azure/backup/sap-hana-db-restore), [gestione](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) di SAP Hana backup nelle VM di Azure.

### <a name="dr-strategy"></a>Strategia di ripristino di emergenza
Se gli obiettivi del livello di servizio supportano un tempo di recupero più lungo di un backup semplice nell'archiviazione BLOB e il ripristino sul posto o in una nuova macchina virtuale, è la strategia di ripristino di emergenza più semplice e meno costosa.  
Analogamente alla piattaforma per istanze di grandi dimensioni in cui HANA DR viene in genere eseguito con HSR; Nella macchina virtuale di Azure, HSR è anche la soluzione di ripristino di emergenza più naturale e nativa SAP HANA.  Indipendentemente dal fatto che la distribuzione di origine sia a istanza singola, in cluster con o senza failover automatico o con scalabilità orizzontale di più nodi HANA, è necessaria una replica HSR di destinazione dell'infrastruttura di origine nell'area di ripristino di emergenza. Questa replica di ripristino di emergenza della destinazione HSR verrà stabilita dopo il completamento della migrazione da HLI primaria a macchina virtuale.  L'istanza di DR HANA viene registrata nel SAP HANA primario nell'istanza di macchina virtuale come sito di replica secondaria.  

### <a name="sap-application-server-connectivity-destination-change"></a>Modifica destinazione connettività server applicazioni SAP
L'approccio alla migrazione di HSR produce un nuovo host del database HANA e pertanto un nuovo nome host del database per il livello dell'applicazione, i profili SAP devono essere modificati per riflettere il nuovo nome host.  Se l'opzione viene eseguita con la risoluzione dei nomi che conserva il nome host, non è richiesta alcuna modifica del profilo.

### <a name="operating-system"></a>Sistema operativo
Le immagini del sistema operativo per HLI e VM, anche se hanno lo stesso livello di versione, SLES 11 SP4, ad esempio, non sono identiche. I clienti devono convalidare i pacchetti necessari, le correzioni rapide, le patch, il kernel e le correzioni per la sicurezza in HLI, in modo che possano essere installate nel sistema operativo della macchina virtuale di destinazione.  Inoltre, è comune per i clienti configurare una versione più recente del sistema operativo nella macchina virtuale di destinazione per SAP HSR.  Supportato per la [Nota SAP 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Nuova richiesta di licenza SAP
Una semplice chiamata per richiedere una nuova licenza SAP per il nuovo sistema HANA ora che è stata migrata alle macchine virtuali.

### <a name="service-level-agreement-sla-differences"></a>Differenze del contratto di servizio (SLA)
Gli autori desiderano richiamare la differenza del contratto di contratto di disponibilità tra HLI e la macchina virtuale di Azure.  Ad esempio, le coppie a disponibilità elevata cluster HLIs offrono una disponibilità del 99,99%. Per ottenere lo stesso contratto di contratto, è necessario distribuire le macchine virtuali nelle zone di disponibilità. Questo [articolo](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) descrive la disponibilità con le architetture di distribuzione associate, in modo che i clienti possano pianificare l'infrastruttura di destinazione di conseguenza.


## <a name="migration-strategy"></a>Strategia di migrazione
Questo documento include solo l'approccio di replica di sistema HANA per la migrazione da HLI alla macchina virtuale di Azure.  Dipende dalla soluzione di archiviazione di destinazione distribuita, il processo è leggermente diverso. Di seguito sono descritti i passaggi di alto livello.

### <a name="vm-with-premiumultra-disks-for-data"></a>VM con dischi Premium/ultra-per i dati
Per le macchine virtuali distribuite con dischi Premium o Ultra, viene applicata la configurazione della replica di sistema SAP HANA standard e può essere usata per configurare HSR.  L' [articolo della Guida di SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) a cui viene fatto riferimento fornisce una panoramica dei passaggi necessari per la configurazione della replica di sistema tra due sistemi, il passaggio a un sistema secondario, il failback a un sistema primario e la disabilitazione della replica di sistema.  Ai fini della migrazione, è necessaria solo la configurazione, l'acquisizione e la disabilitazione della replica di sistema nei passaggi dell'origine HLI.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM con e per i volumi di dati e di log
A livello generale, gli snapshot di archiviazione HLI più recenti dei volumi completi di dati e log devono essere copiati in archiviazione di Azure, dove sono accessibili e recuperabili dalla VM HANA di destinazione.  Il processo di copia può essere eseguito con tutti gli strumenti di copia nativi di Linux.  

>[!Important]
> La copia e il trasferimento dei dati possono richiedere ore in base alle dimensioni del database HANA e alla larghezza di banda di rete.  La maggior parte del processo di copia deve essere eseguita in anticipo del tempo di inattività del database HANA primario.

### <a name="mcos-to-mdc-conversion"></a>Conversione da MCOS a MDC
Il modello di distribuzione di più componenti in un sistema (MCOS) è stato scelto da alcuni clienti di HLI.  La motivazione era aggirare la limitazione dello snapshot di archiviazione di più database (MDC) per le versioni precedenti del SAP HANA.  Nel modello MCOS, diverse istanze di SAP HANA indipendenti vengono impilate in un unico pannello HLI.  L'uso di HSR per la migrazione dovrebbe funzionare in modo corretto con la conseguente presenza di più VM HANA con un database tenant.  Questo stato finale rende un panorama più affollato rispetto a quello che un cliente potrebbe essere abituato a.  Con SAP HANA 2,0 la distribuzione predefinita è MDC, un'alternativa valida consiste nell'eseguire lo [spostamento del tenant Hana](https://launchpad.support.sap.com/#/notes/2472478) dopo la migrazione di HSR.  Questo processo "consolida" questi database HANA indipendenti in cotenant in un unico contenitore HANA.  

### <a name="application-layer-consideration"></a>Considerazioni sul livello dell'applicazione
Il server di database viene visualizzato come centro di un sistema SAP.  Tutti i server applicazioni devono trovarsi vicino al database di SAP HANA.  In alcuni casi, quando si desidera un nuovo uso di PPG, è possibile rilocare i server applicazioni esistenti sul PPG in cui è richiesta la VM HANA.  La creazione di nuovi server applicazioni può essere considerata più semplice se sono già disponibili modelli di distribuzione utili.  
Se i server applicazioni esistenti e la nuova VM HANA sono posizionati in modo ottimale, non è necessario compilare nuovi server applicazioni, a meno che non si desideri una capacità aggiuntiva.  
Se viene compilata una nuova infrastruttura per migliorare la disponibilità dei servizi, i server applicazioni esistenti potrebbero non essere più necessari e devono essere arrestati ed eliminati.
Se il nome host della macchina virtuale di destinazione è stato modificato e differisce dal nome host HLI, è necessario modificare i profili del server applicazioni SAP in modo che puntino al nuovo host.  Se è stato modificato solo l'indirizzo IP del database HANA, è necessario un aggiornamento del record DNS per condurre le connessioni in ingresso alla nuova VM HANA.

### <a name="acceptance-test"></a>Test di accettazione
Sebbene la migrazione da HLI alla macchina virtuale non consenta di apportare modifiche sostanziali al contenuto del database rispetto a una migrazione eterogenea, è comunque consigliabile convalidare le funzionalità chiave e l'aspetto delle prestazioni della nuova installazione.  

### <a name="cutover-plan"></a>Piano cutover
Sebbene la migrazione sia semplice, comporta tuttavia la rimozione delle autorizzazioni di un database esistente.  Un'attenta pianificazione per mantenere il sistema di origine con il contenuto associato e le immagini di backup sono fondamentali nel caso in cui sia necessario il fallback.  Una pianificazione efficace offre un'inversione più rapida.   


## <a name="post-migration"></a>Dopo la migrazione
Il processo di migrazione non viene eseguito fino a quando non si disaccoppiano in modo sicuro i servizi dipendenti da HLI o la connettività per garantire che l'integrità dei dati venga mantenuta.  Arrestare inoltre i servizi non necessari.  In questa sezione vengono riportate alcune voci principali.

### <a name="decommissioning-the-hli"></a>Rimozione delle autorizzazioni di HLI
Al termine della migrazione del database HANA alla macchina virtuale di Azure, non è necessario eseguire alcuna transazione di produzione nel database HLI.  Tuttavia, mantenere il HLI operativo per un periodo uguale all'intervallo di conservazione dei dati di backup locale è una procedura sicura che garantisce un recupero più rapido dei dati, se necessario.  Solo a questo punto è necessario rimuovere le autorizzazioni per il pannello HLI.  Oltre all'impresa tecnica, i clienti che hanno un interesse migliore dovranno concludere contrattualmente gli impegni di HLI con Microsoft.  I clienti devono contattare i rappresentanti Microsoft per collaborare con il processo di rimozione delle autorizzazioni di HLI.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Rimuovere qualsiasi proxy (ad esempio: iptables, BIGIP) configurato per HLI 
Se un servizio proxy come quello di una IPTables viene usato per indirizzare il traffico locale da e verso la HLI, il servizio non è più necessario dopo la migrazione riuscita alla macchina virtuale.  Questo servizio di connettività, tuttavia, deve essere mantenuto per tutto il tempo in cui il pannello HLI è ancora in attesa, come illustrato nell'argomento relativo alla rimozione delle autorizzazioni.  Questo servizio può essere arrestato dopo che il pannello HLI è stato completamente ritirato. 

### <a name="remove-global-reach-for-hli"></a>Rimuovere Copertura globale per HLI 
Copertura globale viene in genere usato per connettere il gateway ExpressRoute del cliente al gateway di HLI ExpressRoute, consentendo al traffico locale del cliente di raggiungere direttamente il tenant HLI senza la necessità di un servizio proxy.  Analogamente al caso del servizio proxy IPTables, GlobalReach deve essere mantenuto finché il pannello HLI non è stato completamente ritirato.

### <a name="operating-system-subscription--movereuse"></a>Sottoscrizione del sistema operativo: spostamento/riutilizzo
Quando vengono rilevati i server VM e vengono rimosse le autorizzazioni per i pannelli HLI, le sottoscrizioni del sistema operativo possono essere sostituite o riutilizzate per evitare il doppio pagamento delle licenze del sistema operativo.



## <a name="next-steps"></a>Passaggi successivi
Vedere i seguenti articoli:
- [SAP Hana le configurazioni e le operazioni dell'infrastruttura in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
- [Carichi di lavoro SAP in Azure: elenco di controllo per la pianificazione e la distribuzione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
