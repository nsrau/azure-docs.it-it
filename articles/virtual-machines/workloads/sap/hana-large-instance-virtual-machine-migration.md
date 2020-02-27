---
title: Migrazione di SAP HANA in Azure (istanze large) a macchine virtuali di Azure | Microsoft Docs
description: Come eseguire la migrazione di SAP HANA in Azure (istanze large) in macchine virtuali di Azure
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd1267711871b3e55f1a6229e46ae27b360322f6
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617048"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA sulla migrazione di istanze large di Azure alle macchine virtuali di Azure
Questo articolo descrive i possibili scenari di distribuzione di istanze large di Azure e offre un approccio di pianificazione e migrazione con tempi di inattività della transizione minimi

## <a name="overview"></a>Panoramica
Poiché l'annuncio delle istanze di grandi dimensioni di Azure per SAP HANA (HLI) nel 2016 settembre, molti clienti hanno adottato questo hardware come offerta di servizio per la piattaforma di calcolo in memoria.  Negli ultimi anni, l'estensione delle dimensioni delle VM di Azure abbinata al supporto della distribuzione con scalabilità orizzontale di HANA ha superato la richiesta di capacità del database ERP dei clienti aziendali.  Si inizierà a visualizzare i clienti che esprimono l'interesse per eseguire la migrazione del carico di lavoro SAP HANA da server fisici ad macchine virtuali di Azure.
Questa guida non è un documento di configurazione dettagliata. Vengono descritti i modelli di distribuzione comuni e vengono offerte consigli per la pianificazione e la migrazione.  Lo scopo è quello di richiamare le considerazioni necessarie per la preparazione per ridurre al minimo i tempi di inattività della transizione.

## <a name="assumptions"></a>Presupposti
L'articolo presuppone quanto segue:
- L'unico interesse considerato è una migrazione del servizio di calcolo del database HANA omogeneo dalle istanze large di Hana (HLI) alla macchina virtuale di Azure senza l'aggiornamento o l'applicazione di patch significative. Questi aggiornamenti secondari includono l'uso di una versione più recente del sistema operativo o di una versione di HANA esplicitamente specificata come supportata dalle note SAP pertinenti. 
- Tutte le attività di aggiornamenti/aggiornamenti devono essere eseguite prima o dopo la migrazione.  Ad esempio, SAP HANA MCOS la conversione nella distribuzione di MDC. 
- L'approccio alla migrazione che offrirebbe il minor tempo di inattività è SAP HANA la replica di sistema. Altri metodi di migrazione non fanno parte dell'ambito di questo documento.
- Questa guida è applicabile sia per gli SKU rev3 che rev4 di HLI.
- L'architettura di distribuzione HANA rimane principalmente invariata durante la migrazione.  In altre termini, un sistema con una sola istanza il ripristino di emergenza resterà nello stesso modo nella destinazione.
- I clienti hanno rivisto e compreso il Contratto di servizio (SLA) dell'architettura di destinazione (to-be). 
- I termini commerciali tra HLIs e le VM sono diversi. I clienti devono monitorare l'uso delle macchine virtuali per la gestione dei costi.
- I clienti comprendono che HLI è una piattaforma di calcolo dedicata mentre le macchine virtuali vengono eseguite in un'infrastruttura condivisa ma isolata.
- I clienti hanno convalidato che le macchine virtuali di destinazione supportano l'architettura desiderata. Per visualizzare tutti gli SKU di VM supportati certificati per la distribuzione di SAP HANA, vedere la [directory hardware SAP Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- I clienti hanno convalidato il piano di progettazione e migrazione.
- Pianificare la VM di ripristino di emergenza insieme al sito primario.  I clienti non possono usare HLI come nodo di ripristino di emergenza per il sito primario in esecuzione sulle VM dopo la migrazione.
- I clienti hanno copiato i file di backup necessari per le macchine virtuali di destinazione, in base ai requisiti di conformità e ripristino delle aziende. Con i backup accessibili per le macchine virtuali, consente il recupero temporizzato durante il periodo di transizione.
- Per la disponibilità elevata di HSR, i clienti devono installare e configurare il dispositivo STONITH per ogni SAP HANA guide a disponibilità elevata per [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) e [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker).  Non è preconfigurato come il caso di HLI.
- Questo approccio alla migrazione non copre gli SKU HLI con la configurazione di Optane.

