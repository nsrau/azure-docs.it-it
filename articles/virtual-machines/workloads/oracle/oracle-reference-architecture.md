---
title: Architetture di riferimento per database Oracle in Azure | Microsoft Docs
description: Fa riferimento a architetture per l'esecuzione di database di Oracle Database Enterprise Edition in Macchine virtuali di Microsoft Azure.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 12/13/2019
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: 86f3ef8ccac83cdc939cff5572dd81e78137d396
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968724"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Architetture di riferimento per Oracle Database Enterprise Edition in Azure

Questa guida fornisce informazioni dettagliate sulla distribuzione di un database Oracle a disponibilità elevata in Azure. In questa guida vengono inoltre illustrate le considerazioni sul ripristino di emergenza. Queste architetture sono state create in base alle distribuzioni dei clienti. Questa guida si applica solo a Oracle Database Enterprise Edition.

Per ulteriori informazioni sull'ottimizzazione delle prestazioni del database Oracle, vedere la pagina relativa all'architettura di [un Oracle DB](oracle-design.md).

## <a name="assumptions"></a>Presupposti

- Per conoscere i diversi concetti di Azure, ad esempio le [zone di disponibilità](../../../availability-zones/az-overview.md)
- Si sta eseguendo Oracle Database Enterprise Edition 12C o versione successiva
- Quando si usano le soluzioni in questo articolo, si è a conoscenza di e di riconoscere le implicazioni relative alle licenze

## <a name="high-availability-for-oracle-databases"></a>Disponibilità elevata per database Oracle

Il raggiungimento della disponibilità elevata nel cloud è una parte importante della pianificazione e della progettazione di ogni organizzazione. Microsoft Azure offre le [zone](../../../availability-zones/az-overview.md) di disponibilità e i set di disponibilità (da usare nelle aree in cui le zone di disponibilità non sono disponibili). Scopri di più sulla [gestione della disponibilità delle tue macchine virtuali](../../manage-availability.md) per la progettazione per il cloud.

