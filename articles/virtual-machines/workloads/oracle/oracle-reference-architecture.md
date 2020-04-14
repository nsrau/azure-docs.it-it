---
title: Architetture di riferimento per i database Oracle in Azure Documenti Microsoft
description: Architetture di riferimento per l'esecuzione di database Oracle Enterprise Edition in macchine virtuali di Microsoft Azure.
services: virtual-machines-linux
author: mimckitt
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2019
ms.author: mimckitt
ms.custom: ''
ms.openlocfilehash: 1dc677ded1e13a64c082d49140fa0de69c0ed9d4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263268"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Architetture di riferimento per Oracle Database Enterprise Edition in AzureReference architectures for Oracle Database Enterprise Edition on Azure

Questa guida descrive in dettaglio le informazioni sulla distribuzione di un database Oracle a disponibilità elevata in Azure.This guide details information on deploying a highly available Oracle database on Azure. Inoltre, questa guida approfondisce le considerazioni sul ripristino di emergenza. Queste architetture sono state create in base alle distribuzioni dei clienti. Questa guida si applica solo a Oracle Database Enterprise Edition.

Per altre informazioni sull'ottimizzazione delle prestazioni del database Oracle, vedere [Architetto di un database Oracle](oracle-design.md).

## <a name="assumptions"></a>Presupposti

- È necessario comprendere i diversi concetti di Azure, ad esempio le zone di [disponibilitàYou](../../../availability-zones/az-overview.md) have an understanding of the different concepts of Azure such as availability zones
- Si esegue Oracle Database Enterprise Edition 12c o versione successiva
- Si è a conoscenza e si riconoscono le implicazioni di licenza quando si utilizzano le soluzioni in questo articolo

## <a name="high-availability-for-oracle-databases"></a>Disponibilità elevata per i database OracleHigh availability for Oracle databases

Il raggiungimento di un'elevata disponibilità nel cloud è una parte importante della pianificazione e della progettazione di ogni organizzazione. Microsoft Azure offre zone di [disponibilità](../../../availability-zones/az-overview.md) e set di disponibilità (da usare nelle aree in cui le zone di disponibilità non sono disponibili). Altre informazioni sulla [gestione della disponibilità delle macchine virtuali](../../../virtual-machines/linux/manage-availability.md) da progettare per il cloud.

Oltre agli strumenti e alle offerte native del cloud, Oracle fornisce soluzioni per la disponibilità elevata, ad esempio [Oracle Data Guard,](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7) [Data Guard with FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)e [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) che possono essere configurate in Azure. Questa guida illustra le architetture di riferimento per ognuna di queste soluzioni.

Infine, durante la migrazione o la creazione di applicazioni per il cloud, è importante modificare il codice dell'applicazione per aggiungere modelli nativi cloud, ad esempio il modello di [ripetizione dei tentativi](https://docs.microsoft.com/azure/architecture/patterns/retry) e il modello di [interruttore](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker). Modelli aggiuntivi definiti nella guida Modelli di [progettazione cloud](https://docs.microsoft.com/azure/architecture/patterns/) potrebbero aiutare l'applicazione a essere più resiliente.

### <a name="oracle-rac-in-the-cloud"></a>Oracle RAC nel cloud

Oracle Real Application Cluster (RAC) è una soluzione di Oracle per aiutare i clienti a ottenere velocità elevate facendo in modo che molte istanze accedano a un unico archivio di database (modello di architettura tutto condiviso). Mentre Oracle RAC può essere utilizzato anche per la disponibilità elevata in locale, Oracle RAC da solo non può essere utilizzato per la disponibilità elevata nel cloud in quanto protegge solo contro gli errori a livello di istanza e non in caso di errori a livello di rack o data center. Per questo motivo, Oracle consiglia di utilizzare Oracle Data Guard con il database (sia a istanza singola che rac) per la disponibilità elevata. I clienti in genere richiedono un sLA elevato per l'esecuzione delle loro applicazioni mission-critical. Oracle RAC non è attualmente certificato o supportato da Oracle in Azure. Tuttavia, Azure offre funzionalità come Azure offre zone di disponibilità e finestre di manutenzione pianificata per proteggere da errori a livello di istanza. Inoltre, i clienti possono utilizzare tecnologie come Oracle Data Guard, Oracle GoldenGate e Oracle Sharding per prestazioni elevate e resiliancy proteggendo i propri database da errori a livello di rack, nonché a livello di data center e geo-politici.

Quando si eseguono database Oracle in più zone di [disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview) in combinazione con Oracle Data Guard o GoldenGate, i clienti sono in grado di ottenere un sLA uptime del 99,99%. Nelle aree di Azure in cui le zone di disponibilità non sono ancora presenti, i clienti possono usare i set di [disponibilità](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) e ottenere un sLA di tempo di attività del 99,95%.

>NOTA: è possibile avere una destinazione di tempo di attività che è molto superiore al sLA uptime fornito da Microsoft.

