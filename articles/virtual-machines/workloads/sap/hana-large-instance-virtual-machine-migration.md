---
title: Migrazione di SAP HANA in Azure (istanze di grandi dimensioni) alle macchine virtuali di Azure Documenti Microsoft
description: Come eseguire la migrazione di SAP HANA in Azure (istanze di grandi dimensioni) alle macchine virtuali di AzureHow to migrate SAP HANA on Azure (Large Instances) to Azure virtual machines
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617048"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>Migrazione di SAP HANA in Azure Large Instance alle macchine virtuali di Azure
Questo articolo descrive i possibili scenari di distribuzione di Azure Large Instance e offre l'approccio di pianificazione e migrazione con tempi di inattività di transizione ridotti al minimo

## <a name="overview"></a>Panoramica
Dall'annuncio delle istanze di Grandi dimensioni di Azure per SAP HANA (HLI) nel settembre 2016, molti clienti hanno adottato questo hardware come servizio per la piattaforma di calcolo in memoria.  Negli ultimi anni, l'estensione delle dimensioni della macchina virtuale di Azure associata al supporto della distribuzione con scalabilità orizzontale HANA ha superato la richiesta di capacità del database ERP dei clienti aziendali.  Iniziamo a vedere i clienti esprimere l'interesse di migrare il carico di lavoro SAP HANA dai server fisici alle macchine virtuali di Azure.We begin to see customers expressing the interest to migrate their SAP HANA workload from physical servers to Azure VMs.
Questa guida non è un documento di configurazione passo-passo. Vengono descritti i modelli di distribuzione comuni e vengono fornite indicazioni per la pianificazione e la migrazione.  Lo scopo è quello di richiamare le considerazioni necessarie per la preparazione per ridurre al minimo i tempi di inattività della transizione.

## <a name="assumptions"></a>Presupposti
L'articolo presuppone quanto segue:
- L'unico interesse considerato è una migrazione omogenea del servizio di calcolo del database HANA da Hana Large Instance (HLI) a VM di Azure senza un aggiornamento o applicazione di patch software significativo. Questi aggiornamenti secondari includono l'uso di una versione del sistema operativo più recente o una versione HANA esplicitamente indicata come supportata dalle note SAP pertinenti. 
- Tutte le attività di aggiornamenti/aggiornamenti devono essere eseguite prima o dopo la migrazione.  Ad esempio, SAP HANA MCOS conversione in distribuzione MDC. 
- L'approccio di migrazione che offrirebbe il minor tempo di inattività è sap HANA System Replication. Altri metodi di migrazione non fanno parte dell'ambito di questo documento.
- Queste indicazioni sono applicabili agli SKU Rev3 e Rev4 di HLI.
- L'architettura di distribuzione HANA rimane invariata principalmente durante la migrazione.  Ovvero, un sistema con ripristino di emergenza a istanza singola rimarrà allo stesso modo nella destinazione.
- I clienti hanno esaminato e compreso il contratto di servizio (SLA) dell'architettura di destinazione (da trattare). 
- I termini commerciali tra HLIs e macchine virtuali sono diversi. I clienti devono monitorare l'utilizzo delle macchine virtuali per la gestione dei costi.
- I clienti capiscono che HLI è una piattaforma di elaborazione dedicata mentre le macchine virtuali vengono eseguite su un'infrastruttura condivisa ma isolata.
- I clienti hanno convalidato che le macchine virtuali di destinazione supportano l'architettura prevista. Per visualizzare tutti gli SKU delle macchine virtuali supportati certificati per la distribuzione SAP HANA, vedere la [directory hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- I clienti hanno convalidato il piano di progettazione e migrazione.
- Pianificare la macchina virtuale di ripristino di emergenza insieme al sito primario.  I clienti non possono usare l'HLI come nodo di ripristino di emergenza per il sito primario in esecuzione nelle macchine virtuali dopo la migrazione.
- I clienti hanno copiato i file di backup necessari per le macchine virtuali di destinazione, in base ai requisiti di conformità e di recuperabilità aziendali. Con i backup accessibili alle macchine virtuali, consente il ripristino temporizzato durante il periodo di transizione.
- Per HSR HA, i clienti devono impostare e configurare il dispositivo STONITH per le guide SAP HANA HA per [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) e [RHEL.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)  Non è preconfigurato come il caso HLI.
- Questo approccio di migrazione non copre gli SKU HLI con configurazione Optane.