## <a name="deployment-scenarios"></a>Scenari di distribuzione
I modelli di distribuzione comuni con i clienti di HLI sono riepilogati nella tabella seguente.  È possibile eseguire la migrazione alle macchine virtuali di Azure per tutti gli scenari HLI.  Per sfruttare i vantaggi offerti dai servizi di Azure complementari, potrebbe essere necessario apportare piccole modifiche all'architettura.

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
Si tratta di una procedura operativa efficace per ordinare il contenuto del database in modo che i dati non desiderati, obsoleti o non aggiornati non vengano migrati nel nuovo database.  Il servizio di manutenzione in genere comporta l'eliminazione o l'archiviazione di dati obsoleti, scaduti o inattivi.  Queste azioni di "igiene dei dati" devono essere testate nei sistemi non di produzione per convalidare la validità della riduzione dei dati prima dell'utilizzo di produzione.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Consenti la connettività di rete per le nuove macchine virtuali e o per la rete virtuale 
Nella distribuzione HLI del cliente la rete è stata configurata in base alle informazioni descritte nell'articolo [SAP Hana architettura di rete (istanze large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Il routing del traffico di rete viene inoltre eseguito in modo analogo a quanto descritto nella sezione "routing in Azure".
- Quando si configura una nuova macchina virtuale come destinazione della migrazione, se viene inserita nella rete virtuale esistente con gli intervalli di indirizzi IP già consentiti per la connessione a HLI, non è necessario alcun aggiornamento aggiuntivo della connettività.
- Se la nuova VM di Azure viene inserita in un nuovo Rete virtuale di Microsoft Azure, potrebbe trovarsi in un'altra area e con peering con la rete virtuale esistente, è possibile usare la chiave del servizio ExpressRoute e l'ID di risorsa del provisioning HLI originale per consentire l'accesso per la nuova macchina virtuale intervallo IP di rete.  Coordinarsi con gestione dei servizi Microsoft per consentire alla rete virtuale di HLI la connettività.  Nota: per ridurre al minimo la latenza di rete tra i livelli dell'applicazione e del database, è necessario che i livelli dell'applicazione e del database si trovino nella stessa rete virtuale.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Set di disponibilità del livello app esistente, zone di disponibilità e gruppo di posizionamento vicino (PPG)
Il modello di distribuzione corrente viene eseguito per soddisfare determinati obiettivi del livello di servizio.  In questo spostamento, assicurarsi che l'infrastruttura di destinazione soddisfi o superi gli obiettivi impostati.  
Più probabilmente, i server applicazioni SAP dei clienti vengono inseriti in un set di disponibilità.  Se il livello di servizio di distribuzione corrente è soddisfacente e 
- Se la macchina virtuale di destinazione presuppone il nome host del nome logico HLI, l'aggiornamento della risoluzione degli indirizzi DNS (Domain Name Service) che punta all'indirizzo IP della macchina virtuale funziona senza aggiornare i profili SAP
- Se non si usa PPG, assicurarsi di collocare tutti i server applicazioni e di database nella stessa area per ridurre al minimo la latenza di rete.
- Se si usa PPG, vedere la sezione di questo documento:' pianificazione della destinazione, set di disponibilità, zone di disponibilità e gruppo di posizionamento vicino (PPG)'.

### <a name="storage-replication-discontinuance-process-if-used"></a>Processo di discontinuità della replica di archiviazione (se usato)
Se la replica di archiviazione viene usata come soluzione di ripristino di emergenza, deve essere terminata (non pianificata) dopo che l'applicazione SAP è stata arrestata.  Inoltre, l'ultimo catalogo SAP HANA, il file di log e i backup dei dati sono stati replicati nei volumi remoti di archiviazione HLI di ripristino di emergenza.  Questa operazione viene eseguita come precauzione nel caso in cui si verifichi un'emergenza durante la transizione del server fisico alla macchina virtuale di Azure.

