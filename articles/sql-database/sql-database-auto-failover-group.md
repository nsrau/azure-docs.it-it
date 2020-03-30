---
title: Gruppi di failover
description: I gruppi di failover automatico sono una funzionalità del database SQL che consente di gestire la replica e il failover automatico/coordinato di un gruppo di database in un server di database SQL o tutti i database in un'istanza gestita.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 2/10/2020
ms.openlocfilehash: 6d87d3373711d12df3f2cced26ef35ae951ad41e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269835"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Usare i gruppi di failover automatico per consentire il failover trasparente e coordinato di più database

Gruppi di failover automatico è una funzionalità di database SQL che consente di gestire la replica e il failover di un gruppo di database in un server di database SQL o di tutti i database di un'istanza gestita in un'altra area. Si tratta di un'astrazione dichiarativa in cima alla funzionalità di [replica geografica attiva](sql-database-active-geo-replication.md) esistente, progettata per semplificare la distribuzione e la gestione di database con replica geografica su larga scala. È possibile avviare manualmente il failover oppure delegarlo al servizio del database SQL in base a criteri definiti dall'utente. Grazie a questa seconda opzione è possibile ripristinare automaticamente più database correlati in un'area secondaria dopo un errore irreversibile o un altro evento imprevisto che comporta la perdita parziale o completa di disponibilità del servizio del database SQL nell'area primaria. Un gruppo di failover può includere uno o più database, in genere utilizzati dalla stessa applicazione. È inoltre possibile usare i database secondari leggibili per l'offload dei carichi di lavoro delle query di sola lettura. Poiché i gruppi di failover automatico coinvolgono più database, questi ultimi devono essere configurati nel server primario. I gruppi di failover automatico supportano la replica di tutti i database nel gruppo in un solo server secondario in un'area diversa.

> [!NOTE]
> Se si usano database singoli o in pool in un server di database SQL e si intende avere più database secondari nella stessa area geografica o in aree diverse, usare la [replica geografica attiva](sql-database-active-geo-replication.md). 

Se si usano gruppi di failover automatico con criteri di failover automatico, eventuali interruzioni che influiscono su uno o più database nel gruppo determinano un failover automatico. In genere si tratta di eventi imprevisti che non possono essere autoattenuati dalle operazioni automatiche di disponibilità elevata incorporate. Gli esempi di trigger di failover includono un evento imprevisto causato da un anello tenant SQL o un anello di controllo inattivo a causa di una perdita di memoria del kernel del sistema operativo in diversi nodi di calcolo o un incidente causato da uno o più anelli tenant inattivo perché un cavo di rete errato è stato interrotto durante di routine di disattivazione dell'hardware.  Per ulteriori informazioni, vedere [Disponibilità elevata del database SQL](sql-database-high-availability.md).

I gruppi di failover automatico forniscono anche endpoint di listener di sola lettura e di sola scrittura che rimangono invariati durante i failover. Se si usa l'attivazione di failover manuale o automatica, il failover trasforma tutti i database secondari nel gruppo in database primari. Dopo aver completato il failover del database, il record DNS viene automaticamente aggiornato per reindirizzare gli endpoint alla nuova area. Per i dati RPO e RTO specifici consultare la [panoramica della continuità aziendale](sql-database-business-continuity.md).

Se si usano gruppi di failover automatico con criteri di failover automatico, eventuali interruzioni che influiscono sui database nel server di database SQL o nell'istanza gestita determinano un failover automatico. È possibile gestire il gruppo di failover automatico mediante:

- [Portale di Azure](sql-database-implement-geo-distributed-database.md)
- [Interfaccia di comando di Azure: gruppo di failoverAzure CLI: Failover Group](scripts/sql-database-add-single-db-to-failover-group-cli.md)
- [PowerShell: gruppo di failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [API REST: gruppo di failover](/rest/api/sql/failovergroups).

Dopo il failover, verificare che nel nuovo database primario siano configurati i requisiti di autenticazione relativi al server e al database. Per tutti i dettagli, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

Per ottenere una reale continuità aziendale, l'aggiunta di ridondanza dei database tra i data center rappresenta solo una parte della soluzione. Per ripristinare un'applicazione (servizio) end-to-end dopo un problema grave, è necessario effettuare il ripristino di tutti i componenti del servizio e gli eventuali servizi dipendenti. Esempi di questi componenti includono il software client (ad esempio, un browser con JavaScript personalizzato), front-end Web, spazio di archiviazione e DNS. È fondamentale che tutti i componenti siano resilienti agli stessi problemi e diventino disponibili entro I'obiettivo del tempo di ripristino (RTO) dell'applicazione. È perciò necessario identificare tutti i servizi dipendenti e comprendere quali garanzie e funzionalità vengono fornite. È quindi necessario intraprendere le azioni appropriate per assicurare il funzionamento del servizio durante il failover dei servizi da cui dipende. Per altre informazioni sulla progettazione di soluzioni per il ripristino di emergenza, vedere [Progettazione di applicazioni per il ripristino di emergenza cloud con la replica geografica attiva in database SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Funzionalità e terminologia dei gruppi di failover automatico

- **Gruppo di failover (FOG)**

  Un gruppo di failover è un gruppo denominato di database gestito da un singolo server di database SQL o all'interno di una singola istanza gestita che può eseguire il failover come un'unità in un'altra area nel caso in cui tutti o alcuni database primari non siano più disponibili a causa di un'interruzione nell'area primaria. Quando viene creato per le istanze gestite, un gruppo di failover contiene tutti i database utente nell'istanza e pertanto è possibile configurare un solo gruppo di failover in un'istanza.
  
  > [!IMPORTANT]
  > Il nome del gruppo di failover deve `.database.windows.net` essere univoco a livello globale all'interno del dominio.

- **Server di database SQL**

     Con i server di database SQL è possibile inserire alcuni o tutti i database utente di un singolo server di database SQL in un gruppo di failover. Un server di database SQL supporta anche più gruppi di failover in un singolo server di database SQL.

- **Primario**

  Il server di database SQL o l'istanza gestita che ospita i database primari nel gruppo di failover.

- **Secondari**

  Il server di database SQL o l'istanza gestita che ospita i database secondari nel gruppo di failover. Il server o l'istanza secondaria non può trovarsi nella stessa area del server o dell'istanza primaria.

- **Aggiunta di database singoli al gruppo di failover**

  È possibile inserire più database singoli nello stesso server di database SQL all'interno dello stesso gruppo di failover. Se si aggiunge un database singolo al gruppo di failover, viene creato automaticamente un database secondario usando la stessa edizione e le stesse dimensioni di calcolo nel server secondario.  Tale server è stato specificato quando è stato creato il gruppo di failover. Se si aggiunge un database che ha già un database secondario nel server secondario, tale collegamento della replica geografica viene ereditato dal gruppo. Quando si aggiunge un database che dispone già di un database secondario in un server che non fa parte del gruppo di failover, viene creato un nuovo database secondario nel server secondario.

  > [!IMPORTANT]
  > Assicurarsi che il server secondario non disponga di un database con lo stesso nome, a meno che non si tratti di un database secondario esistente. Nei gruppi di failover per l'istanza gestita vengono replicati tutti i database utente. Non è possibile selezionare un subset di database utente per la replica nel gruppo di failover.

- **Aggiunta di database nel pool elastico al gruppo di failover**

  È possibile inserire tutti o alcuni database all'interno di un pool elastico nello stesso gruppo di failover. Se il database primario si trova in un pool elastico, il database secondario viene creato automaticamente nel pool elastico con lo stesso nome (pool secondario). È necessario assicurarsi che il server secondario contenga un pool elastico con lo stesso identico nome e capacità sufficiente per ospitare i database secondari che verranno creati dal gruppo di failover. Se si aggiunge un database nel pool che ha già un database secondario nel pool secondario, tale collegamento della replica geografica viene ereditato dal gruppo. Quando si aggiunge un database che ha già un database secondario in un server che non fa parte del gruppo di failover, viene creato un nuovo database secondario nel pool secondario.
  
- **Seeding iniziale** 

  Quando si aggiungono database, pool elastici o istanze gestite a un gruppo di failover, viene eseguita una fase di seeding iniziale prima dell'avvio della replica dei dati. La fase di seeding iniziale è l'operazione più lunga e costosa. Al termine del seeding iniziale, i dati vengono sincronizzati e quindi vengono replicate solo le modifiche successive ai dati. Il tempo necessario per il completamento del seeding iniziale dipende dalle dimensioni dei dati, dal numero di database replicati e dalla velocità del collegamento tra le entità nel gruppo di failover. In circostanze normali, la velocità di seeding tipica è 50-500 GB all'ora per un singolo database o pool elastico e 18-35 GB all'ora per un'istanza gestita. Il seeding viene eseguito per tutti i database in parallelo. È possibile utilizzare la velocità di seeding indicata, insieme al numero di database e alla dimensione totale dei dati per stimare il tempo necessario alla fase di seeding iniziale prima dell'inizio della replica dei dati.

  Per le istanze gestite, è necessario considerare anche la velocità del collegamento Express Route tra le due istanze quando si stima il tempo della fase di seeding iniziale. Se la velocità del collegamento tra le due istanze è più lenta di quella necessaria, il tempo necessario per il seeding è probabilmente notevolmente influenzato. È possibile utilizzare la velocità di seeding indicata, il numero di database, la dimensione totale dei dati e la velocità di collegamento per stimare la durata della fase di seeding iniziale prima dell'avvio della replica dei dati. Ad esempio, per un singolo database da 100 GB, la fase di inizializzazione iniziale richiederebbe da 2,8 a 5,5 ore se il collegamento è in grado di eseguire il push di 35 GB all'ora. Se il collegamento può trasferire solo 10 GB all'ora, il seeding di un database da 100 GB richiederà circa 10 ore. Se sono presenti più database da replicare, il seeding verrà eseguito in parallelo e, se combinato con una velocità di collegamento lenta, la fase di seeding iniziale potrebbe richiedere molto più tempo, soprattutto se il seeding parallelo dei dati di tutti i database supera la velocità disponibile larghezza di banda del collegamento. Se la larghezza di banda di rete tra due istanze è limitata e si aggiungono più istanze gestite a un gruppo di failover, è consigliabile aggiungere più istanze gestite al gruppo di failover in sequenza, una alla volta.

  
- **Zona DNS**

  ID univoco generato automaticamente quando viene creata una nuova istanza. Viene eseguito il provisioning di un certificato multidominio (SAN) per questa istanza per autenticare le connessioni client a qualsiasi istanza nella stessa zona DNS. Le due istanze gestite nello stesso gruppo di failover devono condividere la zona DNS.
  
  > [!NOTE]
  > Non è necessario un ID zona DNS per i gruppi di failover creati per i server di database SQL.

- **Listener di lettura/scrittura del gruppo di failover**

  Un record CNAME DNS che punta all'URL del database primario corrente. Viene creato automaticamente quando viene creato il gruppo di failover e consente al carico di lavoro SQL di lettura/scrittura di riconnettersi in modo trasparente al database primario quando il database primario cambia dopo il failover. Quando il gruppo di failover viene creato in un server di database `<fog-name>.database.windows.net`SQL, il record CNAME DNS per l'URL del listener viene formato come . Quando il gruppo di failover viene creato in un'istanza gestita, `<fog-name>.zone_id.database.windows.net`il record CNAME DNS per l'URL del listener viene formato come .

- **Listener di sola lettura del gruppo di failover**

  Un record CNAME DNS che punta al listener di sola lettura che punta all'URL del database secondario. Viene creato automaticamente quando viene creato il gruppo di failover e consente al carico di lavoro SQL di sola lettura di connettersi in modo trasparente al database secondario usando le regole di bilanciamento del carico specificate. Quando il gruppo di failover viene creato in un server di database `<fog-name>.secondary.database.windows.net`SQL, il record CNAME DNS per l'URL del listener viene formato come . Quando il gruppo di failover viene creato in un'istanza gestita, `<fog-name>.zone_id.secondary.database.windows.net`il record CNAME DNS per l'URL del listener viene formato come .

- **Criteri di failover automatico**

  Per impostazione predefinita un gruppo di failover viene configurato con criteri di failover automatico. Il servizio del database SQL attiva il failover dopo che è stato rilevato l'errore ed è scaduto il periodo di prova. Il sistema deve verificare che l'interruzione del servizio non può essere risolta dall'[infrastruttura con disponibilità elevata incorporata del servizio del database SQL](sql-database-high-availability.md) a causa dell'entità dell'impatto. Se si desidera controllare il flusso di lavoro del failover dall'applicazione, è possibile disattivare il failover automatico.
  
  > [!NOTE]
  > Poiché la verifica della scala dell'interruzione e la velocità con cui può essere attenuata comporta azioni umane da parte del team operativo, il periodo di tolleranza non può essere impostato al di sotto di un'ora.  Questa limitazione si applica a tutti i database nel gruppo di failover indipendentemente dallo stato di sincronizzazione dei dati. 

- **Criteri di failover di sola lettura**

  Per impostazione predefinita, il failover del listener di sola lettura è disabilitato. Verificare che le prestazioni del database primario non siano interessate quando il database secondario è offline. Ciò significa anche che le sessioni di sola lettura non sono in grado di connettersi fino a quando il database secondario non viene recuperato. Se non è possibile tollerare il tempo di inattività per le sessioni di sola lettura e si desidera utilizzare temporaneamente il database primario sia di sola `AllowReadOnlyFailoverToPrimary` lettura che per il traffico di lettura/scrittura a scapito della potenziale riduzione delle prestazioni del database primario, è possibile abilitare il failover per il listener di sola lettura configurando la proprietà. In tal caso, il traffico di sola lettura verrà automaticamente reindirizzato al database primario se il database secondario non è disponibile.

- **Failover pianificato**

   Il failover pianificato esegue la sincronizzazione completa tra il database primario e il database secondario prima che il database secondario passi al ruolo primario. In questo modo si esclude la perdita di dati. Il failover pianificato viene usato negli scenari seguenti:

  - Eseguire esercitazioni per il ripristino di emergenza in produzione quando la perdita di dati non è accettabile
  - Rilocare i database in un'area diversa
  - Ripristinare i database nell'area primaria dopo la risoluzione dell'interruzione (failback).

- **Failover non pianificato**

   Con il failover non pianificato o forzato il database secondario passa immediatamente al ruolo primario senza alcuna sincronizzazione. Questa operazione comporta la perdita di dati. Un failover non pianificato viene usato come metodo di ripristino durante le interruzioni quando non è accessibile il database primario. Quando il database primario originale è di nuovo online, si riconnetterà automaticamente senza sincronizzazione e diventerà un nuovo secondario.

- **Failover manuale**

  È possibile avviare manualmente il failover in qualsiasi momento, indipendentemente dalla configurazione del failover automatico. Se non sono configurati criteri di failover automatico, è necessario eseguire il failover manuale per ripristinare i database del gruppo di failover nell'area secondaria. È possibile avviare il failover forzato o semplice (con sincronizzazione completa dei dati). Quest'ultimo può essere usato per rilocare il database primario nell'area secondaria. Quando il failover viene completato, i record DNS vengono aggiornati automaticamente per garantire la connettività al nuovo database primario

- **Periodo di tolleranza con perdita di dati**

  Poiché i database primario e secondario vengono sincronizzati tramite la replica asincrona, il failover può comportare la perdita di dati. È possibile personalizzare i criteri di failover automatico per riflettere la tolleranza dell'applicazione verso la perdita dei dati. Configurando `GracePeriodWithDataLossHours`, è possibile controllare il tempo di attesa del sistema prima di avviare il failover che potrebbe causare la perdita di dati.

- **Più gruppi di failover**

  È possibile configurare più gruppi di failover per la stessa coppia di server per controllare la scalabilità di failover. Ogni gruppo esegue il failover in modo indipendente. Se l'applicazione multi-tenant fa uso di pool elastici, è possibile usare questa funzionalità per combinare i database primari e secondari in ogni pool. In questo modo è possibile ridurre l'impatto di un'interruzione a solo la metà dei tenant.

  > [!NOTE]
  > Istanza gestita non supporta più gruppi di failover.
  
## <a name="permissions"></a>Autorizzazioni

Le autorizzazioni per un gruppo di failover vengono gestite tramite il [controllo degli accessi in base](../role-based-access-control/overview.md)al ruolo. Il ruolo [Collaboratore SQL Server](../role-based-access-control/built-in-roles.md#sql-server-contributor) dispone di tutte le autorizzazioni necessarie per gestire i gruppi di failover.

### <a name="create-failover-group"></a>Create failover group

Per creare un gruppo di failover, è necessario l'accesso in scrittura RBAC ai server primario e secondario e a tutti i database nel gruppo di failover. Per un'istanza gestita, è necessario l'accesso in scrittura RBAC sia all'istanza gestita primaria che a quella secondaria, ma le autorizzazioni per i singoli database non sono rilevanti poiché i singoli database dell'istanza gestita non possono essere aggiunti o rimossi da un gruppo di failover. 

### <a name="update-a-failover-group"></a>Aggiornare un gruppo di failoverUpdate a failover group

Per aggiornare un gruppo di failover, è necessario disporre dell'accesso in codice rbac in scrittura al gruppo di failover e a tutti i database nel server primario o nell'istanza gestita corrente.  

### <a name="failover-a-failover-group"></a>Failover di un gruppo di failover

Per eseguire il failover di un gruppo di failover, è necessario l'accesso in scrittura RBAC al gruppo di failover nel nuovo server primario o nell'istanza gestita.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Procedure consigliate per l'uso di gruppi di failover con database singoli e pool elastici

Il gruppo di failover automatico deve essere configurato nel server di database SQL primario e lo connetterà al server di database SQL secondario in un'altra area di Azure. I gruppi possono includere alcuni o tutti i database in questi server. Il diagramma seguente illustra una configurazione tipica di un'applicazione cloud con ridondanza geografica con più database e un gruppo di failover automatico.

![failover automatico](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Per un'esercitazione dettagliata sull'aggiunta di un singolo database a un gruppo di failover, vedere Aggiungere un singolo database a un gruppo di [failover.](sql-database-single-database-failover-group-tutorial.md)

Quando si progetta un servizio facendo particolare attenzione alla continuità aziendale, seguire queste linee guida generali:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Utilizzo di uno o più gruppi di failover per gestire il failover di più database

È possibile creare uno o più gruppi di failover tra due server in aree diverse (server primario e secondario). Ogni gruppo può includere uno o più database che vengono ripristinati come unità nel caso in cui tutti o alcuni database primari diventino non disponibili a causa di un'interruzione nell'area primaria. Il gruppo di failover crea un database di replica geografica secondaria con lo stesso obiettivo di servizio di quello primario. Se si aggiunge una relazione di replica geografica esistente al gruppo di failover, verificare che il database di replica geografica secondario sia configurato con lo stesso livello di servizio e le stesse dimensioni di calcolo del database primario.
  
> [!IMPORTANT]
> La creazione di gruppi di failover tra due server in sottoscrizioni diverse non è attualmente supportata per singoli database e pool elastici. Se si sposta il server primario o secondario in una sottoscrizione diversa dopo la creazione del gruppo di failover, potrebbero verificarsi errori delle richieste di failover e di altre operazioni.

### <a name="using-read-write-listener-for-oltp-workload"></a>Utilizzo del listener di lettura/scrittura per il carico di lavoro OLTPUsing read-write listener for OLTP workload

Quando si eseguono operazioni OLTP, usare `<fog-name>.database.windows.net` come URL del server per indirizzare automaticamente le connessioni al database primario. Questo URL non cambia dopo il failover. Si noti che il failover comporta l'aggiornamento del record DNS in modo che le connessioni client vengano reindirizzate al nuovo database primario solo dopo l'aggiornamento della cache DNS del client.

### <a name="using-read-only-listener-for-read-only-workload"></a>Utilizzo di listener di sola lettura per il carico di lavoro di sola letturaUsing read-only listener for read-only workload

Se è presente un carico di lavoro di sola lettura isolato logicamente che tollera un certo grado di obsolescenza dei dati, è possibile usare il database secondario nell'applicazione. Per sessioni di sola lettura usare `<fog-name>.secondary.database.windows.net` come URL del server per indirizzare automaticamente le connessioni al server secondario. Si consiglia inoltre di indicare nella stringa `ApplicationIntent=ReadOnly`di connessione l'intenzione di lettura utilizzando . Se si desidera assicurarsi che il carico di lavoro di sola lettura possa riconnettersi `AllowReadOnlyFailoverToPrimary` dopo il failover o, nel caso in cui il server secondario non sia in linea, assicurarsi di configurare la proprietà dei criteri di failover.

### <a name="preparing-for-performance-degradation"></a>Preparazione per la riduzione delle prestazioni

Una tipica applicazione di Azure usa più servizi di Azure ed è costituita da più componenti. Il failover automatico del gruppo di failover viene attivato in base allo stato in cui i soli componenti SQL di Azure. Altri servizi di Azure nell'area primaria potrebbero non essere interessati dall'interruzione e i relativi componenti potrebbero essere ancora disponibili in tale area. Una volta che i database primari passano all'area di ripristino di emergenza, la latenza tra i componenti dipendenti può aumentare. Per evitare l'impatto di una latenza più elevata sulle prestazioni dell'applicazione, assicurarsi la ridondanza di tutti i componenti dell'applicazione nell'area di ripristino di emergenza e seguire queste linee guida sulla sicurezza di [rete.](#failover-groups-and-network-security)

### <a name="preparing-for-data-loss"></a>Preparazione per la perdita di dati

Se viene rilevata un'interruzione, SQL attende `GracePeriodWithDataLossHours`il periodo specificato da . Il valore predefinito è 1 ora. Se non è possibile permettersi la `GracePeriodWithDataLossHours` perdita di dati, assicurarsi di impostare su un numero sufficientemente grande, ad esempio 24 ore. Usare il failover manuale dei gruppi per eseguire il failback dal server secondario a quello primario.

> [!IMPORTANT]
> I pool elastici con 800 o meno DTU e più di 250 database con replica geografica possono riscontrare problemi quali failover pianificati più lunghi e un peggioramento delle prestazioni.  Questi problemi si verificano con maggiore probabilità nella scrittura di carichi di lavoro con utilizzo intensivo, quando gli endpoint con replica geografica sono ampiamente separati per area geografica o quando vengono utilizzati più endpoint secondari per ogni database.  I sintomi di questi problemi emergono quando il ritardo della replica geografica aumenta nel tempo.  Questo ritardo può essere monitorato mediante [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Se si verificano questi problemi, per prevenirli si può aumentare il numero di DTU nel pool o ridurre il numero di database replicati geograficamente nello stesso pool.

### <a name="changing-secondary-region-of-the-failover-group"></a>Modifica dell'area secondaria del gruppo di failover

Per illustrare la sequenza di modifiche, si presuppone che il server A sia il server primario, che il server B sia il server secondario esistente e che il server C sia il nuovo secondario nella terza area.  Per effettuare la transizione, attenersi alla seguente procedura:

1.  Creare ulteriori secondari di ogni database sul server A al server C utilizzando la [replica geografica attiva.](sql-database-active-geo-replication.md) Ogni database sul server A avrà due secondari, uno sul server B e uno sul server C. Ciò garantirà che i database primari rimangano protetti durante la transizione.
2.  Eliminare il gruppo di failover. A questo punto gli account di accesso avranno esito negativo. Ciò è dovuto al fatto che gli alias SQL per i listener del gruppo di failover sono stati eliminati e il gateway non riconoscerà il nome del gruppo di failover.
3.  Ricreare il gruppo di failover con lo stesso nome tra i server A e C. A questo punto gli account di accesso smetteranno di non riuscire.
4.  Aggiungere tutti i database primari nel server A al nuovo gruppo di failover.
5.  Eliminare il server B. Tutti i database su B verranno eliminati automaticamente. 


### <a name="changing-primary-region-of-the-failover-group"></a>Modifica dell'area primaria del gruppo di failover

Per illustrare la sequenza di modifiche, si presuppone che il server A sia il server primario, che il server B sia il server secondario esistente e che il server C sia il nuovo primario nella terza area.  Per effettuare la transizione, attenersi alla seguente procedura:

1.  Eseguire un failover pianificato per passare il server primario al server B. Server A diventerà il nuovo server secondario. Il failover può comportare diversi minuti di inattività. Il tempo effettivo dipenderà dalle dimensioni del gruppo di failover.
2.  Creare secondari aggiuntivi di ogni database sul server B al server C utilizzando la [replica geografica attiva.](sql-database-active-geo-replication.md) Ogni database sul server B avrà due secondari, uno sul server A e uno sul server C. Ciò garantirà che i database primari rimangano protetti durante la transizione.
3.  Eliminare il gruppo di failover. A questo punto gli account di accesso avranno esito negativo. Ciò è dovuto al fatto che gli alias SQL per i listener del gruppo di failover sono stati eliminati e il gateway non riconoscerà il nome del gruppo di failover.
4.  Ricreare il gruppo di failover con lo stesso nome tra i server A e C. A questo punto gli account di accesso smetteranno di non riuscire.
5.  Aggiungere tutti i database primari in B al nuovo gruppo di failover. 
6.  Eseguire un failover pianificato del gruppo di failover per passare a B e C. Ora il server C diventerà il primario e B - il secondario. Tutti i database secondari sul server A verranno collegati automaticamente alle primarie su C. Come nel passaggio 1, il failover può comportare diversi minuti di inattività.
6.  Eliminare il server A. Tutti i database su A verranno eliminati automaticamente.

> [!IMPORTANT]
> Quando il gruppo di failover viene eliminato, vengono eliminati anche i record DNS per gli endpoint del listener. A questo punto, è presente una probabilità diversa da zero che qualcun altro crei un gruppo di failover o un alias del server con lo stesso nome, che impedirà di utilizzarlo nuovamente. Per ridurre al minimo il rischio, non utilizzare nomi di gruppi di failover generici.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Procedure consigliate per l'utilizzo di gruppi di failover con istanze gestiteBest practices of using failover groups with managed instances

Il gruppo di failover automatico deve essere configurato nell'istanza primaria e la connetterà all'istanza secondaria in un'altra area di Azure.  Tutti i database nell'istanza verranno replicati nell'istanza secondaria.

Il diagramma seguente illustra una configurazione tipica di un'applicazione cloud con ridondanza geografica con un'istanza gestita un gruppo di failover automatico.

![failover automatico](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Per un'esercitazione dettagliata sull'aggiunta di un'istanza gestita per l'uso del gruppo di failover, vedere [Aggiungere un'istanza gestita a un gruppo](sql-database-managed-instance-failover-group-tutorial.md) di failover.

Se l'applicazione usa l'istanza gestita come livello dati, seguire queste linee guida generali durante la progettazione per la continuità aziendale:If your application uses managed instance as the data tier, follow these general guidelines when designing for business continuity:

### <a name="creating-the-secondary-instance"></a>Creazione dell'istanza secondaria 

Per garantire connettività ininterrotta all'istanza primaria dopo il failover, l'istanza primaria e l'istanza secondaria devono essere nella stessa zona DNS. Garantisce che lo stesso certificato a più domini (SAN) possa essere utilizzato per autenticare le connessioni client a una delle due istanze nel gruppo di failover. Quando l'applicazione è pronta per la distribuzione in produzione, creare un'istanza secondaria in un'area diversa e assicurarsi che condivida la zona DNS con l'istanza primaria. È possibile farlo specificando `DNS Zone Partner` un parametro facoltativo usando il portale di Azure, PowerShell o l'API REST.

> [!IMPORTANT]
> La prima istanza creata nella subnet determina la zona DNS per tutte le istanze successive nella stessa subnet. Ciò significa che due istanze della stessa subnet non possono appartenere a zone DNS diverse.

Per ulteriori informazioni sulla creazione dell'istanza secondaria nella stessa zona DNS dell'istanza primaria, vedere [Creare un'istanza gestita secondaria.](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance)

### <a name="enabling-replication-traffic-between-two-instances"></a>Abilitazione del traffico di replica tra due istanze

Poiché ogni istanza è isolata nella propria rete virtuale, è necessario consentire il traffico bidirezionale tra queste reti virtuali. Vedere [Gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Creazione di un gruppo di failover tra istanze gestite in sottoscrizioni diverseCreating a failover group between managed instances in different subscriptions

È possibile creare un gruppo di failover tra istanze gestite in due sottoscrizioni diverse. Quando si usa l'API di PowerShell, è possibile farlo specificando il `PartnerSubscriptionId` parametro per l'istanza secondaria. Quando si usa l'API REST, ogni ID istanza incluso nel parametro può avere il `properties.managedInstancePairs` proprio subscriptionID.
  
> [!IMPORTANT]
> Il portale di Azure non supporta la creazione di gruppi di failover tra sottoscrizioni diverse. Inoltre, per i gruppi di failover esistenti tra sottoscrizioni e/o gruppi di risorse diversi, il failover non può essere avviato manualmente tramite il portale dall'istanza primaria. Avviarlo dall'istanza geo-secondaria.

### <a name="managing-failover-to-secondary-instance"></a>Gestione del failover nell'istanza secondariaManaging failover to secondary instance

Il gruppo di failover gestisce il failover di tutti i database nell'istanza. Quando viene creato un gruppo, ogni database nell'istanza sarà automaticamente sottoposto a replica geografica nell'istanza secondaria. Non è possibile usare gruppi di failover per avviare un failover parziale di un subset dei database.

> [!IMPORTANT]
> Se un database viene rimosso dall'istanza primaria, l'operazione verrà automaticamente riflessa anche nell'istanza secondaria.

### <a name="using-read-write-listener-for-oltp-workload"></a>Utilizzo del listener di lettura/scrittura per il carico di lavoro OLTPUsing read-write listener for OLTP workload

Quando si eseguono operazioni OLTP, usare `<fog-name>.zone_id.database.windows.net` come URL del server per indirizzare automaticamente le connessioni al database primario. Questo URL non cambia dopo il failover. Il failover comporta l'aggiornamento del record DNS in modo che le connessioni client vengano reindirizzate al nuovo database primario solo dopo l'aggiornamento della cache DNS del client. Poiché l'istanza secondaria condivide la zona DNS con il database primario, l'applicazione client sarà in grado di riconnettersi utilizzando lo stesso certificato SAN.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Utilizzo del listener di sola lettura per connettersi all'istanza secondariaUsing read-only listener to connect to the secondary instance

Se è presente un carico di lavoro di sola lettura isolato logicamente che tollera un certo grado di obsolescenza dei dati, è possibile usare il database secondario nell'applicazione. Per connettersi direttamente al database secondario con replica geografica, usare `server.secondary.zone_id.database.windows.net` come URL del server.

> [!NOTE]
> In determinati livelli di servizio il database SQL di Azure supporta l'uso di [repliche di sola lettura](sql-database-read-scale-out.md) per bilanciare i carichi di lavoro di query di sola lettura usando la capacità di una replica di sola lettura e il parametro `ApplicationIntent=ReadOnly` nella stringa di connessione. Dopo aver configurato un database secondario con replica geografica, sarà possibile usare questa funzionalità per connettersi a una replica di sola lettura nella posizione primaria o nella posizione con replica geografica.
> - Per connettersi a una replica di sola lettura nella posizione con replica geografica, usare `<fog-name>.zone_id.database.windows.net`.
> - Per connettersi a una replica di sola `<fog-name>.secondary.zone_id.database.windows.net`lettura nella posizione secondaria, utilizzare .

### <a name="preparing-for-performance-degradation"></a>Preparazione per la riduzione delle prestazioni

Una tipica applicazione di Azure usa più servizi di Azure ed è costituita da più componenti. Il failover automatico del gruppo di failover viene attivato in base allo stato in cui i soli componenti SQL di Azure. Altri servizi di Azure nell'area primaria potrebbero non essere interessati dall'interruzione e i relativi componenti potrebbero essere ancora disponibili in tale area. Una volta che i database primari passano all'area di ripristino di emergenza, la latenza tra i componenti dipendenti può aumentare. Per evitare l'impatto di una latenza più elevata sulle prestazioni dell'applicazione, assicurarsi la ridondanza di tutti i componenti dell'applicazione nell'area di ripristino di emergenza e seguire queste linee guida sulla sicurezza di [rete.](#failover-groups-and-network-security)

### <a name="preparing-for-data-loss"></a>Preparazione per la perdita di dati

Se viene rilevata un'interruzione del servizio, SQL attiva automaticamente il failover di lettura/scrittura nel caso in cui la perdita di dati è nulla al meglio delle nostre conoscenze. In caso contrario, attende il periodo specificato da `GracePeriodWithDataLossHours`. Se è stato specificato `GracePeriodWithDataLossHours`, potrebbe verificarsi una perdita di dati. Durante le interruzioni del servizio, generalmente Azure predilige la disponibilità. Se non ci si può permettere una perdita di dati, assicurarsi di impostare GracePeriodWithDataLossHours su un numero sufficientemente elevato, ad esempio 24 ore.

Subito dopo l'avvio del failover si verificherà l'aggiornamento DNS del listener di lettura/scrittura. Questa operazione non comporta la perdita di dati. Tuttavia, il processo di scambio dei ruoli del database può richiedere fino a 5 minuti in condizioni normali. Durante il processo alcuni database nella nuova istanza primaria rimarranno di sola lettura. Se il failover viene avviato tramite PowerShell, l'intera operazione è sincrona. Se viene avviato tramite il portale di Azure, l'interfaccia utente indicherà lo stato di completamento. Se viene avviato mediante l'API REST, usare il meccanismo di polling standard di Azure Resource Manager per il monitoraggio del completamento.

> [!IMPORTANT]
> Usare il failover manuale dei gruppi per ripristinare i database primari nella posizione originale. Quando viene risolta l'interruzione del servizio che ha determinato il failover, sarà possibile spostare i database primari nel percorso originale. A tale scopo è necessario avviare il failover manuale del gruppo.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Modifica dell'area secondaria del gruppo di failover

Supponiamo che l'istanza A sia l'istanza primaria, che l'istanza B sia l'istanza secondaria esistente e che l'istanza C sia la nuova istanza secondaria nella terza area.  Per effettuare la transizione, attenersi alla seguente procedura:

1.  Creare l'istanza C con le stesse dimensioni di A e nella stessa zona DNS. 
2.  Eliminare il gruppo di failover tra le istanze A e B. A questo punto gli account di accesso avranno esito negativo perché gli alias SQL per i listener del gruppo di failover sono stati eliminati e il gateway non riconoscerà il nome del gruppo di failover. I database secondari verranno disconnessi dai database primari e verranno database di lettura/scrittura. 
3.  Creare un gruppo di failover con lo stesso nome tra le istanze A e C. Seguire le istruzioni nel gruppo di [failover con l'istanza gestita](sql-database-managed-instance-failover-group-tutorial.md)esercitazione . Si tratta di un'operazione di dimensione dei dati e verrà completata quando vengono esedati e sincronizzati tutti i database dall'istanza A.
4.  Eliminare l'istanza B se non è necessario per evitare addebiti non necessari.

> [!NOTE]
> Dopo il passaggio 2 e fino al completamento del passaggio 3, i database nell'istanza A rimarranno non protetti da un errore irreversibile dell'istanza A.

### <a name="changing-primary-region-of-the-failover-group"></a>Modifica dell'area primaria del gruppo di failover

Supponiamo che l'istanza A sia l'istanza primaria, che l'istanza B sia l'istanza secondaria esistente e che l'istanza C sia la nuova istanza primaria nella terza area.  Per effettuare la transizione, attenersi alla seguente procedura:

1.  Creare l'istanza C con le stesse dimensioni di B e nella stessa zona DNS. 
2.  Connettersi all'istanza B ed eseguire manualmente il failover per passare l'istanza primaria a B. L'istanza A diventerà automaticamente la nuova istanza secondaria.
3.  Eliminare il gruppo di failover tra le istanze A e B. A questo punto gli account di accesso avranno esito negativo perché gli alias SQL per i listener del gruppo di failover sono stati eliminati e il gateway non riconoscerà il nome del gruppo di failover. I database secondari verranno disconnessi dai database primari e verranno database di lettura/scrittura. 
4.  Creare un gruppo di failover con lo stesso nome tra le istanze A e C. Seguire le istruzioni nel gruppo di [failover con l'istanza gestita esercitazione](sql-database-managed-instance-failover-group-tutorial.md). Si tratta di un'operazione di dimensione dei dati e verrà completata quando vengono esedati e sincronizzati tutti i database dall'istanza A.
5.  Eliminare l'istanza A se non è necessario per evitare addebiti non necessari.

> [!NOTE] 
> Dopo il passaggio 3 e fino al completamento del passaggio 4, i database nell'istanza A rimarranno non protetti da un errore irreversibile dell'istanza A.

> [!IMPORTANT]
> Quando il gruppo di failover viene eliminato, vengono eliminati anche i record DNS per gli endpoint del listener. A questo punto, è presente una probabilità diversa da zero che qualcun altro crei un gruppo di failover o un alias del server con lo stesso nome, che impedirà di utilizzarlo nuovamente. Per ridurre al minimo il rischio, non utilizzare nomi di gruppi di failover generici.

## <a name="failover-groups-and-network-security"></a>Gruppi di failover e sicurezza di rete

Per alcune applicazioni le regole di sicurezza richiedono che l'accesso di rete al livello dati sia limitato a uno o più componenti specifici, ad esempio una macchina virtuale, un servizio Web e così via. Questo requisito presenta alcune sfide per la progettazione della continuità aziendale e l'utilizzo dei gruppi di failover. Quando si implementa tale accesso limitato, tenere presenti le opzioni seguenti.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Uso dei gruppi di failover e delle regole di rete virtuale

Se si usano [endpoint servizio e regole di rete virtuale](sql-database-vnet-service-endpoint-rule-overview.md) per limitare l'accesso al database SQL, tenere presente che ogni endpoint servizio di rete virtuale si applica a una sola area geografica di Azure. L'endpoint non consente ad altre aree di accettare le comunicazioni dalla subnet. Pertanto, solo le applicazioni client distribuite nella stessa area possono connettersi al database primario. Poiché il failover ha per effetto il reindirizzamento delle sessioni client SQL a un server presente in un'altra area geografica (secondaria), tali sessioni avranno esito negativo se hanno avuto origine da un client al di fuori di tale area. Per questo motivo non è possibile abilitare i criteri di failover automatico se i server partecipanti sono inclusi nelle regole di rete virtuale. Per supportare il failover manuale, seguire questa procedura:

1. Effettuare il provisioning delle copie ridondanti dei componenti front-end dell'applicazione (servizio Web, macchine virtuali e così via) nell'area secondaria
2. Configurare le [regole di rete virtuale](sql-database-vnet-service-endpoint-rule-overview.md) singolarmente per il server primario e per quello secondario
3. Abilitare il [failover front-end usando una configurazione di Gestione traffico](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Avviare il failover manuale quando viene rilevata l'interruzione. Questa opzione è ottimizzata per le applicazioni che richiedono la latenza coerente tra il front-end e il livello dati e supporta il ripristino quando il front-end, il livello dati o entrambi sono interessati dall'interruzione del servizio.

> [!NOTE]
> Se si usa il **listener di sola lettura** per il bilanciamento di un carico di lavoro di sola lettura, assicurarsi che il carico di lavoro venga eseguito in una macchina virtuale o in un'altra risorsa nell'area secondaria in modo da consentire la connessione al database secondario.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Uso dei gruppi di failover e delle regole del firewall del database SQL

Se il piano di continuità aziendale richiede il failover tramite gruppi con failover automatico, è possibile limitare l'accesso al database SQL usando le regole del firewall tradizionali. Per supportare il failover automatico, seguire questa procedura:

1. [Creare un indirizzo IP pubblico](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address).
2. [Creare un servizio di bilanciamento del carico pubblico](../load-balancer/quickstart-load-balancer-standard-public-portal.md) e assegnare l'indirizzo IP pubblico a tale servizio.
3. [Creare una rete virtuale e le macchine virtuali](../load-balancer/quickstart-load-balancer-standard-public-portal.md) per i componenti front-end.
4. [Creare un gruppo di sicurezza di rete](../virtual-network/security-overview.md) e configurare le connessioni in ingresso.
5. Assicurarsi che le connessioni in uscita siano aperte al database SQL di Azure usando il [tag di servizio](../virtual-network/security-overview.md#service-tags) 'Sql'.
6. Creare un [regola del firewall del database SQL](sql-database-firewall-configure.md) per consentire il traffico in ingresso dall'indirizzo IP pubblico creato nel passaggio 1.

Per altre informazioni su come configurare l'accesso in uscita e su quale indirizzo IP usare nelle regole del firewall, vedere [Connessioni in uscita del servizio di bilanciamento del carico](../load-balancer/load-balancer-outbound-connections.md).

La configurazione appena illustrata garantisce che il failover automatico non blocchi le connessioni tra i componenti front-end e presuppone che l'applicazione riesca a tollerare una maggiore latenza tra il front-end e il livello dati.

> [!IMPORTANT]
> Per garantire la continuità aziendale nel caso di interruzioni del servizio a livello di area è necessario verificare la ridondanza geografica sia per i componenti front-end che per i database.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Abilitazione della replica geografica tra istanze gestite e le relative reti virtualiEnabling geo-replication between managed instances and their VNets

Quando si configura un gruppo di failover tra istanze gestite primarie e secondarie in due aree diverse, ogni istanza viene isolata tramite una rete virtuale indipendente. Per consentire il traffico di replica tra queste reti virtuali, verificare che siano soddisfatti i prerequisiti seguenti:To allow replication traffic between these VNets ensure these prerequisites are met:

1. Le due istanze gestite devono trovarsi in aree di Azure diverse.
2. Le due istanze gestite devono essere dello stesso livello di servizio e avere le stesse dimensioni di archiviazione.
3. L'istanza gestita secondaria deve essere vuota (nessun database utente).
4. Le reti virtuali utilizzate dalle istanze gestite devono essere connesse tramite un [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [Express Route](../expressroute/expressroute-howto-circuit-portal-resource-manager.md). Quando due reti virtuali si connettono tramite una rete locale, verificare che non vi siano regole del firewall che bloccano le porte 5022 e 11000-11999. Il peering di reti virtuali globale non è supportato.
5. Le due reti virtuali dell'istanza gestita non possono avere indirizzi IP sovrapposti.
6. È necessario configurare i gruppi di sicurezza di rete (NSG) in modo che le porte 5022 e l'intervallo 11000 -12000 siano aperti in ingresso e in uscita per le connessioni dalla subnet dell'altra istanza gestita. In questo modo si consente il traffico di replica tra le istanze.

   > [!IMPORTANT]
   > Regole di sicurezza dei gruppi di sicurezza di rete non configurate correttamente determinano il blocco delle operazioni di copia del database.

7. L'istanza secondaria è configurata con l'ID zona DNS corretto. La zona DNS è una proprietà di un'istanza gestita e di un cluster virtuale e il relativo ID è incluso nell'indirizzo del nome host. L'ID di zona viene generato come stringa casuale quando viene creata la prima istanza gestita in ogni rete virtuale e lo stesso ID viene assegnato a tutte le altre istanze nella stessa subnet. Una volta assegnata, la zona DNS non può essere modificata. Le istanze gestite incluse nello stesso gruppo di failover devono condividere la zona DNS. A tale scopo, passare l'ID di zona dell'istanza primaria come valore del parametro di Dns'onePartner durante la creazione dell'istanza secondaria. 

   > [!NOTE]
   > Per un'esercitazione dettagliata sulla configurazione dei gruppi di failover con l'istanza gestita, vedere [Aggiungere un'istanza gestita a un gruppo](sql-database-managed-instance-failover-group-tutorial.md)di failover.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Aggiornamento o downgrade di un database primario

È possibile eseguire l'aggiornamento o il downgrade di un database primario a dimensioni di calcolo diverse (entro lo stesso livello di servizio e non tra il livello per utilizzo generico e business critical) senza disconnettere eventuali database secondari. Quando si esegue l'aggiornamento, è consigliabile aggiornare prima tutti i database secondari e quindi aggiornare il database primario. Quando si esegue il downgrade, invertire l'ordine: eseguire prima il downgrade del database primario e quindi eseguire il downgrade di tutti i database secondari. Quando si aggiorna o si effettua il downgrade del database a un livello di servizio diverso, viene applicata questa raccomandazione.

Questa sequenza è consigliata in modo specifico per evitare il problema in cui il database secondario in uno SKU inferiore viene sottoposto a overload e deve essere nuovamente sottoposto a seeded durante un processo di aggiornamento o downgrade. È anche possibile evitare il problema rendendo il database di sola lettura primario, a scapito dell'impatto su tutti i carichi di lavoro di lettura/scrittura rispetto al database primario.

> [!NOTE]
> Se è stato creato il database secondario come parte della configurazione del gruppo di failover, non è consigliabile eseguire il downgrade del database secondario. In questo modo si garantisce che il livello dei dati abbia una capacità sufficiente per elaborare il carico di lavoro normale dopo che il failover viene attivato.

## <a name="preventing-the-loss-of-critical-data"></a>Evitare la perdita di dati critici

A causa della latenza elevata delle reti WAN, per la copia continua viene usato un meccanismo di replica asincrona. La replica asincrona rende inevitabile una perdita parziale dei dati nel caso si verifichi un problema. Tuttavia, alcune applicazione potrebbero non essere soggette alla perdita dei dati. Per proteggere questi aggiornamenti critici, uno sviluppatore di applicazioni può chiamare la procedura di sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) subito dopo il commit della transazione. La `sp_wait_for_database_copy_sync` chiamata blocca il thread chiamante fino a quando l'ultima transazione di cui è stato eseguito il commit non è stata trasmessa al database secondario. Tuttavia, non attende che le transazioni trasmesse vengano riprodotta e che ne venga eseguito il commit nel database secondario. `sp_wait_for_database_copy_sync`ha come ambito un collegamento specifico per la copia continua. La procedura può essere chiamata da qualsiasi utente che abbia diritti di connessione al database primario.

> [!NOTE]
> `sp_wait_for_database_copy_sync`impedisce la perdita di dati dopo il failover, ma non garantisce la sincronizzazione completa per l'accesso in lettura. Il ritardo `sp_wait_for_database_copy_sync` causato da una chiamata di routine può essere significativo e dipende dalle dimensioni del log delle transazioni al momento della chiamata.

## <a name="failover-groups-and-point-in-time-restore"></a>Gruppi di failover e ripristino temporizzato

Per informazioni sull'uso del ripristino temporizzato con gruppi di failover, vedere [Point in Time Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore) (Recupero temporizzato).

## <a name="limitations-of-failover-groups"></a>Limitazioni dei gruppi di failoverLimitations of failover groups

Tenere presente le limitazioni seguenti:

- Impossibile creare un gruppo di failover tra due server o istanze nelle stesse aree di Azure.Failover group cannot be created between two servers or instances in the same Azure regions.
- Impossibile rinominare il gruppo di failover. È necessario eliminare il gruppo e ricrearlo con un nome diverso. 
- La ridenominazione del database non è supportata per le istanze nel gruppo di failover. Sarà necessario eliminare temporaneamente il gruppo di failover per poter rinominare un database.

## <a name="programmatically-managing-failover-groups"></a>Gestione di gruppi di failover a livello di codice

Come indicato in precedenza, i gruppi di failover automatico e la replica geografica attiva possono essere gestiti a livello di codice usando Azure PowerShell e l'API REST. Le tabelle seguenti descrivono il set di comandi disponibili. La replica geografica attiva include un set di API di Azure Resource Manager per la gestione, compresa l'[API REST del Database SQL di Azure](https://docs.microsoft.com/rest/api/sql/) e i [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Queste API richiedono l'uso di gruppi di risorse e supportano la sicurezza basata sui ruoli (Controllo degli accessi in base al ruolo). Per altre informazioni su come implementare i ruoli di accesso, vedere Controllo degli accessi in base al ruolo di Azure.For more information on how to implement access roles, see [Azure Role-Based Access Control](../role-based-access-control/overview.md).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Gestire il failover del database SQL con database singoli e pool elastici

| Cmdlet | Descrizione |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Questo comando crea un gruppo di failover e lo registra nei server primario e secondario|
| [Rimuovi-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Rimuove un gruppo di failover dal server |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Recupera la configurazione di un gruppo di failover |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modifica la configurazione di un gruppo di failover |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Attiva il failover di un gruppo di failover nel server secondarioTriggers failover of a failover group to the secondary server |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Aggiunge uno o più database a un gruppo di failover|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Gestire i gruppi di failover del database SQL con le istanze gestiteManage SQL database failover groups with managed instances

| Cmdlet | Descrizione |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Questo comando crea un gruppo di failover e lo registra nelle istanze primarie e secondarie|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modifica la configurazione di un gruppo di failover|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Recupera la configurazione di un gruppo di failover|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Attiva il failover di un gruppo di failover nell'istanza secondariaTriggers failover of a failover group to the secondary instance|
| [Rimuovi-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Rimuove un gruppo di failover|

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Gestire il failover del database SQL con database singoli e pool elastici

| Comando | Descrizione |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Questo comando crea un gruppo di failover e lo registra nei server primario e secondario|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Rimuove un gruppo di failover dal server |
| [az sql failover-group mostra](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Recupera la configurazione di un gruppo di failover |
| [az sql failover-group update (aggiornamento del gruppo di failover di az sql)](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Modifica la configurazione di un gruppo di failover e/o aggiunge uno o più database a un gruppo di failover|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Attiva il failover di un gruppo di failover nel server secondarioTriggers failover of a failover group to the secondary server |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Gestire i gruppi di failover del database SQL con le istanze gestiteManage SQL database failover groups with managed instances

| Comando | Descrizione |
| --- | --- |
| [az sql instance-failover-group creare](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | Questo comando crea un gruppo di failover e lo registra nelle istanze primarie e secondarie |
| [az sql sql-failover-group update (aggiornamento del gruppo di failover di istanza sql)](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | Modifica la configurazione di un gruppo di failover|
| [az sql instance-failover-group mostra](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Recupera la configurazione di un gruppo di failover|
| [az sql instance-failover-group set-primary](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Attiva il failover di un gruppo di failover nell'istanza secondariaTriggers failover of a failover group to the secondary instance|
| [az sql instance-failover-group delete](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Rimuove un gruppo di failover |

* * *

> [!IMPORTANT]
> Per uno script di esempio, vedere [Configure and failover a failover group for a single database](scripts/sql-database-add-single-db-to-failover-group-powershell.md) (Configurare ed eseguire il failover di un gruppo di failover per un singolo database).

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>API REST: Gestire i gruppi di failover del database SQL con database singoli e in pool

| API | Descrizione |
| --- | --- |
| [Create or Update Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Crea o aggiorna un gruppo di failover. |
| [Delete Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Rimuove un gruppo di failover dal server |
| [Failover (Planned)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Attiva il failover dal server primario corrente al server secondario con sincronizzazione completa dei dati.|
| [Force Failover Allow Data Loss](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Attiva il failover dal server primario corrente al server secondario senza sincronizzare i dati. Questa operazione può causare la perdita di dati. |
| [Get Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Recupera la configurazione di un gruppo di failover. |
| [List Failover Groups By Server](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Elenca i gruppi di failover in un server. |
| [Update Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Aggiorna la configurazione di un gruppo di failover. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>API REST: Gestire i gruppi di failover con le istanze gestite

| API | Descrizione |
| --- | --- |
| [Create or Update Failover Group](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Crea o aggiorna la configurazione di un gruppo di failover |
| [Delete Failover Group](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Rimuove un gruppo di failover dall'istanza |
| [Failover (Planned)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Attiva il failover dall'istanza primaria corrente a questa istanza con sincronizzazione completa dei dati. |
| [Force Failover Allow Data Loss](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Attiva il failover dall'istanza primaria corrente all'istanza secondaria senza sincronizzare i dati. Questa operazione può causare la perdita di dati. |
| [Get Failover Group](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | recupera la configurazione di un gruppo di failover. |
| [List Failover Groups - List By Location](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Elenca i gruppi di failover in una posizione. |

## <a name="next-steps"></a>Passaggi successivi

- Per esercitazioni dettagliate, vedere
    - [Aggiungere un singolo database a un gruppo di failoverAdd single database to a failover group](sql-database-single-database-failover-group-tutorial.md)
    - [Aggiungere il pool elastico a un gruppo di failover](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Aggiungere un'istanza gestita a un gruppo di failoverAdd a managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md)
- Per script di esempio, vedere:
  - [Usare PowerShell per configurare la replica geografica attiva per un database singolo di database SQL di Azure](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Usare PowerShell per configurare la replica geografica attiva per un database in pool nel database SQL di Azure](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Usare PowerShell per aggiungere un database singolo di database SQL di Azure a un gruppo di failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
- Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).
- Per informazioni sull'utilizzo dei backup automatici per il ripristino, vedere [Ripristinare un database dai backup avviati dal servizio.](sql-database-recovery-using-backups.md)
- Per ulteriori informazioni sui requisiti di autenticazione per un nuovo database e server primario, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).