Oltre agli strumenti e alle offerte native del cloud, Oracle offre soluzioni per la disponibilità elevata, ad esempio [Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7), [Data Guard con FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [partizionamento orizzontale](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)e [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) , che possono essere configurati in Azure. Questa guida illustra le architetture di riferimento per ognuna di queste soluzioni.

Infine, quando si esegue la migrazione o la creazione di applicazioni per il cloud, è importante modificare il codice dell'applicazione per aggiungere modelli nativi del cloud, come il modello di ripetizione dei [tentativi](/azure/architecture/patterns/retry) e il [modello di interruttore](/azure/architecture/patterns/circuit-breaker). I modelli aggiuntivi definiti nella [Guida ai modelli di progettazione cloud](/azure/architecture/patterns/) possono aiutare le applicazioni a essere più resilienti.

### <a name="oracle-rac-in-the-cloud"></a>Oracle RAC nel cloud

Oracle Real Application cluster (RAC) è una soluzione di Oracle che consente ai clienti di ottenere velocità effettiva elevate, con molte istanze che accedono a una risorsa di archiviazione del database (modello di architettura condivisa-tutti). Sebbene Oracle RAC possa essere usato anche per la disponibilità elevata in locale, non è possibile usare solo Oracle RAC per la disponibilità elevata nel cloud, perché protegge solo da errori a livello di istanza e non da errori a livello di rack o di Data Center. Per questo motivo, Oracle consiglia di usare Oracle Data Guard con il database (a seconda dell'istanza o del RAC) per la disponibilità elevata. Per l'esecuzione delle applicazioni mission-critical, i clienti richiedono in genere un contratto di contratto elevato. Oracle RAC non è attualmente certificato né supportato da Oracle in Azure. Tuttavia, Azure offre funzionalità come Azure offre zone di disponibilità e le finestre di manutenzione pianificate per la protezione da errori a livello di istanza. Oltre a questo, i clienti possono usare tecnologie quali Oracle Data Guard, Oracle GoldenGate e il partizionamento orizzontale Oracle per garantire prestazioni e resilienza elevate proteggendo i database da errori a livello di rack, oltre che a livello di data center e geo-politico.

Quando si eseguono database Oracle in più [zone di disponibilità](../../../availability-zones/az-overview.md) insieme a Oracle Data Guard o GoldenGate, i clienti sono in grado di ottenere un contratto di tempo di uptime del 99,99%. Nelle aree di Azure in cui le zone di disponibilità non sono ancora presenti, i clienti possono usare i [set di disponibilità](../../manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) e ottenere un contratto di tempo di uptime del 99,95%.

>Nota: è possibile avere un valore di destinazione di tempo molto superiore rispetto al contratto di esecuzione di tempo previsto da Microsoft.

## <a name="disaster-recovery-for-oracle-databases"></a>Ripristino di emergenza per database Oracle

Quando si ospitano le applicazioni cruciali nel cloud, è importante progettare per la disponibilità elevata e il ripristino di emergenza.

Per Oracle Database Enterprise Edition, Oracle Data Guard è una funzionalità utile per il ripristino di emergenza. È possibile configurare un'istanza del database di standby in un' [area di Azure abbinata](../../../best-practices-availability-paired-regions.md) e configurare il failover di Data Guard per il ripristino di emergenza. Per una perdita di dati pari a zero, è consigliabile distribuire un'istanza di Oracle Data Guard per la sincronizzazione oltre ad Active Data Guard. 

Valutare la possibilità di configurare l'istanza di Data Guard per la sincronizzazione in un'area di disponibilità diversa rispetto al database primario Oracle se l'applicazione consente la latenza (sono necessari test completi). Utilizzare una modalità di **disponibilità massima** per configurare il trasporto sincrono dei file di rollforward nell'istanza di sincronizzazione di tipo "lontano". Questi file vengono quindi trasferiti in modo asincrono al database di standby. 

Se l'applicazione non consente la perdita di prestazioni quando si configura un'istanza di sincronizzazione di gran lunga in un'altra zona di disponibilità in modalità di **disponibilità massima** (sincrona), è possibile configurare un'istanza di sincronizzazione più lunga nella stessa zona di disponibilità del database primario. Per una maggiore disponibilità, si consiglia di configurare più istanze di sincronizzazione lontano vicino al database primario e almeno un'istanza vicino al database di standby (se il ruolo viene sottoposto a transizione). Scopri di più su Oracle Data Guard per la sincronizzazione in questo [white paper su Oracle Active Data Guard per la sincronizzazione](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf).

Quando si usano i database Oracle Standard Edition, sono disponibili soluzioni ISV come DBVisit standby che consentono di configurare la disponibilità elevata e il ripristino di emergenza.

## <a name="reference-architectures"></a>Architetture di riferimento

### <a name="oracle-data-guard"></a>Oracle Data Guard

Oracle Data Guard garantisce disponibilità elevata, protezione dei dati e ripristino di emergenza per i dati aziendali. Data Guard gestisce i database di standby come copie coerenti a livello di transazione del database primario. A seconda della distanza tra i database primari e secondari e la tolleranza dell'applicazione per la latenza, è possibile impostare la replica sincrona o asincrona. Quindi, se il database primario non è disponibile a causa di un'interruzione pianificata o non pianificata, Data Guard può passare qualsiasi database standby al ruolo primario, riducendo al minimo i tempi di inattività. 

Quando si usa Oracle Data Guard, è anche possibile aprire il database secondario per finalità di sola lettura. Questa configurazione è denominata Active Data Guard. In Oracle Database 12c è stata introdotta una funzionalità denominata Data Guard per l'istanza di sincronizzazione. Questa istanza consente di configurare una configurazione senza perdita di dati del database Oracle senza compromettere le prestazioni.

> [!NOTE]
> Active Data Guard richiede licenze aggiuntive. Questa licenza è necessaria anche per usare la funzionalità di sincronizzazione estrema. Rivolgersi al rappresentante Oracle per discutere le implicazioni relative alle licenze.

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle Data Guard con FSFO
Oracle Data Guard con failover di Fast-Start (FSFO) può fornire resilienza aggiuntiva impostando il broker in un computer separato. Il broker di Data Guard e il database secondario eseguono entrambi l'Observer e osservano il database primario per i tempi di inattività. Ciò consente anche la ridondanza nell'installazione dell'Observer di Data Guard. 

Con Oracle Database versione 12,2 e successive, è anche possibile configurare più Observer con una singola configurazione di Service Broker Oracle Data Guard. Questa installazione fornisce disponibilità aggiuntiva, nel caso in cui un osservatore e il database secondario riscontrino tempi di inattività. Data Guard Broker è leggero e può essere ospitato in una macchina virtuale relativamente piccola. Per altre informazioni su Data Guard broker e sui relativi vantaggi, vedere la [documentazione di Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) in questo argomento.

Il diagramma seguente è un'architettura consigliata per l'uso di Oracle Data Guard in Azure con le zone di disponibilità. Questa architettura consente di ottenere un contratto di tempo di esecuzione della macchina virtuale del 99,99%.

![Diagramma che mostra un'architettura consigliata per l'uso di Oracle Data Guard in Azure con le zone di disponibilità.](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

Nel diagramma precedente, il sistema client accede a un'applicazione personalizzata con il back-end Oracle tramite il Web. Il front-end Web è configurato in un servizio di bilanciamento del carico. Il front-end Web effettua una chiamata al server applicazioni appropriato per gestire il lavoro. Il server applicazioni esegue una query sul database Oracle primario. Il database Oracle è stato configurato usando una [macchina virtuale con ottimizzazione](../../sizes-memory.md) per la memoria con Hyper-Threading con [vCPU Core vincolati](../../../virtual-machines/constrained-vcpu.md) per risparmiare sui costi di licenza e ottimizzare le prestazioni. Per le prestazioni e la disponibilità elevata vengono usati più dischi Premium o Ultra (Managed Disks).

I database Oracle sono posizionati in più zone di disponibilità per la disponibilità elevata. Ogni zona è costituita da uno o più data center dotati di alimentazione, raffreddamento e rete indipendenti. Per garantire la resilienza, vengono configurate almeno tre zone separate in tutte le aree abilitate. La separazione fisica delle zone di disponibilità all'interno di un'area protegge i dati dagli errori data center. Inoltre, due osservatori FSFO vengono configurati in due zone di disponibilità per iniziare ed effettuare il failover del database nel database secondario quando si verifica un'interruzione. 

È possibile configurare altri osservatori e/o database di standby in una zona di disponibilità diversa (AZ 1, in questo caso) rispetto alla zona indicata nell'architettura precedente. Infine, i database Oracle vengono monitorati per tempi di esecuzione e prestazioni da Oracle Enterprise Manager (OEM). OEM consente inoltre di generare vari report sulle prestazioni e sull'utilizzo.

Nelle aree in cui le zone di disponibilità non sono supportate, è possibile usare i set di disponibilità per distribuire il Oracle Database in modo a disponibilità elevata. I set di disponibilità consentono di ottenere un tempo di esecuzione della macchina virtuale pari al 99,95%. Il diagramma seguente è un'architettura di riferimento di questo uso:

![Oracle Database usando i set di disponibilità con Data Guard broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * Non è necessario inserire la VM Oracle Enterprise Manager in un set di disponibilità perché è in corso la distribuzione di una sola istanza di OEM.
> * I dischi Ultra non sono attualmente supportati in una configurazione del set di disponibilità.

#### <a name="oracle-data-guard-far-sync"></a>Oracle Data Guard per la sincronizzazione

Oracle Data Guard lontano Sync fornisce la funzionalità di protezione della perdita dei dati per i database Oracle. Questa funzionalità consente di evitare la perdita di dati in se il computer del database ha esito negativo. È necessario installare Oracle Data Guard per la sincronizzazione in una macchina virtuale separata. Il termine sincronizzazione è un'istanza di Oracle Lightweight che include solo un file di controllo, un file di password, SPFile e i log di standby. Non sono presenti file di dati o file di log rego. 

Per la protezione da nessuna perdita di dati, è necessario che sia presente una comunicazione sincrona tra il database primario e l'istanza di sincronizzazione estrema. L'istanza di sincronizzazione estrema riceve la ripetizione dal database primario in modo sincrono e la inoltra immediatamente a tutti i database di standby in modo asincrono. Questa configurazione consente inoltre di ridurre l'overhead sul database primario, in quanto deve solo inviare il rollforward all'istanza di sincronizzazione più lontana invece che a tutti i database di standby. Se si verifica un errore in un'istanza di sincronizzazione, Data Guard usa automaticamente il trasporto asincrono nel database secondario dal database primario per gestire la protezione da una perdita di dati quasi nulla. Per una resilienza aggiuntiva, i clienti possono distribuire più istanze di sincronizzazione lontano per ogni istanza di database (primaria e secondaria).

Il diagramma seguente è un'architettura a disponibilità elevata che usa Oracle Data Guard per la sincronizzazione:

![Database Oracle con le zone di disponibilità con Data Guard per la sincronizzazione & broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

Nell'architettura precedente è presente un'istanza di sincronizzazione molto diffusa distribuita nella stessa zona di disponibilità dell'istanza del database per ridurre la latenza tra i due. Nei casi in cui l'applicazione è sensibile alla latenza, valutare la possibilità di distribuire il database e l'istanza o le istanze di sincronizzazione in un [gruppo di posizionamento di prossimità](../../../virtual-machines/linux/proximity-placement-groups.md).

Il diagramma seguente è un'architettura che usa Oracle Data Guard FSFO e la sincronizzazione a lungo per ottenere la disponibilità elevata e il ripristino di emergenza:

![Oracle Database uso delle zone di disponibilità per il ripristino di emergenza con Data Guard per la sincronizzazione & broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate consente lo scambio e la manipolazione dei dati a livello di transazione tra più piattaforme eterogenee nell'azienda. Sposta le transazioni di cui è stato eseguito il commit con integrità delle transazioni e un overhead minimo sull'infrastruttura esistente. La sua architettura modulare offre la flessibilità necessaria per estrarre e replicare i record di dati selezionati, le modifiche transazionali e le modifiche apportate a DDL (Data Definition Language) in un'ampia gamma di topologie.

Oracle GoldenGate consente di configurare il database per la disponibilità elevata fornendo la replica bidirezionale. In questo modo è possibile configurare una configurazione **multimaster** o **Active-Active**. Il diagramma seguente è un'architettura consigliata per l'installazione di Oracle GoldenGate Active-Active in Azure. Nell'architettura seguente il database Oracle è stato configurato usando una [macchina virtuale con ottimizzazione](../../sizes-memory.md) per la memoria con Hyper-Threading con [vCPU Core vincolati](../../../virtual-machines/constrained-vcpu.md) per risparmiare sui costi di licenza e ottimizzare le prestazioni. Per prestazioni e disponibilità vengono usati più dischi Premium o Ultra (dischi gestiti).

![Oracle Database usando le zone di disponibilità con Data Guard broker-FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Un'architettura simile può essere configurata usando i set di disponibilità nelle aree in cui le zone di disponibilità non sono attualmente disponibili.

Oracle GoldenGate include processi come Estrai, pompe e replica che consentono di replicare in modo asincrono i dati da un server di database Oracle a un altro. Questi processi consentono di configurare una replica bidirezionale per garantire la disponibilità elevata del database in caso di tempi di inattività a livello di zona di disponibilità. Nel diagramma precedente, il processo di estrazione viene eseguito sullo stesso server del database Oracle, mentre i processi di data pump e replica vengono eseguiti in un server separato nella stessa zona di disponibilità. Il processo replica viene utilizzato per ricevere dati dal database nell'altra zona di disponibilità ed eseguire il commit dei dati nel database Oracle nella relativa zona di disponibilità. Analogamente, il processo data pump invia i dati estratti dal processo di estrazione al processo replica nell'altra zona di disponibilità. 

Mentre il diagramma dell'architettura precedente Mostra il processo data pump e replicate configurato in un server separato, è possibile configurare tutti i processi Oracle GoldenGate sullo stesso server, in base alla capacità e all'utilizzo del server. Per comprendere il modello di utilizzo del server, consultare sempre il report AWR e le metriche in Azure.

Quando si configura la replica bidirezionale Oracle GoldenGate in diverse zone di disponibilità o in aree diverse, è importante assicurarsi che la latenza tra i diversi componenti sia accettabile per l'applicazione. La latenza tra le aree di disponibilità e le aree può variare e dipende da più fattori. È consigliabile configurare i test delle prestazioni tra il livello applicazione e il livello di database in diverse zone di disponibilità e/o aree per verificare che soddisfino i requisiti di prestazioni dell'applicazione.

Il livello applicazione può essere configurato nella propria subnet e il livello del database può essere separato in una subnet specifica. Quando possibile, è consigliabile usare [applicazione Azure gateway](../../../application-gateway/overview.md) per bilanciare il carico del traffico tra i server applicazioni. Applicazione Azure gateway è un servizio di bilanciamento del carico del traffico web affidabile. Fornisce affinità di sessione basata su cookie che consente di mantenere una sessione utente sullo stesso server, riducendo così al minimo i conflitti nel database. Le alternative al gateway applicazione sono [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) e [Gestione traffico di Azure](../../../traffic-manager/traffic-manager-overview.md).

### <a name="oracle-sharding"></a>Partizionamento orizzontale Oracle

Il partizionamento orizzontale è un modello di livello dati introdotto in Oracle 12,2. Consente di partizionare orizzontalmente e ridimensionare i dati in database indipendenti. Si tratta di un'architettura senza condivisione, in cui ogni database è ospitato in una macchina virtuale dedicata, che consente una velocità effettiva di lettura e scrittura elevata, oltre alla resilienza e alla disponibilità aumentata. Questo modello Elimina i singoli punti di errore, fornisce l'isolamento degli errori e Abilita gli aggiornamenti in sequenza senza tempi di inattività. Il tempo di inattività di una partizione o di un errore a livello di data center non influisce sulle prestazioni o sulla disponibilità delle altre partizioni in altri Data Center. 

Il partizionamento orizzontale è adatto per applicazioni OLTP a velocità effettiva elevata che non possono permettersi tempi di inattività. Tutte le righe con la stessa chiave di partizionamento orizzontale sono sempre sempre presenti nella stessa partizione, aumentando così le prestazioni garantendo una coerenza elevata. Le applicazioni che usano il partizionamento orizzontale devono avere un modello di dati ben definito e una strategia di distribuzione dei dati (hash coerente, intervallo, elenco o composito) che accedono principalmente ai dati usando una chiave di partizionamento orizzontale (ad esempio, *CustomerID* o *AccountNum*). Il partizionamento orizzontale consente inoltre di archiviare determinati set di dati più vicini ai clienti finali, consentendo così di soddisfare i requisiti di prestazioni e conformità.

Si consiglia di replicare le partizioni per la disponibilità elevata e il ripristino di emergenza. Questa configurazione può essere eseguita usando tecnologie Oracle come Oracle Data Guard o Oracle GoldenGate. Un'unità di replica può essere una partizione, una parte di una partizione o un gruppo di partizioni. La disponibilità di un database partizionato non è interessata da un'interruzione o da un rallentamento di una o più partizioni. Per la disponibilità elevata, le partizioni di standby possono essere posizionate nella stessa zona di disponibilità in cui vengono inserite le partizioni primarie. Per il ripristino di emergenza, le partizioni di standby possono trovarsi in un'altra area. È anche possibile distribuire le partizioni in più aree per gestire il traffico in tali aree. Per altre informazioni sulla configurazione della disponibilità elevata e della replica del database partizionato, vedere la [documentazione sul partizionamento orizzontale di Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html).

Il partizionamento orizzontale Oracle è costituito principalmente dai componenti seguenti. Altre informazioni su questi componenti sono disponibili nella [documentazione di partizionamento orizzontale di Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html):

- **Catalogo** partizioni: database Oracle per scopi specifici che è un archivio permanente per tutti i dati di configurazione del database di partizionamento. Tutte le modifiche alla configurazione, ad esempio l'aggiunta o la rimozione di partizioni, il mapping dei dati e DDL in un database di partizionamento, vengono avviate nel catalogo partizioni. Il catalogo della partizione contiene anche la copia master di tutte le tabelle duplicate in un oggetto SDB. 

  Il catalogo partizioni USA viste materializzate per replicare automaticamente le modifiche apportate alle tabelle duplicate in tutte le partizioni. Il database del catalogo di partizionamento funge anche da coordinatore di query usato per elaborare query su più partizioni e query che non specificano una chiave di partizionamento orizzontale. 
  
  L'uso di Oracle Data Guard in combinazione con le zone di disponibilità o i set di disponibilità per la disponibilità elevata del catalogo partizioni è una procedura consigliata. La disponibilità del catalogo partizioni non ha alcun effetto sulla disponibilità del database partizionato. Un tempo di inattività nel catalogo partizioni influiscono solo sulle operazioni di manutenzione e sulle query multipartizione durante il breve periodo di tempo durante il quale viene completato il failover di Data Guard. Le transazioni online continuano a essere instradate ed eseguite da SDB e non sono interessate da un'interruzione del catalogo.

- **Direttori** di partizioni: servizi Lightweight che devono essere distribuiti in ogni area/zona di disponibilità in cui risiedono le partizioni. I direttori di partizioni sono responsabili dei servizi globali distribuiti nel contesto del partizionamento orizzontale Oracle. Per la disponibilità elevata, è consigliabile distribuire almeno un Director di partizioni in ogni zona di disponibilità in cui si trovano le partizioni. 

  Quando si esegue la connessione al database inizialmente, le informazioni di routing vengono impostate da un Director di partizioni e vengono memorizzate nella cache per le richieste successive, ignorando il Director di partizionamento. Quando la sessione viene stabilita con una partizione, tutte le query SQL e I DML sono supportate ed eseguite nell'ambito della partizione specificata. Questo routing è veloce e viene usato per tutti i carichi di lavoro OLTP che eseguono transazioni intra-partizione. È consigliabile usare il routing diretto per tutti i carichi di lavoro OLTP che richiedono le prestazioni e la disponibilità più elevate. La cache di routing viene aggiornata automaticamente quando una partizione diventa non disponibile o si verificano modifiche alla topologia di partizionamento orizzontale. 
  
  Per il routing dipendente dai dati ad alte prestazioni, Oracle consiglia di usare un pool di connessioni per l'accesso ai dati nel database partizionato. I pool di connessioni Oracle, le librerie specifiche del linguaggio e i driver supportano il partizionamento orizzontale Oracle. Per altri dettagli, vedere la [documentazione di partizionamento orizzontale di Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9) .

- **Servizio globale** : il servizio globale è simile al normale servizio di database. Oltre a tutte le proprietà di un servizio di database, un servizio globale dispone di proprietà per i database partizionati, ad esempio l'affinità dell'area tra i client e la tolleranza di ritardo di replica e partizione. È necessario creare un solo servizio globale per leggere/scrivere dati da e verso un database partizionato. Quando si usa Active Data Guard e si configurano le repliche di sola lettura delle partizioni, è possibile creare un altro servizio gGobal per i carichi di lavoro di sola lettura. Il client può utilizzare questi servizi globali per connettersi al database.

- **Database di partizionamento** : i database di partizione sono i database Oracle. Ogni database viene replicato con Oracle Data Guard in una configurazione di Service Broker con Fast-Start failover (FSFO) abilitato. Non è necessario configurare il failover e la replica di Data Guard in ogni partizione. Questa configurazione viene configurata e distribuita automaticamente quando viene creato il database condiviso. Se una determinata partizione ha esito negativo, la condivisione Oracle esegue automaticamente il failover delle connessioni del database dal database primario a quello standby.

È possibile distribuire e gestire i database partizionati Oracle con due interfacce: l'interfaccia utente grafica di controllo cloud di Oracle Enterprise Manager e/o l' `GDSCTL` utilità della riga di comando. È anche possibile monitorare le diverse partizioni per la disponibilità e le prestazioni usando il controllo cloud. Il `GDSCTL DEPLOY` comando crea automaticamente le partizioni e i rispettivi listener. Inoltre, questo comando distribuisce automaticamente la configurazione di replica usata per la disponibilità elevata a livello di partizione specificata dall'amministratore.

Esistono diversi modi per partizionare un database:

* Partizionamento orizzontale gestito dal sistema: distribuisce automaticamente tra le partizioni usando il partizionamento
* Partizionamento orizzontale definito dall'utente: consente di specificare il mapping dei dati alle partizioni, che funziona bene in presenza di requisiti normativi o di localizzazione dei dati.
* Partizionamento orizzontale composito: combinazione di partizionamento orizzontale gestito dal sistema e definito dall'utente per _shardspaces_ diversi
* Sottopartizioni di tabella: simili a quelle di una normale tabella partizionata.

Per altre informazioni sui diversi [metodi di partizionamento orizzontale](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) , vedere la documentazione di Oracle.

Anche se un database partizionato può apparire come un database singolo per applicazioni e sviluppatori, quando si esegue la migrazione da un database non partizionato in un database partizionato, è necessaria un'attenta pianificazione per determinare quali tabelle verranno duplicate rispetto al partizionamento orizzontale. 

Le tabelle duplicate vengono archiviate in tutte le partizioni, mentre le tabelle partizionate vengono distribuite tra partizioni diverse. Si consiglia di duplicare le tabelle di dimensioni ridotte e dimensionali e distribuire/partizionare le tabelle dei fatti. I dati possono essere caricati nel database partizionato usando il catalogo di partizioni come coordinatore centrale o eseguendo data pump in ogni partizione. Per altre informazioni sulla [migrazione dei dati in un database partizionato](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) , vedere la documentazione di Oracle.

#### <a name="oracle-sharding-with-data-guard"></a>Partizionamento orizzontale Oracle con Data Guard

Oracle Data Guard può essere usato per il partizionamento orizzontale con metodi gestiti dal sistema, definiti dall'utente e di partizionamento orizzontale composito.

Il diagramma seguente è un'architettura di riferimento per il partizionamento orizzontale Oracle con Oracle Data Guard usato per la disponibilità elevata di ogni partizione. Il diagramma dell'architettura Mostra un _metodo di partizionamento orizzontale composito_. Il diagramma dell'architettura sarà probabilmente diverso per le applicazioni con requisiti diversi per la località dei dati, il bilanciamento del carico, la disponibilità elevata, il ripristino di emergenza e così via e può usare un metodo diverso per il partizionamento orizzontale. Il partizionamento orizzontale di Oracle consente di soddisfare questi requisiti e di ridimensionarsi orizzontalmente ed efficacemente fornendo queste opzioni. Un'architettura simile può anche essere distribuita usando Oracle GoldenGate.

![Oracle Database il partizionamento orizzontale con le zone di disponibilità con Data Guard broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Sebbene il partizionamento orizzontale gestito dal sistema sia il più semplice da configurare e gestire, il partizionamento orizzontale definito dall'utente o il partizionamento orizzontale composito è particolarmente adatto per scenari in cui i dati e l'applicazione sono distribuiti geograficamente o in scenari in cui è necessario controllare la replica di ogni partizione. 

Nell'architettura precedente, il partizionamento orizzontale composito viene usato per distribuire geograficamente i dati e scalare orizzontalmente i livelli di applicazione. Il partizionamento orizzontale composito è una combinazione di partizionamento orizzontale gestito dal sistema e definito dall'utente e offre quindi il vantaggio di entrambi i metodi. Nello scenario precedente i dati vengono innanzitutto partizionati in più shardspaces separati da un'area. Quindi, i dati vengono ulteriormente partizionati in base all'hash coerente tra più partizioni in shardspace. Ogni shardspace contiene più shardgroups. Ogni shardgroup dispone di più partizioni ed è una "unità" della replica, in questo caso. Ogni shardgroup contiene tutti i dati in shardspace. Shardgroups a1 e B1 sono Shardgroups primari, mentre Shardgroups a2 e B2 sono standbys. È possibile scegliere di fare in modo che le singole partizioni siano l'unità di replica, anziché una shardgroup.

Nell'architettura precedente, un Director GSM/partizione viene distribuito in ogni zona di disponibilità per la disponibilità elevata. Si consiglia di distribuire almeno un Director GSM/partizione per data center/area. Inoltre, un'istanza del server applicazioni viene distribuita in ogni zona di disponibilità che contiene un shardgroup. Questa configurazione consente all'applicazione di mantenete la latenza tra il server applicazioni e il database/shardgroup basso. Se un database ha esito negativo, il server applicazioni nella stessa zona del database di standby può gestire le richieste una volta eseguita la transizione del ruolo del database. Applicazione Azure gateway e il Director di partizionamento tengono traccia della latenza di richiesta e risposta e indirizzano le richieste di conseguenza.

Dal punto di vista dell'applicazione, il sistema client invia una richiesta al gateway applicazione Azure (o ad altre tecnologie di bilanciamento del carico in Azure) che reindirizza la richiesta all'area più vicina al client. Applicazione Azure gateway supporta anche le sessioni permanenti, quindi tutte le richieste provenienti dallo stesso client vengono indirizzate allo stesso server applicazioni. Il server applicazioni utilizza il pool di connessioni nei driver di accesso ai dati. Questa funzionalità è disponibile in driver quali JDBC, ODP.NET, OCI e così via. I driver sono in grado di riconoscere le chiavi di partizionamento orizzontale specificate come parte della richiesta. Il [pool di connessioni Oracle Universal (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) per i client JDBC può abilitare client di applicazioni non Oracle come Apache Tomcat e IIS per l'uso con il partizionamento orizzontale Oracle. 

Durante la richiesta iniziale, il server applicazioni si connette al Director di partizionamento nella propria area per ottenere informazioni di routing per la partizione a cui deve essere indirizzata la richiesta. In base alla chiave di partizionamento orizzontale passata, il Director instrada il server applicazioni alla rispettiva partizione. Il server applicazioni memorizza nella cache queste informazioni compilando una mappa e, per le successive richieste, ignora il Director partizioni e instrada le richieste direttamente alla partizione.

#### <a name="oracle-sharding-with-goldengate"></a>Partizionamento orizzontale Oracle con GoldenGate

Il diagramma seguente è un'architettura di riferimento per il partizionamento orizzontale Oracle con Oracle GoldenGate per la disponibilità elevata in un'area di ogni partizione. Rispetto all'architettura precedente, questa architettura rappresenta solo la disponibilità elevata all'interno di una singola area di Azure (più zone di disponibilità). È possibile distribuire un database partizionato a disponibilità elevata in più aree (simile all'esempio precedente) usando Oracle GoldenGate.

![Oracle Database il partizionamento orizzontale usando le zone di disponibilità con GoldenGate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

L'architettura di riferimento precedente usa il metodo di partizionamento orizzontale _gestito dal sistema_ per partizionare i dati. Poiché la replica di Oracle GoldenGate viene eseguita a livello di blocco, la metà dei dati replicati in una partizione può essere replicata in un'altra partizione. L'altra metà può essere replicata in un'altra partizione. 

Il modo in cui i dati vengono replicati dipende dal fattore di replica. Con un fattore di replica di 2, si disporrà di due copie di ogni blocco di dati tra le tre partizioni nella shardgroup. Analogamente, con un fattore di replica di 3 e tre partizioni nella shardgroup, tutti i dati in ogni partizione verranno replicati in ogni altra partizione in shardgroup. Ogni partizione in shardgroup può avere un fattore di replica diverso. Questa configurazione consente di definire in modo efficiente la progettazione della disponibilità elevata e del ripristino di emergenza all'interno di una shardgroup e tra più shardgroups.

Nell'architettura precedente shardgroup A e shardgroup B contengono entrambi gli stessi dati ma risiedono in zone di disponibilità diverse. Se sia shardgroup A che shardgroup B hanno lo stesso fattore di replica 3, ogni riga o blocco della tabella partizionata verrà replicato 6 volte tra i due shardgroups. Se shardgroup A ha un fattore di replica di 3 e shardgroup B ha un fattore di replica pari a 2, ogni riga/blocco verrà replicato 5 volte tra i due shardgroups.

Questa configurazione impedisce la perdita di dati se si verifica un errore a livello di istanza o di zona di disponibilità. Il livello dell'applicazione è in grado di leggere e scrivere in ogni partizione. Per ridurre al minimo i conflitti, il partizionamento orizzontale Oracle designa un "blocco Master" per ogni intervallo di valori hash. Questa funzionalità garantisce che le Scritture richieste per un determinato blocco vengano indirizzate al blocco corrispondente. Oracle GoldenGate fornisce inoltre il rilevamento e la risoluzione dei conflitti automatici per gestire eventuali conflitti che possono verificarsi. Per altre informazioni e limitazioni relative all'implementazione di GoldenGate con il partizionamento orizzontale Oracle, vedere la documentazione di Oracle sull'uso di [Oracle GoldenGate con un database partizionato](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72).

Nell'architettura precedente, un Director GSM/partizione viene distribuito in ogni zona di disponibilità per la disponibilità elevata. Si consiglia di distribuire almeno un Director GSM/partizione per data center o regione. Inoltre, un'istanza del server applicazioni viene distribuita in ogni zona di disponibilità che contiene un shardgroup. Questa configurazione consente all'applicazione di mantenete la latenza tra il server applicazioni e il database/shardgroup basso. Se un database ha esito negativo, il server applicazioni nella stessa zona del database standby può gestire le richieste dopo la transizione del ruolo del database. Applicazione Azure gateway e il Director di partizionamento tengono traccia della latenza di richiesta e risposta e indirizzano le richieste di conseguenza.

Dal punto di vista dell'applicazione, il sistema client invia una richiesta al gateway applicazione Azure (o ad altre tecnologie di bilanciamento del carico in Azure) che reindirizza la richiesta all'area più vicina al client. Applicazione Azure gateway supporta anche le sessioni permanenti, quindi tutte le richieste provenienti dallo stesso client vengono indirizzate allo stesso server applicazioni. Il server applicazioni utilizza il pool di connessioni nei driver di accesso ai dati. Questa funzionalità è disponibile in driver quali JDBC, ODP.NET, OCI e così via. I driver sono in grado di riconoscere le chiavi di partizionamento orizzontale specificate come parte della richiesta. Il [pool di connessioni Oracle Universal (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) per i client JDBC può abilitare client di applicazioni non Oracle come Apache Tomcat e IIS per l'uso con il partizionamento orizzontale Oracle. 

Durante la richiesta iniziale, il server applicazioni si connette al Director di partizionamento nella propria area per ottenere informazioni di routing per la partizione a cui deve essere indirizzata la richiesta. In base alla chiave di partizionamento orizzontale passata, il Director instrada il server applicazioni alla rispettiva partizione. Il server applicazioni memorizza nella cache queste informazioni compilando una mappa e, per le successive richieste, ignora il Director partizioni e instrada le richieste direttamente alla partizione.

## <a name="patching-and-maintenance"></a>Applicazione di patch e manutenzione

Quando si distribuiscono i carichi di lavoro Oracle in Azure, Microsoft si occupa di tutte le patch a livello di sistema operativo host. Ogni manutenzione pianificata a livello di sistema operativo viene comunicata ai clienti in anticipo per consentire al cliente la manutenzione pianificata. Due server di due zone di disponibilità diversi non vengono mai sottoposti a patch simultaneamente. Vedere [gestire la disponibilità delle macchine virtuali](../../manage-availability.md) per altri dettagli sulla manutenzione delle VM e sull'applicazione di patch. 

L'applicazione di patch al sistema operativo della macchina virtuale può essere automatizzata usando [automazione di Azure Gestione aggiornamenti](../../../automation/update-management/update-mgmt-overview.md). Per ridurre al minimo i tempi di inattività, è possibile automatizzare e pianificare l'applicazione di patch e la gestione del database Oracle con [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops) o [Gestione aggiornamenti automazione di Azure](../../../automation/update-management/update-mgmt-overview.md) . Vedere [recapito continuo e distribuzioni Blue/Green](/azure/devops/learn/what-is-continuous-delivery) per comprendere come può essere usato nel contesto dei database Oracle.

## <a name="architecture-and-design-considerations"></a>Considerazioni sull'architettura e sulla progettazione

- Si consiglia di usare una [macchina virtuale con ottimizzazione](../../sizes-memory.md) per la memoria con Hyper-Threading con [vCPU Core vincolati](../../../virtual-machines/constrained-vcpu.md) per la VM Oracle database per risparmiare sui costi di licenza e ottimizzare le prestazioni. Usare più dischi Premium o Ultra (Managed Disks) per prestazioni e disponibilità.
- Quando si usa Managed disks, il nome del disco o del dispositivo può variare al riavvio. È consigliabile usare l'UUID del dispositivo anziché il nome per assicurarsi che i montaggi vengano mantenuti tra i riavvii. Altre informazioni sono disponibili [qui](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab).
- Usare le zone di disponibilità per ottenere la disponibilità elevata in-Region.
- Si consiglia di usare dischi Ultra (se disponibili) o dischi Premium per il database Oracle.
- Si consiglia di configurare un database Oracle di standby in un'altra area di Azure usando Oracle Data Guard.
- Per ridurre la latenza tra l'applicazione e il livello del database, provare a usare i [gruppi di posizionamento prossimità](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) .
- Configurare [Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) per la gestione, il monitoraggio e la registrazione.
- Prendere in considerazione l'uso di Oracle Automatic Storage Management (ASM) per la gestione semplificata dell'archiviazione per il database.
- Usare [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) per gestire l'applicazione di patch e gli aggiornamenti al database senza tempi di inattività.
- Modificare il codice dell'applicazione per aggiungere modelli nativi del cloud, come il [modello di ripetizione dei tentativi](/azure/architecture/patterns/retry), il modello di [interruttore](/azure/architecture/patterns/circuit-breaker)e altri modelli definiti nella Guida ai [modelli di progettazione cloud](/azure/architecture/patterns/) che potrebbero aiutare l'applicazione a essere più resiliente.

## <a name="next-steps"></a>Passaggi successivi

Esaminare gli articoli di riferimento Oracle seguenti applicabili allo scenario.

- [Introduzione a Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Concetti relativi a Oracle Data Guard broker](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Configurazione di Oracle GoldenGate per la disponibilità elevata Active-Active](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Panoramica del partizionamento orizzontale Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [La sincronizzazione dei dati da parte di Oracle Active Data Guard è zero a qualsiasi distanza](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)