### <a name="data-backups-preservation-consideration"></a>Considerazioni sulla conservazione dei backup di dati
Al termine dell'SAP HANA della macchina virtuale di Azure, tutti i backup di dati o di log basati su snapshot in HLI non sono facilmente accessibili o ripristinabili a una macchina virtuale, se necessario. Nel periodo di transizione anticipato, prima che il backup basato su Azure crei una cronologia sufficiente per soddisfare i requisiti di ripristino temporizzato, è consigliabile eseguire backup a livello di file oltre agli snapshot in HLI, giorni o settimane prima del trasferimento.  Fare in modo che questi backup vengano copiati in un account di archiviazione di Azure accessibile dalla nuova VM SAP HANA.
Oltre a eseguire il backup del contenuto di HLI, è consigliabile avere backup completi del landscape SAP facilmente accessibili nel caso in cui sia necessario eseguire il rollback.

### <a name="adjusting-system-monitoring"></a>Regolazione del monitoraggio del sistema 
I clienti usano molti strumenti diversi per monitorare e inviare notifiche di avviso per i sistemi all'interno dell'ambiente SAP.  Questo elemento è semplicemente una chiamata per l'azione appropriata per incorporare le modifiche per il monitoraggio e aggiornare i destinatari delle notifiche di avviso, se necessario.

### <a name="microsoft-operations-team-involvement"></a>Coinvolgimento del team operativo Microsoft 
Aprire un ticket dal portale di Azure in base all'istanza di HLI esistente.  Dopo aver creato il ticket di supporto, un tecnico del supporto contatterà l'utente tramite posta elettronica.  

### <a name="engage-microsoft-account-team"></a>Coinvolgere account Microsoft Team
Pianificare la migrazione vicino al tempo di rinnovo dell'anniversario del contratto HLI per ridurre al minimo la spesa necessaria per le risorse di calcolo. Per rimuovere le autorizzazioni del pannello HLI, è necessario coordinare la terminazione del contratto e l'arresto effettivo dell'unità.

## <a name="destination-planning"></a>Pianificazione della destinazione
La creazione di una nuova infrastruttura per la sostituzione di una nuova infrastruttura merita una certa idea per garantire che la nuova aggiunta rientri nel grande schema di cose.  Di seguito sono riportati alcuni punti chiave per la contemplazione.

### <a name="resource-availability-in-the-target-region"></a>Disponibilità di risorse nell'area di destinazione 
L'area di distribuzione corrente dei server applicazioni SAP è in genere vicina al HLIs associato.  Tuttavia, i HLIs vengono offerti in un minor numero di posizioni rispetto alle aree di Azure disponibili.  Quando si esegue la migrazione del HLI fisico alla macchina virtuale di Azure, è anche opportuno "ottimizzare" la distanza di prossimità di tutti i servizi correlati per l'ottimizzazione delle prestazioni.  Quando si esegue questa operazione, una considerazione fondamentale consiste nel garantire che l'area scelta disponga di tutte le risorse necessarie.  Ad esempio, la disponibilità di una determinata famiglia di VM o l'offerta di zone di Azure per la configurazione della disponibilità elevata.

### <a name="virtual-network"></a>Rete virtuale 
I clienti devono scegliere se eseguire il nuovo database HANA in una rete virtuale esistente o crearne uno nuovo.  Il fattore decisivo principale è il layout di rete corrente per il panorama applicativo SAP.  Inoltre, quando l'infrastruttura passa dalla distribuzione da una zona a una a due zone e USA PPG, impone una modifica dell'architettura. Per altre informazioni, vedere l'articolo [di Azure PPG per la latenza di rete ottimale con l'applicazione SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).   

### <a name="security"></a>Sicurezza
Se la nuova macchina virtuale SAP HANA che si trova in una VNET/subnet nuova o esistente, rappresenta un nuovo servizio aziendale cruciale che richiede la protezione.  Il controllo di accesso conforme ai criteri di sicurezza delle informazioni aziendali deve essere valutato e distribuito per questa nuova classe di servizio.

### <a name="vm-sizing-recommendation"></a>Raccomandazione per il dimensionamento delle VM
Questa migrazione è anche un'opportunità di ridimensionare il motore di calcolo HANA.  È possibile usare le [viste di sistema](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) Hana insieme a Hana Studio per comprendere l'utilizzo delle risorse di sistema, che consente di dimensionare correttamente l'efficienza di spesa.