## <a name="disaster-recovery-for-oracle-databases"></a>Ripristino di emergenza per i database OracleDisaster recovery for Oracle databases

Quando si ospitano le applicazioni mission-critical nel cloud, è importante progettare per la disponibilità elevata e il ripristino di emergenza.

Per Oracle Database Enterprise Edition, Oracle Data Guard è una funzionalità utile per il ripristino di emergenza. È possibile configurare un'istanza del database standby in [un'area di Azure abbinata](/azure/best-practices-availability-paired-regions) e configurare il failover di Data Guard per il ripristino di emergenza. Per una perdita di dati pari a zero, è consigliabile distribuire un'istanza Oracle Data Guard Far Sync oltre a Active Data Guard. 

Valutare la possibilità di configurare l'istanza di Data Guard Far Sync in una zona di disponibilità diversa rispetto al database primario Oracle se l'applicazione consente la latenza (è necessario eseguire test approfonditi). Utilizzare una modalità **di disponibilità massima** per impostare il trasporto sincrono dei file di riesecuzione nell'istanza di Sincronizzazione lontana. Questi file vengono quindi trasferiti in modo asincrono al database standby. 

Se l'applicazione non consente la perdita di prestazioni durante la configurazione dell'istanza di Far Sync in un'altra zona di disponibilità in modalità **disponibilità massima** (sincrona), è possibile configurare un'istanza di Far Sync nella stessa zona di disponibilità del database primario. Per una maggiore disponibilità, è consigliabile configurare più istanze di Far Sync vicino al database primario e almeno un'istanza vicino al database standby (se il ruolo esegue la transizione). Per saperne di più su Oracle Data Guard Far Sync, questo [white paper Oracle Active Data Guard Far Sync](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf).

Quando si utilizzano i database Oracle Standard Edition, sono disponibili soluzioni ISV come DBVisit Standby che consentono di impostare la disponibilità elevata e il ripristino di emergenza.

## <a name="reference-architectures"></a>Architetture di riferimento

### <a name="oracle-data-guard"></a>Oracle Data Guard

Oracle Data Guard garantisce disponibilità elevata, protezione dei dati e ripristino di emergenza per i dati aziendali. Data Guard gestisce i database standby come copie coerenti a livello transazionale del database primario. A seconda della distanza tra il database primario e secondario e la tolleranza dell'applicazione per la latenza, è possibile impostare la replica sincrona o asincrona. Quindi, se il database primario non è disponibile a causa di un'interruzione pianificata o non pianificata, Data Guard può passare qualsiasi database standby al ruolo primario, riducendo al minimo i tempi di inattività. 

Quando si utilizza Oracle Data Guard, è anche possibile aprire il database secondario per scopi di sola lettura. Questa configurazione è denominata Active Data Guard.This configuration is called Active Data Guard. Oracle Database 12c ha introdotto una funzionalità denominata Data Guard Far Sync Instance. Questa istanza consente di impostare una configurazione di perdita di dati zero del database Oracle senza dover compromettere le prestazioni.

> [!NOTE]
> Active Data Guard richiede licenze aggiuntive. Questa licenza è necessaria anche per utilizzare la funzione Far Sync. Contatta il tuo rappresentante Oracle per discutere le implicazioni relative alle licenze.

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle Data Guard con FSFO
Oracle Data Guard con Fast-Start Failover (FSFO) può fornire resilienza aggiuntiva impostando il broker in un computer separato. Il broker Data Guard e il database secondario eseguono l'osservatore e osservano il database primario per i tempi di inattività. Ciò consente la ridondanza anche nella configurazione dell'osservatore di Data Guard. 

Con Oracle Database versione 12.2 e successive, è anche possibile configurare più osservatori con una singola configurazione di broker Oracle Data Guard. Questa configurazione offre disponibilità aggiuntiva, nel caso in cui un osservatore e il database secondario si verifichino tempi di inattività. Data Guard Broker è leggero e può essere ospitato in una macchina virtuale relativamente piccola. Per ulteriori informazioni su Data Guard Broker e sui relativi vantaggi, consulta la [documentazione di Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) su questo argomento.

Il diagramma seguente è un'architettura consigliata per l'uso di Oracle Data Guard in Azure con zone di disponibilità. Questa architettura consente di ottenere un servizio di servizio per il tempo di attività delle macchine virtuali del 99,99%.