## <a name="deployment-scenarios"></a>Scenari di distribuzione
I modelli di distribuzione comuni con i clienti HLI sono riepilogati nella tabella seguente.  La migrazione alle macchine virtuali di Azure per tutti gli scenari HLI è possibile.  Per trarre vantaggio dai servizi complementari di Azure disponibili, potrebbero essere necessarie modifiche minori all'architettura.

| Scenario ID | HLI Scenario | Eseguire la migrazione alla macchina virtuale alla migrazione alla macchina virtuale? | Commento |
| --- | --- | --- | --- |
| 1 | [Nodo singolo con un SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | Sì | - |
| 2 | [Nodo singolo con MCOS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | Sì | - |
| 3 | [Singolo nodo con ripristino di emergenza tramite la replica di archiviazioneSingle node with DR using storage replication](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | No | La replica di archiviazione non è disponibile con la piattaforma virtuale di Azure, modificare la soluzione di ripristino di emergenza corrente in HSR o backup/ripristinoStorage replication is not available with Azure virtual platform, change current DR solution to either HSR or backup/restore |
| 4 | [Singolo nodo con ripristino di emergenza (multiuso) tramite la replica di archiviazioneSingle node with DR (multipurpose) using storage replication](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | No | La replica di archiviazione non è disponibile con la piattaforma virtuale di Azure, modificare la soluzione di ripristino di emergenza corrente in HSR o backup/ripristinoStorage replication is not available with Azure virtual platform, change current DR solution to either HSR or backup/restore |
| 5 | [HSR con STONITH per un'elevata disponibilità](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | Sì | Nessun SBD preconfigurato per le macchine virtuali di destinazione.  Selezionare e distribuire una soluzione STONITH.  Opzioni possibili: Azure Fencing Agent (supportato sia per [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker), [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)), SBD |
| 6 | [HA con HSR, ripristino di emergenza con replica di archiviazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | No | Sostituire la replica di archiviazione per le esigenze di ripristino di emergenza con HSR o backup/ripristino |
| 7 | [Failover automatico dell'host (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | Sì | Usare ANF per l'archiviazione condivisa con macchine virtuali di AzureUse ANF for shared storage with Azure VMs |
| 8 | [Scale-out con standby](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | Sì | BW/4HANA con M128, M416s, M416ms Macchine virtuali che utilizzano ANF solo per l'archiviazione |
| 9 | [Scale-out senza standby](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | Sì | BW/4HANA con M128, M416s, M416ms Macchine virtuali (con o senza utilizzo anF per l'archiviazione) |
| 10 | [Scalabilità orizzontale con ripristino di emergenza tramite la replica di archiviazioneScale-out with DR using storage replication](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | No | Sostituire la replica di archiviazione per le esigenze di ripristino di emergenza con HSR o backup/ripristino |
| 11 | [Singolo nodo con DR con HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | Sì | - |
| 12 | [Singolo nodo da HSR a DR (costo ottimizzato)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | Sì | - |
| 13 | [HA e DR con HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | Sì | - |
| 14 | [HA e DR con HSR (ottimizzato per costi)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Sì | - |
| 15 | [Scalabilità orizzontale con DR con HSRScale-out with DR using HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | Sì | BW/4HANA con M128. M416s, M416ms Macchine virtuali (con o senza utilizzo di ANF per l'archiviazione) |


## <a name="source-hli-planning"></a>Pianificazione delle fonti (HLI)
Durante l'onboarding di un server HLI, sia Microsoft Service Management che i clienti hanno eseguito la pianificazione delle impostazioni specifiche di elaborazione, rete, archiviazione e sistema operativo per l'esecuzione del database SAP HANA.  Per la migrazione alla macchina virtuale di Azure è necessario eseguire una pianificazione simile.

### <a name="sap-hana-housekeeping"></a>Pulizia SAP HANA 
È consigliabile riordinare il contenuto del database in modo che i dati indesiderati e obsoleti o i log non aggiornati vengano migrati nel nuovo database.  Le pulidi comportano in genere l'eliminazione o l'archiviazione di dati obsoleti, scaduti o inattivi.  Queste azioni di "igiene dei dati" devono essere testate in sistemi non di produzione per convalidare la validità del taglio dei dati prima dell'utilizzo della produzione.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Consentire la connettività di rete per le nuove macchine virtuali e, o la rete virtualeAllow network connectivity for new VMs and, or virtual network 
Nella distribuzione HLI di un cliente, la rete è stata configurata in base alle informazioni descritte nell'articolo Architettura di [rete SAP HANA (Large Instances).](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture) Inoltre, il routing del traffico di rete viene eseguito nel modo descritto nella sezione "Routing in Azure".
- Durante la configurazione di una nuova macchina virtuale come destinazione della migrazione, se viene inserita nella rete virtuale esistente con intervalli di indirizzi IP già autorizzati a connettersi all'HLI, non è necessario alcun ulteriore aggiornamento della connettività.
- Se la nuova macchina virtuale di Azure viene inserita in una nuova rete virtuale di Microsoft Azure, può trovarsi in un'altra area ed è stato sottoposto a peered con la rete virtuale esistente, la chiave del servizio ExpressRoute e l'ID risorsa del provisioning HLI originale sono utilizzabili per consentire l'accesso a questa nuova rete virtuale l'intervallo IP di rete.  Coordinarsi con Microsoft Service Management per abilitare la connettività HLI dalla rete virtuale.  Nota: per ridurre al minimo la latenza di rete tra i livelli dell'applicazione e di database, sia l'applicazione che i livelli di database devono trovarsi nella stessa rete virtuale.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Set di disponibilità del livello app esistente, zone di disponibilità e gruppo di posizionamento di prossimità (PPG)
Il modello di distribuzione corrente viene eseguito per soddisfare determinati obiettivi del livello di servizio.  In questo spostamento, verificare che l'infrastruttura di destinazione soddisfi o superi gli obiettivi prefissati.  
Più probabilmente, i server applicazioni SAP dei clienti vengono inseriti in un set di disponibilità.  Se l'attuale livello di servizio di distribuzione è soddisfacente e 
- Se la macchina virtuale di destinazione presuppone il nome host del nome logico HLI, l'aggiornamento della risoluzione dell'indirizzo DNS (Domain Name Service) che punta all'IP della macchina virtuale funzionerebbe senza aggiornare i profili SAP
- Se non si utilizza PPG, assicurarsi di posizionare tutti i server applicazioni e DB nella stessa zona per ridurre al minimo la latenza di rete.
- Se si utilizza PPG, fare riferimento alla sezione di questo documento: 'Pianificazione destinazione, Set di disponibilità, Zone di disponibilità e Gruppo di posizionamento di prossimità (PPG)'.

### <a name="storage-replication-discontinuance-process-if-used"></a>Processo di interruzione della replica di archiviazione (se utilizzato)Storage replication escontinuance process (if used)
Se la replica di archiviazione viene utilizzata come soluzione di ripristino di emergenza, deve essere terminata (non pianificata) dopo l'arresto dell'applicazione SAP.  Inoltre, l'ultimo catalogo SAP HANA, i file di registro e i backup dei dati sono stati replicati nei volumi di archiviazione HLI di ripristino di emergenza remoti.  In questo modo per precauzione nel caso in cui si verifichi un'emergenza durante la transizione tra server fisico e VM di Azure.Doing so a precauzion in case a disaster happens during the physical server to Azure VM transition.

### <a name="data-backups-preservation-consideration"></a>Considerazione della conservazione dei backup dei dati
Dopo il cut-over a SAP HANA in macchina virtuale di Azure, tutti i backup di dati basati su snapshot o log nell'HLI non sono facilmente accessibili o ripristinabili a una macchina virtuale, se necessario. Nel periodo di transizione iniziale, prima che il backup basato su Azure crei una cronologia sufficiente per soddisfare i requisiti di ripristino temporizzato, è consigliabile eseguire backup a livello di file oltre agli snapshot nell'HLI, giorni o settimane prima del cut-over.  Fare in modo che questi backup siano copiati in un account di archiviazione di Azure accessibile dalla nuova macchina virtuale SAP HANA.
Oltre a eseguire il backup del contenuto HLI, è prudente avere backup completi del panorama SAP facilmente accessibili nel caso in cui sia necessario un rollback.

### <a name="adjusting-system-monitoring"></a>Regolazione del monitoraggio del sistema 
I clienti utilizzano molti strumenti diversi per monitorare e inviare notifiche di avviso per i sistemi all'interno del proprio panorama SAP.  Questo elemento è solo un callout per un'azione appropriata per incorporare le modifiche per il monitoraggio e aggiornare i destinatari della notifica di avviso, se necessario.

### <a name="microsoft-operations-team-involvement"></a>Coinvolgimento del team Microsoft Operations 
Aprire un ticket dal portale di Azure in base all'istanza HLI esistente.  Dopo aver creato il ticket di supporto, un tecnico del supporto ti contatterà via e-mail.  

### <a name="engage-microsoft-account-team"></a>Coinvolgi il team dell'account Microsoft
Pianificare la migrazione in prossimità del tempo di rinnovo dell'anniversario del contratto HLI per ridurre al minimo le spese inutili per le risorse di elaborazione. Per rimuovere le autorizzazioni del pannello HLI, è necessario coordinare la risoluzione del contratto e l'arresto effettivo dell'unità.

## <a name="destination-planning"></a>Pianificazione della destinazione
La creazione di una nuova infrastruttura per prendere il posto di una esistente merita un po 'di riflessione per garantire che la nuova aggiunta si adatti al grande schema delle cose.  Di seguito sono riportati alcuni punti chiave per la contemplazione.

### <a name="resource-availability-in-the-target-region"></a>Disponibilità delle risorse nell'area di destinazione 
L'area di distribuzione corrente dei server applicazioni SAP in genere si trova in prossimità delle HLIs associate.  Tuttavia, gli HLI sono offerti in un numero inferiore di località rispetto alle aree di Azure disponibili.  Quando si esegue la migrazione dell'HLI fisico alla macchina virtuale di Azure, è anche consigliabile ottimizzare la distanza di prossimità di tutti i servizi correlati per l'ottimizzazione delle prestazioni.  In tal modo, una considerazione fondamentale consiste nell'assicurare che la regione scelta disponga di tutte le risorse necessarie.  Ad esempio, la disponibilità di una determinata famiglia di macchine virtuali o l'offerta di zone di Azure per la configurazione a disponibilità elevata.

### <a name="virtual-network"></a>Rete virtuale 
I clienti devono scegliere se eseguire il nuovo database HANA in una rete virtuale esistente o crearne uno nuovo.  Il fattore decisivo principale è il layout di rete corrente per il panorama SAP.  Anche quando l'infrastruttura passa da una zona a due zone di distribuzione e utilizza PPG, impone modifiche architettoniche. Per altre informazioni, vedere l'articolo Azure PPG per una latenza di [rete ottimale con l'applicazione SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)   

### <a name="security"></a>Security
Che si tratti del nuovo server SAP HANA VM che atterra su una rete virtuale/subnet nuova o esistente, rappresenta un nuovo servizio business critical che richiede la protezione.  Il controllo di accesso conforme ai criteri di sicurezza delle informazioni aziendali deve essere valutato e distribuito per questa nuova classe di servizi.

### <a name="vm-sizing-recommendation"></a>Raccomandazione per il dimensionamento delle macchine virtualiVM sizing recommendation
Questa migrazione è anche un'opportunità per ridimensionare correttamente il motore di calcolo HANA.  È possibile utilizzare le viste di [sistema](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) HANA in combinazione con HANA Studio per comprendere il consumo di risorse di sistema, che consente il dimensionamento corretto per aumentare l'efficienza della spesa.

### <a name="storage"></a>Archiviazione 
Le prestazioni di archiviazione sono uno dei fattori che influiscono sull'esperienza utente dell'applicazione SAP.  Basandosi su un determinato SKU di VM, sono disponibili configurazioni di [archiviazione della macchina virtuale SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)pubblicate nel layout di archiviazione minimo. È consigliabile esaminare queste specifiche minime e confrontarle con le statistiche di sistema HLI esistenti per garantire una capacità e prestazioni di I/O adeguate per la nuova macchina virtuale HANA.

Se si configura PPG per la nuova macchina virtuale HANA e i server associati, inviare un ticket di supporto per esaminare e garantire la co-ubicazione dell'archiviazione e della macchina virtuale. Poiché potrebbe essere necessario modificare la soluzione di backup, anche il costo di archiviazione deve essere rivisto per evitare sorprese di spesa operativa.

### <a name="storage-replication-for-disaster-recovery"></a>Replica dell'archiviazione per il ripristino di emergenzaStorage replication for disaster recovery
Con HLI, la replica di archiviazione è stata offerta come opzione predefinita per il ripristino di emergenza. Questa funzionalità non è l'opzione predefinita per SAP HANA in Azure VM. Considera HSR, backup/ripristino o altre soluzioni supportate che soddisfano le esigenze aziendali.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Availability Sets, Availability Zones, and Proximity Placement Groups 
Per ridurre la distanza tra il livello dell'applicazione e SAP HANA per mantenere al minimo la latenza di rete, la nuova macchina virtuale del database e i server applicazioni SAP correnti devono essere inseriti in un PPG. Fare riferimento a [Gruppo di posizionamento di prossimità](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) per informazioni sul funzionamento delle aree Set di disponibilità e Disponibilità di Azure con PPG per le distribuzioni SAP.
Se i membri del sistema HANA di destinazione vengono distribuiti in più zone di Azure, i clienti devono avere una visione chiara del profilo di latenza delle zone scelte. Il posizionamento dei componenti del sistema SAP è ottimale per quanto riguarda la distanza prossimale tra l'applicazione SAP e il database.  Lo [strumento di test della latenza](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) della zona di disponibilità del dominio pubblico consente di semplificare la misurazione.  


### <a name="backup-strategy"></a>Strategia di backup
Molti clienti stanno già utilizzando soluzioni di backup di terze parti per SAP HANA su HLI.  In tal caso è necessario configurare solo una macchina virtuale protetta aggiuntiva e database HANA.  I processi di backup HLI in corso possono ora essere non pianificati se il computer viene rimosso dopo la migrazione.
Backup di Azure per SAP HANA in VM è ora disponibile in genere.  Vedere questi collegamenti per informazioni dettagliate su: Backup , Ripristina , [Gestire il](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) backup SAP HANA nelle macchine virtuali di Azure.See these links for detailed information about: [Backup](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database), [Restore](https://docs.microsoft.com/azure/backup/sap-hana-db-restore), Manage SAP HANA backup in Azure VMs.

### <a name="dr-strategy"></a>Strategia di ripristino di emergenza
Se gli obiettivi del livello di servizio soddisfano un tempo di ripristino più lungo, un semplice backup dell'archiviazione e del ripristino BLOB sul posto o il ripristino in una nuova macchina virtuale è la strategia di ripristino più semplice e meno costosa.  
Come la piattaforma di istanza di grandi dimensioni in cui HANA DR in genere viene eseguita con HSR; In Azure VM, HSR è anche la soluzione SAP HANA DR più naturale e nativa.  Indipendentemente dal fatto che la distribuzione di origine sia a istanza singola o in cluster, è necessaria una replica dell'infrastruttura di origine nell'area di ripristino di emergenza.
Questa replica di ripristino di emergenza verrà configurata al termine della migrazione dell'HLI primario alla macchina virtuale.  Il database HANA di ripristino di emergenza verrà registrato nell'istanza SAP HANA primaria nell'istanza della macchina virtuale come sito di replica secondario.  

### <a name="sap-application-server-connectivity-destination-change"></a>Modifica della destinazione della connettività del server applicazioni SAP
La migrazione HSR comporta un nuovo host HANA DB e quindi un nuovo nome host DB per il livello dell'applicazione, i profili SAP devono essere modificati per riflettere il nuovo nome host.  Se la commutazione viene eseguita tramite la risoluzione dei nomi mantenendo il nome host, non è necessaria alcuna modifica del profilo.

### <a name="operating-system"></a>Sistema operativo
Le immagini del sistema operativo per HLI e VM, nonostante siano sullo stesso livello di rilascio, SLES 12 SP4, ad esempio, non sono identiche. I clienti devono convalidare i pacchetti necessari, correzioni rapide, patch, kernel e correzioni di sicurezza sull'HLI per installare gli stessi pacchetti nella destinazione.  È supportato per usare HSR per replicare da un sistema operativo precedente in una macchina virtuale con una versione più recente del sistema operativo.  Verificare le versioni supportate specifiche esaminando la [nota SAP 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Nuova richiesta di licenza SAP
Un semplice callout per richiedere una nuova licenza SAP per il nuovo sistema HANA ora che è stata eseguita la migrazione alle macchine virtuali.

### <a name="service-level-agreement-sla-differences"></a>Differenze del contratto di servizio (SLA)
The authors like to call out the difference of availability SLA between HLI and Azure VM.  Ad esempio, le coppie HA HLIs in cluster offrono una disponibilità del 99,99%.For example, clustered HLIs HA pairs offer 99.99% availability. Per ottenere lo stesso sLA, è necessario distribuire le macchine virtuali nelle zone di disponibilità. Questo [articolo](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) descrive la disponibilità con le architetture di distribuzione associate in modo che i clienti possano pianificare di conseguenza l'infrastruttura di destinazione.


## <a name="migration-strategy"></a>Strategia di migrazione
In this document, we cover only the HANA System Replication approach for the migration from HLI to Azure VM.  Dipende dalla soluzione di archiviazione di destinazione distribuita, il processo differisce leggermente. I passaggi di alto livello sono descritti di seguito.

### <a name="vm-with-premiumultra-disks-for-data"></a>VM con dischi premium/ultra per i dati
Per le macchine virtuali distribuite con dischi Premium o ultra, la configurazione di replica del sistema SAP HANA standard è applicabile per la configurazione di HSR.  L'articolo della [Guida di SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) fornisce una panoramica dei passaggi necessari per configurare la replica del sistema, assumere il controllo di un sistema secondario, eseguire il ripristino del database primario e disabilitare la replica del sistema.  Ai fini della migrazione, sarà necessaria solo l'installazione, l'acquisizione e la disabilitazione dei passaggi di replica.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM con ANF per volumi di dati e log
A livello generale, gli snapshot di archiviazione HLI più recenti dei volumi di log e dati completi devono essere copiati in Archiviazione di Azure, dove sono accessibili e recuperabili dalla macchina virtuale HANA di destinazione.  Il processo di copia può essere fatto con qualsiasi strumenti di copia Linux nativi.  

> [!IMPORTANT]
> La copia e il trasferimento dei dati possono richiedere ore dipende dalle dimensioni del database HANA e dalla larghezza di banda della rete.  La maggior parte del processo di copia deve essere eseguita prima del tempo di inattività primario HANA DB.

### <a name="mcos-to-mdc-conversion"></a>Conversione da MCOS a MDC
Il modello di distribuzione MCOS (Multiple Components in One System) è stato utilizzato da alcuni dei nostri clienti HLI.  La motivazione consisteva nell'eludere la limitazione degli snapshot di archiviazione MDC (Multiple Databases Container) delle versioni precedenti di SAP HANA.  Nel modello MCOS, diverse istanze SAP HANA indipendenti vengono impilate in un unico pannello HLI.  L'utilizzo di HSR per la migrazione funzionerebbe correttamente, ma con conseguente più macchine virtuali HANA con un DB tenant ciascuna.  Questo stato finale rende il paesaggio più affollato di quello a cui un cliente potrebbe essere abituato.  Con la distribuzione predefinita di SAP HANA 2.0 MDC, un'alternativa praticabile consiste nell'eseguire [lo spostamento del tenant HANA](https://launchpad.support.sap.com/#/notes/2472478) dopo la migrazione HSR.  Questo processo "consolida" questi database HANA indipendenti in cotenant in un unico contenitore HANA.  

### <a name="application-layer-consideration"></a>Considerazioni a livello di applicazioneApplication layer consideration
Il server DB viene visualizzato come il centro di un sistema SAP.  Tutti i server applicazioni devono trovarsi vicino al database SAP HANA.  In alcuni casi in cui si desidera un nuovo utilizzo di PPG, potrebbe essere necessario il trasferimento dei server applicazioni esistenti nel PPG in cui è necessaria la macchina virtuale HANA.  La creazione di nuovi server applicazioni può essere considerata più semplice se si dispone già di modelli di distribuzione a portata di mano.  
Se i server applicazioni esistenti e la nuova macchina virtuale HANA si trovano in modo ottimale, non è necessario compilare nuovi server applicazioni a meno che non sia necessaria una capacità aggiuntiva.  
Se viene creata una nuova infrastruttura per migliorare la disponibilità del servizio, i server applicazioni esistenti potrebbero diventare superflui e devono essere arrestati ed eliminati.
Se il nome host della macchina virtuale di destinazione è stato modificato e diverso dal nome host HLI, i profili del server applicazioni SAP devono essere regolati in modo che puntino al nuovo host.  Se è stato modificato solo l'indirizzo IP del database HANA, è necessario un aggiornamento del record DNS per condurre le connessioni in ingresso alla nuova macchina virtuale HANA.

### <a name="acceptance-test"></a>Test di accettazione
Anche se la migrazione da HLI a VM non apporta modifiche rilevanti al contenuto del database rispetto a una migrazione eterogenea, è comunque consigliabile convalidare le funzionalità chiave e l'aspetto delle prestazioni della nuova configurazione.  

### <a name="cutover-plan"></a>Piano di cutover
Anche se questa migrazione è semplice, comporta tuttavia lo smantellamento di un db esistente.  Un'attenta pianificazione per mantenere il sistema di origine con il contenuto associato e le immagini di backup sono fondamentali nel caso in cui sia necessario il fallback.  Una buona pianificazione offre un'inversione più rapida.   


## <a name="post-migration"></a>Dopo la migrazione
Il processo di migrazione non viene eseguito fino a quando non sono stati disaccoppiati i servizi o la connettività dipendenti da HLI per garantire il ripristino dell'integrità dei dati.  Inoltre, arrestare i servizi non necessari.  Questa sezione richiama alcuni elementi top-of-mind.

### <a name="decommissioning-the-hli"></a>Smantellamento dell'HLI
Dopo aver eseguito correttamente la migrazione del database HANA alla macchina virtuale di Azure, assicurarsi che non vengano eseguite transazioni aziendali produttive nel database HLI.  Tuttavia, mantenere l'HLI in esecuzione per un periodo di tempo equivale alla sua finestra di conservazione del backup locale è una pratica sicura che garantisce un ripristino più rapido se necessario.  Solo allora la lama HLI deve essere dismessa.  I clienti devono concludere contrattualmente i loro impegni HLI con Microsoft contattando i loro rappresentanti Microsoft.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Rimuovere qualsiasi proxy (ad esempio Iptables, BIGIP) configurato per HLI 
Se un servizio proxy come IPTables viene utilizzato per instradare il traffico locale da e verso l'HLI, non è più necessario dopo la corretta migrazione alla macchina virtuale.  Tuttavia, questo servizio di connettività deve essere mantenuto per tutto il tempo in cui il pannello HLI è ancora in attesa.  Arrestare il servizio solo dopo che il pannello HLI è stato completamente disattivato.

### <a name="remove-global-reach-for-hli"></a>Rimuovere la copertura globale per HLI 
La copertura globale viene usata per connettere il gateway ExpressRoute dei clienti al gateway HLI ExpressRoute.Global Reach is used to connect customers' ExpressRoute gateway with the HLI ExpressRoute gateway.  Consente al traffico locale dei clienti di raggiungere direttamente il tenant HLI senza l'utilizzo di un servizio proxy.  Questa connessione non è più necessaria in assenza dell'unità HLI dopo la migrazione.  Come nel caso del servizio proxy IPTables, GlobalReach deve essere mantenuto fino a quando il pannello HLI non viene completamente rimosso.

### <a name="operating-system-subscription--movereuse"></a>Abbonamento al sistema operativo – spostamento/riutilizzo
Man mano che i server delle macchine virtuali sono in attivi e i pannelli HLI vengono rimossi, le sottoscrizioni del sistema operativo possono essere sostituite o riutilizzate per evitare il doppio pagamento delle licenze del sistema operativo.



## <a name="next-steps"></a>Passaggi successivi
 Vedere i seguenti articoli:
- [Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
- [Carichi di lavoro SAP in Azure: elenco](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)di controllo per la pianificazione e la distribuzione.