### <a name="storage"></a>Archiviazione 
Le prestazioni di archiviazione sono uno dei fattori che influiscono sull'esperienza utente dell'applicazione SAP.  Base su uno SKU di VM specificato, è stato pubblicato un layout di archiviazione minimo [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). È consigliabile esaminare queste specifiche minime e confrontare le statistiche di sistema HLI esistenti per garantire la capacità di i/o e le prestazioni adeguate per la nuova macchina virtuale HANA.

Se si configura PPG per la nuova macchina virtuale HANA e la relativa versione, inviare un ticket di supporto per controllare e verificare la condivisione percorso di archiviazione e della VM. Poiché potrebbe essere necessario modificare la soluzione di backup, è necessario rivisitare anche il costo di archiviazione per evitare sorprese di spesa operative.

### <a name="storage-replication-for-disaster-recovery"></a>Replica di archiviazione per il ripristino di emergenza
Con HLI, la replica di archiviazione è stata offerta come opzione predefinita per il ripristino di emergenza. Questa funzionalità non è l'opzione predefinita per SAP HANA in una macchina virtuale di Azure. Prendere in considerazione HSR, backup/ripristino o altre soluzioni supportate che soddisfino le esigenze aziendali.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Set di disponibilità, zone di disponibilità e gruppi di posizionamento di prossimità 
Per abbreviare la distanza tra il livello dell'applicazione e SAP HANA per ridurre al minimo la latenza di rete, la nuova VM del database e i server applicazioni SAP correnti devono essere inseriti in un PPG. Per informazioni sul funzionamento del set di disponibilità di Azure e zone di disponibilità con PPG per le distribuzioni SAP, vedere il [gruppo di posizionamento di prossimità](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) .
Se i membri del sistema HANA di destinazione vengono distribuiti in più aree di Azure, i clienti devono avere una visualizzazione chiara del profilo di latenza delle zone scelte. Il posizionamento dei componenti di sistema SAP è ottimale in relazione alla distanza prossimale tra l'applicazione SAP e il database.  Lo strumento di [test della latenza della zona di disponibilità](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) del dominio pubblico semplifica la misurazione.  