![Oracle Database con zone di disponibilità con Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

Nel diagramma precedente, il sistema client accede a un'applicazione personalizzata con back-end Oracle tramite il Web. Il front-end Web è configurato in un servizio di bilanciamento del carico. Il frontend Web effettua una chiamata al server applicazioni appropriato per gestire il lavoro. Il server applicazioni interroga il database Oracle primario. Il database Oracle è stato configurato utilizzando una [macchina virtuale ottimizzata](../../../virtual-machines/windows/sizes-memory.md) per la memoria hyperthreading con [vCPU core vincolate](../../../virtual-machines/windows/constrained-vcpu.md) per risparmiare sui costi di licenza e ottimizzare le prestazioni. Più dischi Premium o Ultra (dischi gestiti) vengono utilizzati per le prestazioni e la disponibilità elevata.

I database Oracle vengono inseriti in più zone di disponibilità per la disponibilità elevata. Ogni zona è costituita da uno o più data center dotati di alimentazione, raffreddamento e rete indipendenti. Per garantire la resilienza, in tutte le aree abilitate vengono configurate almeno tre zone separate. La separazione fisica delle zone di disponibilità all'interno di un'area protegge i dati dagli errori del data center. Inoltre, due osservatori FSFO sono impostati in due zone di disponibilità per avviare ed eseguire il failover del database al database secondario quando si verifica un'interruzione. 

In questo caso, è possibile impostare altri osservatori e/o database di standby in una zona di disponibilità diversa (A1, in questo caso) rispetto alla zona illustrata nell'architettura precedente. Infine, i database Oracle vengono monitorati per i tempi di attività e le prestazioni da Oracle Enterprise Manager (OEM). OEM consente inoltre di generare vari report sulle prestazioni e sull'utilizzo.

Nelle aree in cui le zone di disponibilità non sono supportate, è possibile usare i set di disponibilità per distribuire il database Oracle in modo a disponibilità elevata. I set di disponibilità consentono di ottenere un tempo di attività della macchina virtuale pari al 99,95%. Il diagramma seguente è un'architettura di riferimento di questo utilizzo:The following diagram is a reference architecture of this use:

![Oracle Database che utilizza i set di disponibilità con Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * La macchina virtuale di Oracle Enterprise Manager non deve essere inserita in un set di disponibilità poiché è presente una sola istanza di OEM distribuita.
> * I dischi ultra non sono attualmente supportati in una configurazione del set di disponibilità.

#### <a name="oracle-data-guard-far-sync"></a>Oracle Data Guard Far Sync

Oracle Data Guard Far Sync non offre alcuna funzionalità di protezione dalla perdita di dati per i database Oracle. Questa funzionalità consente di proteggersi dalla perdita di dati in caso di errore del computer di database. Oracle Data Guard Far Sync deve essere installato in una macchina virtuale separata. Far Sync è un'istanza Oracle leggera che dispone solo di un file di controllo, file di password, spfile e registri di standby. Non ci sono file di dati o rego file di registro. 

Per una protezione dalla perdita di dati zero, deve essere presente una comunicazione sincrona tra il database primario e l'istanza di Far Sync. L'istanza Far Sync riceve il ripristino dal database primario in modo sincrono e lo inoltra immediatamente a tutti i database standby in modo asincrono. Questa configurazione riduce anche l'overhead nel database primario, perché deve solo inviare il ripristino all'istanza di Far Sync anziché a tutti i database standby. Se un'istanza di Sincronizzazione lontana non riesce, Protezione dati utilizza automaticamente il trasporto asincrono al database secondario dal database primario per mantenere la protezione dalla perdita di dati prossima allo zero. Per una maggiore resilienza, i clienti possono distribuire più istanze di Far Sync per ogni istanza del database (primaria e secondaria).

Il diagramma seguente è un'architettura a disponibilità elevata che usa Oracle Data Guard Far Sync:The following diagram is a high availability architecture using Oracle Data Guard Far Sync:

![Database Oracle che utilizza le zone di disponibilità con Data Guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

Nell'architettura precedente è presente un'istanza di Far Sync distribuita nella stessa zona di disponibilità dell'istanza del database per ridurre la latenza tra i due. Nei casi in cui l'applicazione è sensibile alla latenza, è consigliabile distribuire il database e l'istanza o le istanze di Far Sync in un gruppo di posizionamento di [prossimità.](../../../virtual-machines/linux/proximity-placement-groups.md)

Il diagramma seguente è un'architettura che utilizza Oracle Data Guard FSFO e Far Sync per ottenere disponibilità elevata e ripristino di emergenza:The following diagram is an architecture using Oracle Data Guard FSFO and Far Sync to achieve high availability and disaster recovery:

![Database Oracle che utilizza le zone di disponibilità per il ripristino di emergenza con Data Guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate consente lo scambio e la manipolazione dei dati a livello di transazione tra più piattaforme eterogenee in tutta l'azienda. Sposta le transazioni di cui è stato eseguito il commit con integrità delle transazioni e un overhead minimo sull'infrastruttura esistente. La sua architettura modulare offre la flessibilità di estrarre e replicare record di dati selezionati, modifiche transazionali e modifiche al DDL (data definition language) in un'ampia gamma di topologie.

Oracle GoldenGate consente di configurare il database per la disponibilità elevata fornendo la replica bidirezionale. In questo modo è possibile impostare una configurazione **multimaster** o **attiva-attiva.** Il diagramma seguente è un'architettura consigliata per l'installazione attiva-attiva di Oracle GoldenGate in Azure.The following diagram is a recommended architecture for Oracle GoldenGate active-active setup on Azure. Nell'architettura seguente, il database Oracle è stato configurato utilizzando una [macchina virtuale ottimizzata](../../../virtual-machines/windows/sizes-memory.md) per la memoria hyperthreading con [vCPU core vincolate](../../../virtual-machines/windows/constrained-vcpu.md) per ridurre i costi di licenza e ottimizzare le prestazioni. Per le prestazioni e la disponibilità vengono utilizzati più dischi Premium o Ultra (dischi gestiti).

![Oracle Database con zone di disponibilità con Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Un'architettura simile può essere configurata usando i set di disponibilità nelle aree in cui le zone di disponibilità non sono attualmente disponibili.

Oracle GoldenGate dispone di processi quali Extract, Pump e Replicat che consentono di replicare in modo asincrono i dati da un server di database Oracle a un altro. Questi processi consentono di impostare una replica bidirezionale per garantire la disponibilità elevata del database in caso di tempo di inattività a livello di zona di disponibilità. Nel diagramma precedente il processo di estrazione viene eseguito sullo stesso server del database Oracle, mentre i processi Data Pump e Replicat vengono eseguiti in un server separato nella stessa zona di disponibilità. Il processo Replicat viene utilizzato per ricevere dati dal database nell'altra zona di disponibilità ed eseguire il commit dei dati nel database Oracle nella relativa zona di disponibilità. Analogamente, il processo Data Pump invia i dati estratti dal processo di estrazione al processo di replica nell'altra zona di disponibilità. 

Mentre il diagramma dell'architettura precedente mostra il processo Data Pump e Replicat configurato su un server separato, è possibile impostare tutti i processi Oracle GoldenGate sullo stesso server, in base alla capacità e all'utilizzo del server. Consultare sempre il report AWR e le metriche in Azure per comprendere il modello di utilizzo del server.

Quando si configura la replica bidirezionale Oracle GoldenGate in zone di disponibilità o aree diverse, è importante garantire che la latenza tra i diversi componenti sia accettabile per l'applicazione. La latenza tra le zone di disponibilità e le aree può variare e dipende da più fattori. È consigliabile impostare i test delle prestazioni tra il livello applicazione e il livello di database in aree e aree di disponibilità diverse per verificare che soddisfi i requisiti di prestazioni dell'applicazione.

Il livello applicazione può essere configurato nella propria subnet e il livello database può essere separato nella propria subnet. Quando possibile, è consigliabile usare il [gateway applicazione](../../../application-gateway/overview.md) di Azure per bilanciare il carico del traffico tra i server applicazioni. Il gateway applicazione di Azure è un solido servizio di bilanciamento del carico del traffico Web.Azure Application Gateway is a robust web traffic balancer. Fornisce l'affinità di sessione basata su cookie che mantiene una sessione utente sullo stesso server, riducendo così al minimo i conflitti nel database. Le alternative al gateway applicazione sono [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) e Azure Traffic [Manager.](../../../traffic-manager/traffic-manager-overview.md)

### <a name="oracle-sharding"></a>Frammento Oracle

Il partizionamento frammento è un modello di livello dati introdotto in Oracle 12.2.Sharding is a data tier pattern that was introduced in Oracle 12.2. Consente di partizionare e ridimensionare orizzontalmente i dati in database indipendenti. Si tratta di un'architettura di condivisione non in cui ogni database è ospitato in una macchina virtuale dedicata, che consente un'elevata velocità effettiva di lettura e scrittura oltre alla resilienza e a una maggiore disponibilità. Questo modello elimina singoli punti di errore, fornisce l'isolamento degli errori e consente gli aggiornamenti in sequenza senza tempi di inattività. Il tempo di inattività di una partizione o di un errore a livello di data center non influisce sulle prestazioni o sulla disponibilità delle altre partizioni in altri data center. 

Il partizionamento su più tempo è adatto per applicazioni OLTP ad alta velocità effettiva che non possono permettersi tempi di inattività. Tutte le righe con la stessa chiave di partizionamento orizzontale sono sempre garantite per essere sulla stessa partizione, aumentando così le prestazioni fornendo la coerenza elevata. Le applicazioni che utilizzano il partizionamento delle partizioni devono disporre di un modello di dati ben definito e di una strategia di distribuzione dei dati (hash coerente, intervallo, elenco o composito) che acceda principalmente ai dati utilizzando una chiave di partizionamento di partizionamento (ad esempio *customerId* o *accountNum*). Il partizionamento su base consente inoltre di archiviare particolari set di dati più vicini ai clienti finali, consentendo così di soddisfare i requisiti di prestazioni e conformità.

È consigliabile replicare le partizioni per la disponibilità elevata e il ripristino di emergenza. Questa configurazione può essere eseguita utilizzando tecnologie Oracle come Oracle Data Guard o Oracle GoldenGate. Un'unità di replica può essere una partizione, una parte di una partizione o un gruppo di partizioni. La disponibilità di un database partizionato non è influenzata da un'interruzione o da un rallentamento di una o più partizioni. Per la disponibilità elevata, le partizioni di standby possono essere posizionate nella stessa zona di disponibilità in cui sono posizionate le partizioni primarie. Per il ripristino di emergenza, le partizioni di standby possono trovarsi in un'altra area. È inoltre possibile distribuire partizioni in più aree per gestire il traffico in tali aree. Per ulteriori informazioni sulla configurazione della disponibilità elevata e della replica del database partizionato, vedere la documentazione relativa al [partizionamento orizzontale Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html).

Oracle Sharding è costituito principalmente dai componenti seguenti. Ulteriori informazioni su questi componenti sono disponibili nella documentazione di [Oracle Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html):

- **Catalogo partizioni:** database Oracle speciale che è un archivio permanente per tutti i dati di configurazione del database Shard. Tutte le modifiche alla configurazione, ad esempio l'aggiunta o la rimozione di partizioni, il mapping dei dati e DDL in un database partizione, vengono avviate nel catalogo partizioni. Il catalogo partizioni contiene anche la copia master di tutte le tabelle duplicate in un sDB. 

  Il catalogo delle partizioni utilizza viste materializzate per replicare automaticamente le modifiche alle tabelle duplicate in tutte le partizioni. Il database del catalogo partizioni funge anche da coordinatore di query utilizzato per elaborare query a più partizioni e query che non specificano una chiave di partizionamento orizzontale. 
  
  L'utilizzo di Oracle Data Guard insieme alle zone di disponibilità o ai set di disponibilità per la disponibilità elevata del catalogo di partizioni è una procedura consigliata. La disponibilità del catalogo partizioni non ha alcun impatto sulla disponibilità del database partizionato. Un tempo di inattività nel catalogo partizioni influisce solo sulle operazioni di manutenzione e sulle query multishard durante il breve periodo di completamento del failover di Protezione dati. Le transazioni online continuano a essere instradate ed eseguite dal database SDB e non sono interessate da un'interruzione del catalogo.

- **Server Director:** servizi leggeri che devono essere distribuiti in ogni area/zona di disponibilità in cui risiedono le partizioni. I Server Director sono Global Service Manager distribuiti nel contesto di Oracle Sharding. Per la disponibilità elevata, è consigliabile distribuire almeno un server di partizioni in ogni zona di disponibilità in cui sono presenti le partizioni. 

  Quando ci si connette al database inizialmente, le informazioni di routing vengono impostate da un direttore partizione e vengono memorizzate nella cache per le richieste successive, ignorando il server Director della partizione. Una volta stabilita la sessione con una partizione, tutte le query e le DLL SQL vengono supportate ed eseguite nell'ambito della partizione specificata. Questo routing è veloce e viene utilizzato per tutti i carichi di lavoro OLTP che eseguono transazioni intra-shard. È consigliabile usare il routing diretto per tutti i carichi di lavoro OLTP che richiedono prestazioni e disponibilità più elevate. La cache di routing viene aggiornata automaticamente quando una partizione non è più disponibile o si verificano modifiche alla topologia di partizionamento di partizione. 
  
  Per il routing dipendente dai dati ad alte prestazioni, Oracle consiglia di utilizzare un pool di connessioni quando si accede ai dati nel database partizionato. I pool di connessioni Oracle, le librerie specifiche del linguaggio e i driver supportano Oracle Sharding. Per ulteriori dettagli, fare riferimento alla documentazione di [Oracle Sharding.](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9)

- **Servizio globale:** il servizio globale è simile al normale servizio di database. Oltre a tutte le proprietà di un servizio di database, un servizio globale dispone di proprietà per i database partizionati, ad esempio l'affinità di area tra i client e la tolleranza di ritardo della partizione e della replica. È necessario creare un solo servizio globale per leggere/scrivere dati in/da un database partizionato. Quando si usa Active Data Guard e si configurano repliche di sola lettura delle partizioni, è possibile creare un altro servizio gGobal per i carichi di lavoro di sola lettura. Il client può utilizzare questi servizi globali per connettersi al database.

- **Database partizioni:** i database partizioni sono i database Oracle. Ogni database viene replicato utilizzando Oracle Data Guard in una configurazione broker con Fast-Start Failover (FSFO) abilitato. Non è necessario configurare il failover e la replica di Data Guard in ogni partizione. Questo viene configurato e distribuito automaticamente quando viene creato il database condiviso. Se si verifica un errore in una determinata partizione, la condivisione Oracle esegue automaticamente il failover delle connessioni al database dal database primario allo standby.

È possibile distribuire e gestire database partizionati Oracle con due interfacce: `GDSCTL` Oracle Enterprise Manager Cloud Control GUI e/o l'utilità della riga di comando. È anche possibile monitorare le diverse partizioni per la disponibilità e le prestazioni utilizzando il controllo cloud. Il `GDSCTL DEPLOY` comando crea automaticamente le partizioni e i rispettivi listener. Inoltre, questo comando distribuisce automaticamente la configurazione di replica utilizzata per la disponibilità elevata a livello di partizione specificata dall'amministratore.

Esistono diversi modi per partizionare un database:There are different ways to shard a database:

* Partizionamento gestito dal sistema: viene distribuito automaticamente tra partizioni tramite il partizionamento
* Partizionamento definito dall'utente: consente di specificare il mapping dei dati alle partizioni, che funziona bene in caso di requisiti normativi o di localizzazione dei dati.User-defined sharding - Allows you to specify the mapping of the data to the shards, which works well when there are regulatory or data-localization requirements)
* Partizionamento composito - Combinazione di partizioni gestite dal sistema e definite dall'utente per _spazi di partizioni_ diversi
* Sottodivisioni di tabella : simile a una normale tabella partizionata.

Ulteriori informazioni sui diversi metodi di [partizionamento delle partizioni](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) sono disponibili nella documentazione di Oracle.Read more about the different sharding methods in Oracle's documentation.

Mentre un database partizionato può apparire come un singolo database per applicazioni e sviluppatori, quando si esegue la migrazione da un database senza partizioni in un database partizionato, è necessaria un'attenta pianificazione per determinare quali tabelle verranno duplicate rispetto al partizioni. 

Le tabelle duplicate vengono archiviate in tutte le partizioni, mentre le tabelle partizioni vengono distribuite tra partizioni diverse. Si consiglia di duplicare tabelle piccole e dimensionali e distribuire/partizionare le tabelle dei fatti. I dati possono essere caricati nel database partizionato utilizzando il catalogo partizioni come coordinatore centrale o eseguendo Data Pump su ogni partizione. Per altre informazioni sulla [migrazione dei dati a un database partizionato,](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) vedere la documentazione di Oracle.Read more about migrating data to a sharded database in Oracle documentation.

#### <a name="oracle-sharding-with-data-guard"></a>Sharding Oracle con Protezione dati

Oracle Data Guard può essere utilizzato per il partizionamento delle partizioni con metodi di partizionamento di partizionamento di sistema, definiti dall'utente e compositi.

Il diagramma seguente è un'architettura di riferimento per Oracle Sharding con Oracle Data Guard utilizzato per la disponibilità elevata di ogni partizione. Il diagramma dell'architettura mostra un _metodo di partizionamento orizzontale composito._ Il diagramma dell'architettura sarà probabilmente diverso per le applicazioni con requisiti diversi per la localizzazione dei dati, il bilanciamento del carico, la disponibilità elevata, il ripristino di emergenza e così via e può utilizzare un metodo diverso per il partizionamento orizzontale. Oracle Sharding consente di soddisfare questi requisiti e scalare orizzontalmente ed efficacemente fornendo queste opzioni. Un'architettura simile può anche essere distribuita utilizzando Oracle GoldenGate.

![Oracle Database Sharding con zone di disponibilità con Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Mentre il partizionamento di partizionamento gestito dal sistema è il più semplice da configurare e gestire, il partizionamento di partizione definito dall'utente o il partizionamento generale composito è adatto per scenari in cui i dati e l'applicazione sono distribuiti geograficamente o in scenari in cui è necessario avere il controllo sulla replica di ogni partizione. 

Nell'architettura precedente, il partizionamento orizzontale composito viene usato per distribuire geograficamente i dati e scalare orizzontalmente i livelli applicazione. Il partizionamento su base composito è una combinazione di partizionamento di partizionamento e partizionamento di sistema gestite dall'utente e offre pertanto il vantaggio di entrambi i metodi. Nello scenario precedente, i dati vengono prima partizionati su più spazi di partizioni separati per area. Quindi, i dati vengono ulteriormente partizionati dall'hash coerente tra più partizioni nello spazio partizione. Ogni shardspace contiene più gruppi di partizioni. Ogni gruppo di partizioni ha più partizioni ed è una "unità" di replica, in questo caso. Ogni gruppo shardgroup contiene tutti i dati nello spazio partizioni. I frammenti A1 e B1 sono gruppi di partizioni primari, mentre i gruppi di partizioni A2 e B2 sono in standby. È possibile scegliere di fare in modo che le singole partizioni siano l'unità di replica anziché un gruppo di partizioni.

Nell'architettura precedente, un Director GSM/shard viene distribuito in ogni zona di disponibilità per la disponibilità elevata. Si consiglia di distribuire almeno un director GSM/shard per data center/area geografica. Inoltre, un'istanza del server applicazioni viene distribuita in ogni zona di disponibilità che contiene un gruppo di partizioni. Questa configurazione consente all'applicazione di mantenere bassa la latenza tra il server applicazioni e il database o il gruppo di partizioni. Se si verifica un errore in un database, il server applicazioni nella stessa area del database standby può gestire le richieste una volta eseguita la transizione del ruolo del database. Il gateway applicazione di Azure e il server Director partizione tengono traccia della latenza di richiesta e risposta e indirizzano le richieste di conseguenza.

Dal punto di vista dell'applicazione, il sistema client effettua una richiesta al gateway applicazione di Azure (o ad altre tecnologie di bilanciamento del carico in Azure) che reindirizza la richiesta all'area più vicina al client. Il gateway applicazione di Azure supporta anche le sessioni permanenti, pertanto tutte le richieste provenienti dallo stesso client vengono instradate allo stesso server applicazioni. Il server applicazioni utilizza il pool di connessioni nei driver di accesso ai dati. Questa funzione è disponibile in driver come JDBC, ODP.NET, OCI e così via. I driver possono riconoscere le chiavi di partizionamento delle partizioni specificate come parte della richiesta. [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) per i client JDBC può consentire ai client di applicazioni non Oracle, ad esempio Apache Tomcat e IIS, di utilizzare Oracle Sharding. 

Durante la richiesta iniziale, il server applicazioni si connette al server di partizione nell'area per ottenere informazioni di routing per la partizione a cui la richiesta deve essere instradata. In base alla chiave di partizionamento di livello passata, il director instrada il server applicazioni alla partizione relativa. Il server applicazioni memorizza nella cache queste informazioni creando una mappa e, per le richieste successive, ignora il server di partizione e indirizza le richieste direttamente alla partizione.

#### <a name="oracle-sharding-with-goldengate"></a>Oracle Sharding con GoldenGate

Il diagramma seguente è un'architettura di riferimento per Oracle Sharding con Oracle GoldenGate per la disponibilità elevata nell'area di ogni partizione. A differenza dell'architettura precedente, questa architettura ritrae solo la disponibilità elevata all'interno di una singola area di Azure (più zone di disponibilità). Si potrebbe distribuire un database partizionato a disponibilità elevata in più aree (simile all'esempio precedente) usando Oracle GoldenGate.One could deploy a multi-region high availability sharded database (similar to the preceding example) using Oracle GoldenGate.

![Oracle Database Sharding con le zone di disponibilità con GoldenGate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

L'architettura di riferimento precedente utilizza il metodo di partizionamento orizzontale _gestito dal sistema_ per partizionare i dati. Poiché la replica Oracle GoldenGate viene eseguita a livello di blocco, metà dei dati replicati in una partizione può essere replicata in un'altra partizione. L'altra metà può essere replicata in una partizione diversa. 

Il modo in cui i dati vengono replicati dipende dal fattore di replica. Con un fattore di replica pari a 2, si disporrà di due copie di ogni blocco di dati nelle tre partizioni nel gruppo partizioni. Analogamente, con un fattore di replica pari a 3 e tre partizioni nel gruppo partizioni, tutti i dati in ogni partizione verranno replicati in ogni altra partizione nel gruppo partizioni. Ogni partizione nel gruppo di partizioni può avere un fattore di replica diverso. Questa configurazione consente di definire la progettazione della disponibilità elevata e del ripristino di emergenza in modo efficiente all'interno di un gruppo di partizioni e tra più gruppi di partizioni.

Nell'architettura precedente, il partizionamento del gruppo A e del gruppo shard B contengono entrambi gli stessi dati ma risiedono in zone di disponibilità diverse. Se sia il gruppo partizioni A che il gruppo di partizioni B hanno lo stesso fattore di replica 3, ogni riga/pezzo della tabella partizionata verrà replicato 6 volte tra i due gruppi partizioni. Se il gruppo di partizioni A ha un fattore di replica pari a 3 e il gruppo shard B ha un fattore di replica pari a 2, ogni riga/blocco verrà replicato 5 volte tra i due gruppi di partizioni.

Questa impostazione impedisce la perdita di dati se si verifica un errore a livello di istanza o di zona di disponibilità. Il livello dell'applicazione è in grado di leggere e scrivere in ogni partizione. Per ridurre al minimo i conflitti, Oracle Sharding designa un "pezzo principale" per ogni intervallo di valori hash. Questa funzionalità garantisce che le richieste di scrittura per un determinato blocco vengano indirizzate al blocco corrispondente. Inoltre, Oracle GoldenGate fornisce il rilevamento e la risoluzione automatica dei conflitti per gestire eventuali conflitti. Per ulteriori informazioni e limitazioni sull'implementazione di GoldenGate con Oracle Sharding, vedere la documentazione di Oracle sull'utilizzo di [Oracle GoldenGate con un database partizionato](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72).

Nell'architettura precedente, un Director GSM/shard viene distribuito in ogni zona di disponibilità per la disponibilità elevata. Si consiglia di distribuire almeno un director GSM/shard per ogni data center o area geografica. Inoltre, un'istanza del server applicazioni viene distribuita in ogni zona di disponibilità che contiene un gruppo di partizioni. Questa configurazione consente all'applicazione di mantenere bassa la latenza tra il server applicazioni e il database o il gruppo di partizioni. Se si verifica un errore in un database, il server applicazioni nella stessa area del database standby può gestire le richieste dopo la transizione del ruolo del database. Il gateway applicazione di Azure e il server Director partizione tengono traccia della latenza di richiesta e risposta e indirizzano le richieste di conseguenza.

Dal punto di vista dell'applicazione, il sistema client effettua una richiesta al gateway applicazione di Azure (o ad altre tecnologie di bilanciamento del carico in Azure) che reindirizza la richiesta all'area più vicina al client. Il gateway applicazione di Azure supporta anche le sessioni permanenti, pertanto tutte le richieste provenienti dallo stesso client vengono instradate allo stesso server applicazioni. Il server applicazioni utilizza il pool di connessioni nei driver di accesso ai dati. Questa funzione è disponibile in driver come JDBC, ODP.NET, OCI e così via. I driver possono riconoscere le chiavi di partizionamento delle partizioni specificate come parte della richiesta. [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) per i client JDBC può consentire ai client di applicazioni non Oracle, ad esempio Apache Tomcat e IIS, di utilizzare Oracle Sharding. 

Durante la richiesta iniziale, il server applicazioni si connette al server di partizione nell'area per ottenere informazioni di routing per la partizione a cui la richiesta deve essere instradata. In base alla chiave di partizionamento di livello passata, il director instrada il server applicazioni alla partizione relativa. Il server applicazioni memorizza nella cache queste informazioni creando una mappa e, per le richieste successive, ignora il server di partizione e indirizza le richieste direttamente alla partizione.

## <a name="patching-and-maintenance"></a>Applicazione di patch e manutenzione

Quando si distribuiscono i carichi di lavoro Oracle in Azure, Microsoft si occupa di tutte le patch a livello di sistema operativo host. Qualsiasi manutenzione pianificata a livello di sistema operativo viene comunicata ai clienti in anticipo per consentire al cliente di eseguire questa manutenzione pianificata. Due server di due diverse zone di disponibilità non vengono mai patch a crete contemporaneamente. Per altri dettagli sulla manutenzione e l'applicazione di patch alle macchine virtuali, vedere [Gestire la disponibilità delle macchine virtuali.](../../../virtual-machines/linux/manage-availability.md) 

L'applicazione di patch al sistema operativo della macchina virtuale può essere automatizzata usando [Automazione di](../../../automation/automation-tutorial-update-management.md)Azure.Patching your virtual machine operating system can be automated using Azure Automation . L'applicazione di patch e la manutenzione del database Oracle possono essere automatizzate e pianificate usando [le pipeline](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops) di Azure o [Automazione di Azure](../../../automation/automation-tutorial-update-management.md) per ridurre al minimo i tempi di inattività. Vedere [Distribuzione continua e Distribuzioni blu/verde](/azure/devops/learn/what-is-continuous-delivery) per informazioni su come può essere utilizzato nel contesto dei database Oracle.

## <a name="architecture-and-design-considerations"></a>Considerazioni sull'architettura e sulla progettazione

- Prendi in considerazione l'uso di [una macchina virtuale ottimizzata per](../../../virtual-machines/windows/sizes-memory.md) la memoria hyperthreading con [vCPU core vincolate](../../../virtual-machines/windows/constrained-vcpu.md) per la macchina virtuale del database Oracle per risparmiare sui costi di licenza e ottimizzare le prestazioni. Utilizzare più dischi Premium o Ultra (dischi gestiti) per prestazioni e disponibilità.
- Quando si utilizzano dischi gestiti, il nome del disco/dispositivo può cambiare al riavvio. Si consiglia di utilizzare il dispositivo UUID anziché il nome per garantire che i supporti persistono tra i riavvii. Altre informazioni sono disponibili [qui](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab).
- Usare le zone di disponibilità per ottenere la disponibilità elevata nell'area.
- Prendi in considerazione l'uso di dischi ultra (se disponibili) o dischi premium per il tuo database Oracle.
- Valutare la possibilità di configurare un database Oracle standby in un'altra area di Azure usando Oracle Data Guard.Consider setting up a standby Oracle database in another Azure region using Oracle Data Guard.
- Valutare la possibilità di usare gruppi di posizionamento di [prossimità](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) per ridurre la latenza tra l'applicazione e il livello di database.
- Configurare [Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) per la gestione, il monitoraggio e la registrazione.
- Prendi in considerazione l'uso di Oracle Automatic Storage Management (ASM) per semplificare la gestione dello storage per il tuo database.
- Usare [le pipeline](/azure/devops/pipelines/get-started/what-is-azure-pipelines) di Azure per gestire l'applicazione di patch e gli aggiornamenti al database senza tempi di inattività.
- Modifica il codice dell'applicazione per aggiungere modelli nativi al cloud, ad esempio [ripetizione dei](/azure/architecture/patterns/retry)tentativi, [pattern di interruttori](/azure/architecture/patterns/circuit-breaker)di circuito e altri modelli definiti nella guida Modelli di [progettazione cloud](/azure/architecture/patterns/) che possono aiutare l'applicazione a essere più resiliente.

## <a name="next-steps"></a>Passaggi successivi

Esaminare gli articoli di riferimento Oracle seguenti che si applicano allo scenario.

- [Introduzione a Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Concetti di Oracle Data Guard Broker](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Configurazione di Oracle GoldenGate per la disponibilità elevata Active-Active](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Panoramica del partizionamento generale Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Active Data Guard Far Sync zero perdita di dati a qualsiasi distanza](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