### <a name="backup-strategy"></a>Strategia di backup
Molti clienti usano già soluzioni di backup di terze parti per SAP HANA in HLI.  In tal caso, è necessario configurare solo un'altra VM protetta e database HANA.  È ora possibile non pianificare i processi di backup HLI in corso se è in corso la rimozione delle autorizzazioni del computer dopo la migrazione.
Backup di Azure per SAP HANA nella VM è ora disponibile a livello generale.  Vedere questi collegamenti per informazioni dettagliate su: [backup](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database), [ripristino](https://docs.microsoft.com/azure/backup/sap-hana-db-restore), [gestione](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) di SAP Hana backup nelle VM di Azure.

### <a name="dr-strategy"></a>Strategia di ripristino di emergenza
Se gli obiettivi del livello di servizio sono più lunghi, un backup semplice per l'archiviazione BLOB e il ripristino sul posto o il ripristino in una nuova macchina virtuale è la strategia di ripristino di emergenza più semplice e meno costosa.  
Analogamente alla piattaforma per istanze di grandi dimensioni in cui HANA DR viene in genere eseguito con HSR; Nella macchina virtuale di Azure, HSR è anche la soluzione di ripristino di emergenza più naturale e nativa SAP HANA.  Indipendentemente dal fatto che la distribuzione di origine sia a istanza singola o in cluster, è necessaria una replica dell'infrastruttura di origine nell'area di ripristino di emergenza.
Questa replica di ripristino di emergenza verrà configurata dopo il completamento della migrazione del HLI primario alla macchina virtuale.  Il database di ripristino di emergenza eseguirà la registrazione al SAP HANA primario nell'istanza di macchina virtuale come sito di replica secondaria.  

### <a name="sap-application-server-connectivity-destination-change"></a>Modifica destinazione connettività server applicazioni SAP
La migrazione HSR produce un nuovo host del database HANA e pertanto un nuovo nome host del database per il livello dell'applicazione, i profili SAP devono essere modificati per riflettere il nuovo nome host.  Se il passaggio viene eseguito tramite la risoluzione dei nomi che conserva il nome host, non è richiesta alcuna modifica del profilo.

### <a name="operating-system"></a>Sistema operativo
Le immagini del sistema operativo per HLI e VM, nonostante si trovino sullo stesso livello di versione, SLES 12 SP4, ad esempio, non sono identiche. Per installare gli stessi pacchetti nella destinazione, i clienti devono convalidare i pacchetti necessari, le correzioni rapide, le patch, il kernel e le correzioni di sicurezza nella HLI.  È supportato l'uso di HSR per eseguire la replica da un sistema operativo precedente a una macchina virtuale con una versione più recente del sistema operativo.  Verificare le versioni supportate specifiche riesaminando la [Nota SAP 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Nuova richiesta di licenza SAP
Una semplice chiamata per richiedere una nuova licenza SAP per il nuovo sistema HANA ora che è stata migrata alle macchine virtuali.

### <a name="service-level-agreement-sla-differences"></a>Differenze del contratto di servizio (SLA)
Gli autori desiderano richiamare la differenza del contratto di contratto di disponibilità tra HLI e la macchina virtuale di Azure.  Ad esempio, le coppie a disponibilità elevata cluster HLIs offrono una disponibilità del 99,99%. Per ottenere lo stesso contratto di contratto, è necessario distribuire le macchine virtuali nelle zone di disponibilità. Questo [articolo](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) descrive la disponibilità con le architetture di distribuzione associate, in modo che i clienti possano pianificare l'infrastruttura di destinazione di conseguenza.


## <a name="migration-strategy"></a>Strategia di migrazione
Questo documento include solo l'approccio di replica di sistema HANA per la migrazione da HLI alla macchina virtuale di Azure.  Dipende dalla soluzione di archiviazione di destinazione distribuita, il processo è leggermente diverso. Di seguito sono descritti i passaggi di alto livello.

### <a name="vm-with-premiumultra-disks-for-data"></a>VM con dischi Premium/ultra-per i dati
Per le macchine virtuali distribuite con dischi Premium o Ultra, la configurazione della replica di sistema SAP HANA standard è applicabile per la configurazione di HSR.  L' [articolo della Guida di SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) fornisce una panoramica dei passaggi necessari per la configurazione della replica di sistema, l'acquisizione di un sistema secondario, il failback al database primario e la disabilitazione della replica di sistema.  Ai fini della migrazione, è necessaria solo la configurazione, l'acquisizione e la disabilitazione dei passaggi di replica.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM con e per i volumi di dati e di log
A livello generale, gli snapshot di archiviazione HLI più recenti dei volumi completi di dati e log devono essere copiati in archiviazione di Azure, dove sono accessibili e recuperabili dalla VM HANA di destinazione.  Il processo di copia può essere eseguito con tutti gli strumenti di copia nativi di Linux.  

> [!IMPORTANT]
> La copia e il trasferimento dei dati possono richiedere ore in base alle dimensioni del database HANA e alla larghezza di banda di rete.  La maggior parte del processo di copia deve essere eseguita in anticipo del tempo di inattività del database HANA primario.

### <a name="mcos-to-mdc-conversion"></a>Conversione da MCOS a MDC
Il modello di distribuzione di più componenti in un sistema (MCOS) è stato usato da alcuni clienti di HLI.  La motivazione era aggirare la limitazione dello snapshot di archiviazione di più database (MDC) per le versioni precedenti del SAP HANA.  Nel modello MCOS, diverse istanze di SAP HANA indipendenti vengono impilate in un unico pannello HLI.  L'uso di HSR per la migrazione dovrebbe funzionare correttamente, ma con la conseguente presenza di più VM HANA con un database tenant.  Questo stato finale rende un panorama più affollato rispetto a quello che un cliente potrebbe essere abituato a.  Con SAP HANA 2,0 la distribuzione predefinita è MDC, un'alternativa valida consiste nell'eseguire lo [spostamento del tenant Hana](https://launchpad.support.sap.com/#/notes/2472478) dopo la migrazione di HSR.  Questo processo "consolida" questi database HANA indipendenti in cotenant in un unico contenitore HANA.  

### <a name="application-layer-consideration"></a>Considerazioni sul livello dell'applicazione
Il server di database viene visualizzato come centro di un sistema SAP.  Tutti i server applicazioni devono trovarsi vicino al database di SAP HANA.  In alcuni casi, quando si desidera un nuovo uso di PPG, è possibile rilocare i server applicazioni esistenti sul PPG in cui è richiesta la VM HANA.  La creazione di nuovi server applicazioni può essere considerata più semplice se sono già disponibili modelli di distribuzione utili.  
Se i server applicazioni esistenti e la nuova VM HANA sono posizionati in modo ottimale, non è necessario compilare nuovi server applicazioni, a meno che non sia necessaria una capacità aggiuntiva.  
Se viene compilata una nuova infrastruttura per migliorare la disponibilità dei servizi, i server applicazioni esistenti potrebbero non essere più necessari e devono essere arrestati ed eliminati.
Se il nome host della macchina virtuale di destinazione è stato modificato e differisce dal nome host HLI, è necessario modificare i profili del server applicazioni SAP in modo che puntino al nuovo host.  Se è stato modificato solo l'indirizzo IP del database HANA, è necessario un aggiornamento del record DNS per condurre le connessioni in ingresso alla nuova VM HANA.

### <a name="acceptance-test"></a>Test di accettazione
Sebbene la migrazione da HLI alla macchina virtuale non consenta di apportare modifiche sostanziali al contenuto del database rispetto a una migrazione eterogenea, è comunque consigliabile convalidare le funzionalità chiave e l'aspetto delle prestazioni della nuova installazione.  

### <a name="cutover-plan"></a>Piano cutover
Sebbene la migrazione sia semplice, comporta tuttavia la rimozione delle autorizzazioni di un database esistente.  Un'attenta pianificazione per mantenere il sistema di origine con il contenuto associato e le immagini di backup sono fondamentali nel caso in cui sia necessario il fallback.  Una pianificazione efficace offre un'inversione più rapida.   


## <a name="post-migration"></a>Dopo la migrazione
Il processo di migrazione non viene eseguito fino a quando non si disaccoppiano in modo sicuro i servizi dipendenti da HLI o la connettività per garantire che l'integrità dei dati venga mantenuta.  Arrestare inoltre i servizi non necessari.  In questa sezione vengono riportate alcune voci principali.

### <a name="decommissioning-the-hli"></a>Rimozione delle autorizzazioni di HLI
Dopo aver completato la migrazione del database HANA alla macchina virtuale di Azure, assicurarsi che non vengano eseguite transazioni aziendali produttive sul database HLI.  Tuttavia, mantenere il HLI in esecuzione per un periodo di tempo uguale all'intervallo di conservazione dei backup locali è una procedura sicura che garantisce un ripristino più rapido, se necessario.  Solo a questo punto è necessario rimuovere le autorizzazioni per il pannello HLI.  I clienti devono concludere contrattualmente i propri impegni HLI con Microsoft contattando i rappresentanti Microsoft.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Rimuovere qualsiasi proxy (ad esempio: iptables, BIGIP) configurato per HLI 
Se un servizio proxy come IPTables viene usato per instradare il traffico locale da e verso il HLI, non è più necessario dopo la migrazione riuscita alla macchina virtuale.  Tuttavia, questo servizio di connettività deve essere mantenuto fino a quando il pannello HLI è ancora in funzione.  Arrestare il servizio solo dopo che il pannello HLI è stato completamente ritirato.

### <a name="remove-global-reach-for-hli"></a>Rimuovere Copertura globale per HLI 
Copertura globale viene usato per connettere il gateway ExpressRoute dei clienti al gateway HLI ExpressRoute.  Consente al traffico locale dei clienti di raggiungere direttamente il tenant HLI senza l'uso di un servizio proxy.  Questa connessione non è più necessaria in assenza dell'unità HLI dopo la migrazione.  Come nel caso del servizio proxy IPTables, GlobalReach deve essere mantenuto anche finché il pannello HLI non è stato completamente ritirato.

### <a name="operating-system-subscription--movereuse"></a>Sottoscrizione del sistema operativo: spostamento/riutilizzo
Quando vengono rilevati i server VM e vengono rimosse le autorizzazioni per i pannelli HLI, le sottoscrizioni del sistema operativo possono essere sostituite o riutilizzate per evitare il doppio pagamento delle licenze del sistema operativo.



## <a name="next-steps"></a>Passaggi successivi
Vedere i seguenti articoli:
- [SAP Hana le configurazioni e le operazioni dell'infrastruttura in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
- [Carichi di lavoro SAP in Azure: elenco di controllo per la pianificazione e la distribuzione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
