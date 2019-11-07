---
title: Gruppi di failover-database SQL di Azure
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
ms.date: 10/23/2019
ms.openlocfilehash: 70c8bb618cd25c21d6cc59dde305fff113ffe22f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691170"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Usare i gruppi di failover automatico per consentire il failover trasparente e coordinato di più database

I gruppi di failover automatico sono una funzionalità del database SQL che consente di gestire la replica e il failover di un gruppo di database in un server di database SQL o di tutti i database in un'istanza gestita in un'altra area. Si tratta di un'astrazione dichiarativa basata sulla funzionalità di [replica geografica attiva](sql-database-active-geo-replication.md) esistente, progettata per semplificare la distribuzione e la gestione di database con replica geografica su larga scala. È possibile avviare manualmente il failover oppure delegarlo al servizio del database SQL in base a criteri definiti dall'utente. Grazie a questa seconda opzione è possibile ripristinare automaticamente più database correlati in un'area secondaria dopo un errore irreversibile o un altro evento imprevisto che comporta la perdita parziale o completa di disponibilità del servizio del database SQL nell'area primaria. Un gruppo di failover può includere uno o più database, in genere utilizzati dalla stessa applicazione. È inoltre possibile usare i database secondari leggibili per l'offload dei carichi di lavoro delle query di sola lettura. Poiché i gruppi di failover automatico coinvolgono più database, questi ultimi devono essere configurati nel server primario. I gruppi di failover automatico supportano la replica di tutti i database nel gruppo in un solo server secondario in un'area diversa.

> [!NOTE]
> Se si usano database singoli o in pool in un server di database SQL e si intende avere più database secondari nella stessa area geografica o in aree diverse, usare la [replica geografica attiva](sql-database-active-geo-replication.md). 

Se si usano gruppi di failover automatico con criteri di failover automatico, eventuali interruzioni che influiscono su uno o più database nel gruppo determinano un failover automatico. In genere si tratta di eventi imprevisti che non possono essere automaticamente mitigati dalle operazioni di disponibilità elevata automatiche predefinite. Gli esempi di trigger di failover includono un evento imprevisto causato da un anello del tenant SQL o da un anello di controllo inattivo a causa di una perdita di memoria del kernel del sistema operativo in diversi nodi di calcolo o di un evento imprevisto causato da uno o più anelli del tenant inattivi a causa di un cavo di rete errato durante il ro rimozione delle autorizzazioni per l'hardware utine.  Per altre informazioni, vedere [disponibilità elevata del database SQL](sql-database-high-availability.md).

I gruppi di failover automatico forniscono anche endpoint di listener di sola lettura e di sola scrittura che rimangono invariati durante i failover. Se si usa l'attivazione di failover manuale o automatica, il failover trasforma tutti i database secondari nel gruppo in database primari. Dopo aver completato il failover del database, il record DNS viene automaticamente aggiornato per reindirizzare gli endpoint alla nuova area. Per i dati RPO e RTO specifici consultare la [panoramica della continuità aziendale](sql-database-business-continuity.md).

Se si usano gruppi di failover automatico con criteri di failover automatico, eventuali interruzioni che influiscono sui database nel server di database SQL o nell'istanza gestita determinano un failover automatico. È possibile gestire il gruppo di failover automatico mediante:

- Il[portale di Azure](sql-database-implement-geo-distributed-database.md)
- [PowerShell: gruppo di failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [API REST: gruppo di failover](https://docs.microsoft.com/rest/api/sql/failovergroups).

Dopo il failover, verificare che nel nuovo database primario siano configurati i requisiti di autenticazione relativi al server e al database. Per tutti i dettagli, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

Per ottenere una reale continuità aziendale, l'aggiunta di ridondanza dei database tra i data center rappresenta solo una parte della soluzione. Per ripristinare un'applicazione (servizio) end-to-end dopo un problema grave, è necessario effettuare il ripristino di tutti i componenti del servizio e gli eventuali servizi dipendenti. Esempi di questi componenti includono il software client (ad esempio, un browser con JavaScript personalizzato), front-end Web, spazio di archiviazione e DNS. È fondamentale che tutti i componenti siano resilienti agli stessi problemi e diventino disponibili entro I'obiettivo del tempo di ripristino (RTO) dell'applicazione. È perciò necessario identificare tutti i servizi dipendenti e comprendere quali garanzie e funzionalità vengono fornite. È quindi necessario intraprendere le azioni appropriate per assicurare il funzionamento del servizio durante il failover dei servizi da cui dipende. Per altre informazioni sulla progettazione di soluzioni per il ripristino di emergenza, vedere [Progettazione di applicazioni per il ripristino di emergenza cloud con la replica geografica attiva in database SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Funzionalità e terminologia dei gruppi di failover automatico

- **Gruppo di failover (nebbia)**

  Un gruppo di failover è un gruppo denominato di database gestito da un singolo server di database SQL o all'interno di una singola istanza gestita di cui è possibile eseguire il failover come unità in un'altra area nel caso in cui tutti o alcuni database primari non siano più disponibili a causa di un'interruzione nell'area primaria. Quando viene creato per le istanze gestite, un gruppo di failover contiene tutti i database utente nell'istanza e pertanto è possibile configurare un solo gruppo di failover in un'istanza di.
  
  > [!IMPORTANT]
  > Il nome del gruppo di failover deve essere univoco a livello globale all'interno del dominio `.database.windows.net`.

- **Server di database SQL**

     Con i server di database SQL è possibile inserire alcuni o tutti i database utente di un singolo server di database SQL in un gruppo di failover. Un server di database SQL supporta anche più gruppi di failover in un singolo server di database SQL.

- **Server/istanza primaria**

  Il server di database SQL o l'istanza gestita che ospita i database primari nel gruppo di failover.

- **Server/istanza secondaria**

  Il server di database SQL o l'istanza gestita che ospita i database secondari nel gruppo di failover. Il server o l'istanza secondaria non può trovarsi nella stessa area del server o dell'istanza primaria.

- **Aggiunta di database singoli al gruppo di failover**

  È possibile inserire più database singoli nello stesso server di database SQL all'interno dello stesso gruppo di failover. Se si aggiunge un database singolo al gruppo di failover, viene creato automaticamente un database secondario usando la stessa edizione e le stesse dimensioni di calcolo nel server secondario.  Tale server è stato specificato quando è stato creato il gruppo di failover. Se si aggiunge un database che ha già un database secondario nel server secondario, tale collegamento della replica geografica viene ereditato dal gruppo. Quando si aggiunge un database che dispone già di un database secondario in un server che non fa parte del gruppo di failover, viene creato un nuovo database secondario nel server secondario.
  
  > [!IMPORTANT]
  > Verificare che il server secondario non disponga di un database con lo stesso nome, a meno che non si tratti di un database secondario esistente. In gruppi di failover per istanza gestita tutti i database utente vengono replicati. Non è possibile selezionare un subset di database utente per la replica nel gruppo di failover.

- **Aggiunta di database nel pool elastico al gruppo di failover**

  È possibile inserire tutti o alcuni database all'interno di un pool elastico nello stesso gruppo di failover. Se il database primario si trova in un pool elastico, il database secondario viene creato automaticamente nel pool elastico con lo stesso nome (pool secondario). È necessario assicurarsi che il server secondario contenga un pool elastico con lo stesso identico nome e capacità sufficiente per ospitare i database secondari che verranno creati dal gruppo di failover. Se si aggiunge un database nel pool che ha già un database secondario nel pool secondario, tale collegamento della replica geografica viene ereditato dal gruppo. Quando si aggiunge un database che ha già un database secondario in un server che non fa parte del gruppo di failover, viene creato un nuovo database secondario nel pool secondario.
  
- **Zona DNS**

  ID univoco generato automaticamente quando viene creata una nuova istanza. Viene eseguito il provisioning di un certificato con più domini (SAN) per questa istanza per autenticare le connessioni client a qualsiasi istanza nella stessa zona DNS. Le due istanze gestite nello stesso gruppo di failover devono condividere la zona DNS. 
  
  > [!NOTE]
  > Per i gruppi di failover creati per i server di database SQL non è necessario un ID zona DNS.

- **Listener di lettura/scrittura del gruppo di failover**

  Record DNS CNAME che punta all'URL del database primario corrente. Viene creato automaticamente quando viene creato il gruppo di failover e consente al carico di lavoro SQL di lettura/scrittura di riconnettersi in modo trasparente al database primario quando viene modificato il database primario dopo il failover. Quando il gruppo di failover viene creato in un server di database SQL, il record CNAME DNS per l'URL del listener viene formato `<fog-name>.database.windows.net`. Quando il gruppo di failover viene creato in un'istanza gestita, il record CNAME DNS per l'URL del listener viene formato `<fog-name>.zone_id.database.windows.net`.

- **Listener di sola lettura del gruppo di failover**

  Un record CNAME DNS che punta al listener di sola lettura che punta all'URL del database secondario. Viene creato automaticamente quando viene creato il gruppo di failover e consente al carico di lavoro SQL di sola lettura di connettersi in modo trasparente al database secondario usando le regole di bilanciamento del carico specificate. Quando il gruppo di failover viene creato in un server di database SQL, il record CNAME DNS per l'URL del listener viene formato `<fog-name>.secondary.database.windows.net`. Quando il gruppo di failover viene creato in un'istanza gestita, il record CNAME DNS per l'URL del listener viene formato `<fog-name>.zone_id.secondary.database.windows.net`.

- **Criteri di failover automatico**

  Per impostazione predefinita un gruppo di failover viene configurato con criteri di failover automatico. Il servizio del database SQL attiva il failover dopo che è stato rilevato l'errore ed è scaduto il periodo di prova. Il sistema deve verificare che l'interruzione del servizio non può essere risolta dall'[infrastruttura con disponibilità elevata incorporata del servizio del database SQL](sql-database-high-availability.md) a causa dell'entità dell'impatto. Se si desidera controllare il flusso di lavoro del failover dall'applicazione, è possibile disattivare il failover automatico.
  
  > [!NOTE]
  > Poiché la verifica della scalabilità dell'interruzione e la rapidità con cui è possibile mitigare il problema implicano azioni umane da parte del team operativo, il periodo di tolleranza non può essere impostato al di sotto di un'ora.  Questa limitazione si applica a tutti i database nel gruppo di failover indipendentemente dallo stato di sincronizzazione dei dati. 

- **Criteri di failover di sola lettura**

  Per impostazione predefinita, il failover del listener di sola lettura è disabilitato. Verificare che le prestazioni del database primario non siano interessate quando il database secondario è offline. Ciò significa anche che le sessioni di sola lettura non sono in grado di connettersi fino a quando il database secondario non viene recuperato. Se non è possibile tollerare i tempi di inattività per le sessioni di sola lettura e si è pronti a usare temporaneamente il database primario per il traffico di sola lettura e di lettura/scrittura a scapito della potenziale riduzione delle prestazioni del database primario, è possibile abilitare il failover per il listener di sola lettura configurando la proprietà `AllowReadOnlyFailoverToPrimary`. In tal caso, il traffico di sola lettura verrà reindirizzato automaticamente al database primario se il database secondario non è disponibile.

- **Failover pianificato**

   Il failover pianificato esegue la sincronizzazione completa tra il database primario e il database secondario prima che il database secondario passi al ruolo primario. In questo modo si esclude la perdita di dati. Il failover pianificato viene usato negli scenari seguenti:

  - Eseguire esercitazioni per il ripristino di emergenza in produzione quando la perdita di dati non è accettabile
  - Rilocare i database in un'area diversa
  - Ripristinare i database nell'area primaria dopo la risoluzione dell'interruzione (failback).

- **Failover non pianificato**

   Con il failover non pianificato o forzato il database secondario passa immediatamente al ruolo primario senza alcuna sincronizzazione. Questa operazione comporta la perdita di dati. Un failover non pianificato viene usato come metodo di ripristino durante le interruzioni quando non è accessibile il database primario. Quando la replica primaria originale è di nuovo online, viene riconnessa automaticamente senza sincronizzazione e diventa un nuovo database secondario.

- **Failover manuale**

  È possibile avviare manualmente il failover in qualsiasi momento, indipendentemente dalla configurazione del failover automatico. Se non sono configurati criteri di failover automatico, è necessario eseguire il failover manuale per ripristinare i database del gruppo di failover nell'area secondaria. È possibile avviare il failover forzato o semplice (con sincronizzazione completa dei dati). Quest'ultimo può essere usato per rilocare il database primario nell'area secondaria. Quando il failover viene completato, i record DNS vengono aggiornati automaticamente per garantire la connettività al nuovo database primario

- **Periodo di tolleranza con perdita di dati**

  Poiché i database primario e secondario vengono sincronizzati tramite la replica asincrona, il failover può comportare la perdita di dati. È possibile personalizzare i criteri di failover automatico per riflettere la tolleranza dell'applicazione verso la perdita dei dati. Configurando `GracePeriodWithDataLossHours`, è possibile controllare il tempo di attesa del sistema prima di avviare il failover che probabilmente causa la perdita di dati.

- **Più gruppi di failover**

  È possibile configurare più gruppi di failover per la stessa coppia di server per controllare la scalabilità di failover. Ogni gruppo esegue il failover in modo indipendente. Se l'applicazione multi-tenant fa uso di pool elastici, è possibile usare questa funzionalità per combinare i database primari e secondari in ogni pool. In questo modo è possibile ridurre l'impatto di un'interruzione a solo la metà dei tenant.

  > [!NOTE]
  > Istanza gestita non supporta più gruppi di failover.
  
## <a name="permissions"></a>autorizzazioni
Le autorizzazioni per un gruppo di failover vengono gestite tramite il [controllo degli accessi in base al ruolo (RBAC)](../role-based-access-control/overview.md). Il ruolo [collaboratore SQL Server](../role-based-access-control/built-in-roles.md#sql-server-contributor) dispone di tutte le autorizzazioni necessarie per gestire i gruppi di failover. 

### <a name="create-failover-group"></a>Crea gruppo di failover
Per creare un gruppo di failover, è necessario l'accesso in scrittura RBAC ai server primari e secondari e a tutti i database nel gruppo di failover. Per un'istanza gestita, è necessario l'accesso in scrittura RBAC all'istanza gestita primaria e secondaria, ma le autorizzazioni per i singoli database non sono rilevanti perché i singoli database dell'istanza gestita non possono essere aggiunti o rimossi da un gruppo di failover. 

### <a name="update-a-failover-group"></a>Aggiornare un gruppo di failover
Per aggiornare un gruppo di failover, è necessario l'accesso in scrittura RBAC al gruppo di failover e a tutti i database nel server primario o nell'istanza gestita corrente.  

### <a name="failover-a-failover-group"></a>Failover di un gruppo di failover
Per eseguire il failover di un gruppo di failover, è necessario l'accesso in scrittura RBAC al gruppo di failover nel nuovo server primario o nell'istanza gestita. 

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Procedure consigliate per l'uso di gruppi di failover con database singoli e pool elastici

Il gruppo di failover automatico deve essere configurato nel server di database SQL primario e lo connetterà al server di database SQL secondario in un'altra area di Azure. I gruppi possono includere alcuni o tutti i database in questi server. Il diagramma seguente illustra una configurazione tipica di un'applicazione cloud con ridondanza geografica con più database e un gruppo di failover automatico.

![failover automatico](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Per un'esercitazione dettagliata sull'aggiunta di un singolo database a un gruppo di failover, vedere [aggiungere un singolo database a un gruppo di failover](sql-database-single-database-failover-group-tutorial.md) . 


Quando si progetta un servizio facendo particolare attenzione alla continuità aziendale, seguire queste linee guida generali:

- **Usare uno o più gruppi di failover per gestire il failover di più database**

  È possibile creare uno o più gruppi di failover tra due server in aree diverse (server primario e secondario). Ogni gruppo può includere uno o più database che vengono ripristinati come unità nel caso in cui tutti o alcuni database primari diventino non disponibili a causa di un'interruzione nell'area primaria. Il gruppo di failover crea un database di replica geografica secondaria con lo stesso obiettivo di servizio di quello primario. Se si aggiunge una relazione di replica geografica esistente al gruppo di failover, verificare che il database di replica geografica secondario sia configurato con lo stesso livello di servizio e le stesse dimensioni di calcolo del database primario.
  
  > [!IMPORTANT]
  > La creazione di gruppi di failover tra due server in sottoscrizioni diverse non è attualmente supportata per i database singoli e i pool elastici. Se si sposta il server primario o secondario in una sottoscrizione diversa dopo che il gruppo di failover è stato creato, è possibile che si verifichino errori nelle richieste di failover e in altre operazioni.

- **Usare il listener di lettura/scrittura per il carico di lavoro OLTP**

  Quando si eseguono operazioni OLTP, usare `<fog-name>.database.windows.net` come URL del server per indirizzare automaticamente le connessioni al database primario. Questo URL non cambia dopo il failover. Si noti che il failover comporta l'aggiornamento del record DNS in modo che le connessioni client vengano reindirizzate al nuovo database primario solo dopo l'aggiornamento della cache DNS del client.

- **Usare il listener di sola lettura per il carico di lavoro di sola lettura**

  Se è presente un carico di lavoro di sola lettura isolato logicamente che tollera un certo grado di obsolescenza dei dati, è possibile usare il database secondario nell'applicazione. Per sessioni di sola lettura usare `<fog-name>.secondary.database.windows.net` come URL del server per indirizzare automaticamente le connessioni al server secondario. È inoltre consigliabile indicare la finalità di lettura della stringa di connessione utilizzando `ApplicationIntent=ReadOnly`. Per assicurarsi che il carico di lavoro di sola lettura possa riconnettersi dopo il failover o se il server secondario passa alla modalità offline, assicurarsi di configurare la proprietà `AllowReadOnlyFailoverToPrimary` dei criteri di failover. 

- **Prepararsi a un calo delle prestazioni**

  La decisione di failover di SQL è indipendente dal resto dell'applicazione o dagli altri servizi usati. L'applicazione potrebbe essere "mista" con alcuni componenti in un'area e alcuni componenti in un'altra. Per evitare la riduzione delle prestazioni, verificare la distribuzione dell'applicazione ridondante nell'area di ripristino di emergenza e seguire queste [linee guida sulla sicurezza di rete](#failover-groups-and-network-security).

  > [!NOTE]
  > L'applicazione nell'area di ripristino di emergenza non deve usare una stringa di connessione diversa.  

- **Prepararsi alla perdita di dati**

  Se viene rilevata un'interruzione, SQL attende il periodo specificato da `GracePeriodWithDataLossHours`. Il valore predefinito è 1 ora. Se non è possibile permettersi la perdita di dati, assicurarsi di impostare `GracePeriodWithDataLossHours` su un numero sufficientemente elevato, ad esempio 24 ore. Usare il failover manuale dei gruppi per eseguire il failback dal server secondario a quello primario.

  > [!IMPORTANT]
  > I pool elastici con 800 o meno DTU e più di 250 database con replica geografica possono riscontrare problemi quali failover pianificati più lunghi e un peggioramento delle prestazioni.  Questi problemi si verificano con maggiore probabilità nella scrittura di carichi di lavoro con utilizzo intensivo, quando gli endpoint con replica geografica sono ampiamente separati per area geografica o quando vengono utilizzati più endpoint secondari per ogni database.  I sintomi di questi problemi emergono quando il ritardo della replica geografica aumenta nel tempo.  Questo ritardo può essere monitorato mediante [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Se si verificano questi problemi, per prevenirli si può aumentare il numero di DTU nel pool o ridurre il numero di database replicati geograficamente nello stesso pool.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Procedure consigliate per l'utilizzo di gruppi di failover con istanze gestite

Il gruppo di failover automatico deve essere configurato nell'istanza primaria e la connetterà all'istanza secondaria in un'altra area di Azure.  Tutti i database nell'istanza verranno replicati nell'istanza secondaria. 

Il diagramma seguente illustra una configurazione tipica di un'applicazione cloud con ridondanza geografica con un'istanza gestita un gruppo di failover automatico.

![failover automatico](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Per un'esercitazione dettagliata sull'aggiunta di un'istanza gestita per l'uso del gruppo di failover, vedere [aggiungere un'istanza gestita a un gruppo di failover](sql-database-managed-instance-failover-group-tutorial.md) . 

Se l'applicazione usa l'istanza gestita come livello dati, seguire queste linee guida generali durante la progettazione per la continuità aziendale:

- **Creare l'istanza secondaria nella stessa zona DNS dell'istanza primaria**

  Per garantire connettività ininterrotta all'istanza primaria dopo il failover, l'istanza primaria e l'istanza secondaria devono essere nella stessa zona DNS. Garantisce che lo stesso certificato di multidominio (SAN) possa essere utilizzato per autenticare le connessioni client a una delle due istanze del gruppo di failover. Quando l'applicazione è pronta per la distribuzione in produzione, creare un'istanza secondaria in un'area diversa e assicurarsi che condivida la zona DNS con l'istanza primaria. È possibile eseguire questa operazione specificando un parametro facoltativo `DNS Zone Partner` usando il portale di Azure, PowerShell o l'API REST. 

> [!IMPORTANT]
> La prima istanza creata nella subnet determina la zona DNS per tutte le istanze successive nella stessa subnet. Ciò significa che due istanze della stessa subnet non possono appartenere a zone DNS diverse.   

  Per ulteriori informazioni sulla creazione dell'istanza secondaria nella stessa zona DNS dell'istanza primaria, vedere [creare un'istanza gestita secondaria](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

- **Consentire il traffico di replica tra due istanze**

  Poiché ogni istanza è isolata nella propria rete virtuale, è necessario consentire il traffico bidirezionale tra queste reti virtuali. Vedere [Gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Creazione di un gruppo di failover tra istanze gestite in sottoscrizioni diverse**

  È possibile creare un gruppo di failover tra istanze gestite in due sottoscrizioni diverse. Quando si usa l'API di PowerShell, è possibile eseguire questa operazione specificando il parametro `PartnerSubscriptionId` per l'istanza secondaria. Quando si usa l'API REST, ogni ID istanza incluso nel parametro `properties.managedInstancePairs` può avere il proprio subscriptionID. 
  
  > [!IMPORTANT]
  > Il portale di Azure non supporta i gruppi di failover tra sottoscrizioni diverse.

  
- **Configurare un gruppo di failover per gestire il failover dell'intera istanza**

  Il gruppo di failover gestisce il failover di tutti i database nell'istanza. Quando viene creato un gruppo, ogni database nell'istanza sarà automaticamente sottoposto a replica geografica nell'istanza secondaria. Non è possibile usare gruppi di failover per avviare un failover parziale di un subset dei database.

  > [!IMPORTANT]
  > Se un database viene rimosso dall'istanza primaria, l'operazione verrà automaticamente riflessa anche nell'istanza secondaria.

- **Usare il listener di lettura/scrittura per il carico di lavoro OLTP**

  Quando si eseguono operazioni OLTP, usare `<fog-name>.zone_id.database.windows.net` come URL del server per indirizzare automaticamente le connessioni al database primario. Questo URL non cambia dopo il failover. Il failover comporta l'aggiornamento del record DNS in modo che le connessioni client vengano reindirizzate al nuovo database primario solo dopo l'aggiornamento della cache DNS del client. Poiché l'istanza secondaria condivide la zona DNS con la replica primaria, l'applicazione client sarà in grado di riconnetterla utilizzando lo stesso certificato SAN.

- **Connettersi direttamente al database secondario con replica geografica per le query di sola lettura**

  Se è presente un carico di lavoro di sola lettura isolato logicamente che tollera un certo grado di obsolescenza dei dati, è possibile usare il database secondario nell'applicazione. Per connettersi direttamente al database secondario con replica geografica, usare `server.secondary.zone_id.database.windows.net` come URL del server.

  > [!NOTE]
  > In determinati livelli di servizio il database SQL di Azure supporta l'uso di [repliche di sola lettura](sql-database-read-scale-out.md) per bilanciare i carichi di lavoro di query di sola lettura usando la capacità di una replica di sola lettura e il parametro `ApplicationIntent=ReadOnly` nella stringa di connessione. Dopo aver configurato un database secondario con replica geografica, sarà possibile usare questa funzionalità per connettersi a una replica di sola lettura nella posizione primaria o nella posizione con replica geografica.
  > - Per connettersi a una replica di sola lettura nella posizione primaria, usare `<fog-name>.zone_id.database.windows.net`.
  > - Per connettersi a una replica di sola lettura nella posizione secondaria, usare `<fog-name>.secondary.zone_id.database.windows.net`.

- **Prepararsi a un calo delle prestazioni**

  La decisione di failover di SQL è indipendente dal resto dell'applicazione o dagli altri servizi usati. L'applicazione potrebbe essere "mista" con alcuni componenti in un'area e alcuni componenti in un'altra. Per evitare la riduzione delle prestazioni, verificare la distribuzione dell'applicazione ridondante nell'area di ripristino di emergenza e seguire queste [linee guida sulla sicurezza di rete](#failover-groups-and-network-security).

- **Prepararsi alla perdita di dati**

  Se viene rilevata un'interruzione del servizio, SQL attiva automaticamente il failover di lettura/scrittura nel caso in cui la perdita di dati è nulla al meglio delle nostre conoscenze. In caso contrario, attende il periodo specificato da `GracePeriodWithDataLossHours`. Se è stato specificato `GracePeriodWithDataLossHours`, potrebbe verificarsi una perdita di dati. Durante le interruzioni del servizio, generalmente Azure predilige la disponibilità. Se non ci si può permettere una perdita di dati, assicurarsi di impostare GracePeriodWithDataLossHours su un numero sufficientemente elevato, ad esempio 24 ore.

  Subito dopo l'avvio del failover si verificherà l'aggiornamento DNS del listener di lettura/scrittura. Questa operazione non comporta la perdita di dati. Tuttavia, il processo di scambio dei ruoli del database può richiedere fino a 5 minuti in condizioni normali. Durante il processo alcuni database nella nuova istanza primaria rimarranno di sola lettura. Se il failover viene avviato con PowerShell, l'intera operazione è sincrona. Se viene avviata usando il portale di Azure, l'interfaccia utente indicherà lo stato di completamento. Se viene avviato mediante l'API REST, usare il meccanismo di polling standard di Azure Resource Manager per il monitoraggio del completamento.

  > [!IMPORTANT]
  > Usare il failover manuale dei gruppi per ripristinare i database primari nella posizione originale. Quando viene risolta l'interruzione del servizio che ha determinato il failover, sarà possibile spostare i database primari nel percorso originale. A tale scopo è necessario avviare il failover manuale del gruppo.

- **Confermare le limitazioni note dei gruppi di failover**

  La ridenominazione del database e il ridimensionamento dell'istanza non sono supportati per le istanze nel gruppo di failover. Sarà necessario eliminare temporaneamente il gruppo di failover per poter eseguire queste operazioni.

## <a name="failover-groups-and-network-security"></a>Gruppi di failover e sicurezza di rete

Per alcune applicazioni, le regole di sicurezza richiedono che l'accesso di rete al livello dati sia limitato a un componente o a componenti specifici, ad esempio una macchina virtuale, un servizio Web e così via. Questo requisito presenta alcune difficolte per la progettazione della continuità aziendale e l'utilizzo dei gruppi di failover. Quando si implementa tale accesso limitato, tenere presenti le opzioni seguenti.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Uso dei gruppi di failover e delle regole di rete virtuale

Se si usano [endpoint servizio e regole di rete virtuale](sql-database-vnet-service-endpoint-rule-overview.md) per limitare l'accesso al database SQL, tenere presente che ogni endpoint servizio di rete virtuale si applica a una sola area geografica di Azure. L'endpoint non consente ad altre aree di accettare le comunicazioni dalla subnet. Pertanto, solo le applicazioni client distribuite nella stessa area possono connettersi al database primario. Poiché il failover ha per effetto il reindirizzamento delle sessioni client SQL a un server presente in un'altra area geografica (secondaria), tali sessioni avranno esito negativo se hanno avuto origine da un client al di fuori di tale area. Per questo motivo non è possibile abilitare i criteri di failover automatico se i server partecipanti sono inclusi nelle regole di rete virtuale. Per supportare il failover manuale, seguire questa procedura:

1. Effettuare il provisioning delle copie ridondanti dei componenti front-end dell'applicazione (servizio Web, macchine virtuali e così via) nell'area secondaria
2. Configurare le [regole di rete virtuale](sql-database-vnet-service-endpoint-rule-overview.md) singolarmente per il server primario e per quello secondario
3. Abilitare il [failover front-end usando una configurazione di Gestione traffico](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Avviare il failover manuale quando viene rilevata l'interruzione. Questa opzione è ottimizzata per le applicazioni che richiedono la latenza coerente tra il front-end e il livello dati e supporta il ripristino quando il front-end, il livello dati o entrambi sono interessati dall'interruzione del servizio.

> [!NOTE]
> Se si usa il **listener di sola lettura** per il bilanciamento di un carico di lavoro di sola lettura, assicurarsi che il carico di lavoro venga eseguito in una macchina virtuale o in un'altra risorsa nell'area secondaria in modo da consentire la connessione al database secondario.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Uso dei gruppi di failover e delle regole del firewall del database SQL

Se il piano di continuità aziendale richiede il failover tramite gruppi con failover automatico, è possibile limitare l'accesso al database SQL usando le regole del firewall tradizionali.  Per supportare il failover automatico, seguire questa procedura:

1. [Creare un indirizzo IP pubblico](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address).
2. [Creare un servizio di bilanciamento del carico pubblico](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) e assegnare l'indirizzo IP pubblico a tale servizio.
3. [Creare una rete virtuale e le macchine virtuali](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) per i componenti front-end.
4. [Creare un gruppo di sicurezza di rete](../virtual-network/security-overview.md) e configurare le connessioni in ingresso.
5. Assicurarsi che le connessioni in uscita siano aperte al database SQL di Azure usando il [tag di servizio](../virtual-network/security-overview.md#service-tags) 'Sql'.
6. Creare un [regola del firewall del database SQL](sql-database-firewall-configure.md) per consentire il traffico in ingresso dall'indirizzo IP pubblico creato nel passaggio 1.

Per altre informazioni su come configurare l'accesso in uscita e su quale indirizzo IP usare nelle regole del firewall, vedere [Connessioni in uscita del servizio di bilanciamento del carico](../load-balancer/load-balancer-outbound-connections.md).

La configurazione appena illustrata garantisce che il failover automatico non blocchi le connessioni tra i componenti front-end e presuppone che l'applicazione riesca a tollerare una maggiore latenza tra il front-end e il livello dati.

> [!IMPORTANT]
> Per garantire la continuità aziendale nel caso di interruzioni del servizio a livello di area è necessario verificare la ridondanza geografica sia per i componenti front-end che per i database.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Abilitazione della replica geografica tra le istanze gestite e i relativi reti virtuali

Quando si configura un gruppo di failover tra istanze gestite primarie e secondarie in due aree diverse, ogni istanza viene isolata usando una rete virtuale indipendente. Per consentire il traffico di replica tra questi reti virtuali, verificare che siano soddisfatti i prerequisiti seguenti:

1. Le due istanze gestite devono trovarsi in aree di Azure diverse.
1. È necessario che le due istanze gestite siano lo stesso livello di servizio e abbiano le stesse dimensioni di archiviazione. 
1. L'istanza gestita secondaria deve essere vuota (nessun database utente).
1. Le reti virtuali usate dalle istanze gestite devono essere connesse tramite un [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o Express route. Quando due reti virtuali si connettono tramite una rete locale, assicurarsi che non siano presenti porte di blocco della regola firewall 5022 e 11000-11999. Il peering di reti virtuali globale non è supportato.
1. I due reti virtuali istanza gestita non possono avere indirizzi IP sovrapposti.
1. È necessario configurare i gruppi di sicurezza di rete (NSG) in modo che le porte 5022 e l'intervallo 11000 ~ 12000 siano aperte in ingresso e in uscita per le connessioni dall'altra subnet istanza gestita. per consentire il traffico di replica tra le istanze

   > [!IMPORTANT]
   > Regole di sicurezza dei gruppi di sicurezza di rete non configurate correttamente determinano il blocco delle operazioni di copia del database.

7. L'istanza secondaria è configurata con l'ID della zona DNS corretto. La zona DNS è una proprietà di un'istanza gestita e il relativo ID è incluso nell'indirizzo del nome host. L'ID zona viene generato come stringa casuale quando la prima istanza gestita viene creata in ogni VNet e lo stesso ID viene assegnato a tutte le altre istanze nella stessa subnet. Una volta assegnato, la zona DNS non può essere modificata. Le istanze gestite incluse nello stesso gruppo di failover devono condividere la zona DNS. A tale scopo, passare l'ID zona dell'istanza primaria come valore del parametro DnsZonePartner durante la creazione dell'istanza secondaria. 

   > [!NOTE]
   > Per un'esercitazione dettagliata sulla configurazione di gruppi di failover con istanza gestita, vedere [aggiungere un'istanza gestita a un gruppo di failover](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Aggiornamento o downgrade di un database primario

È possibile eseguire l'aggiornamento o il downgrade di un database primario a dimensioni di calcolo diverse (entro lo stesso livello di servizio e non tra il livello per utilizzo generico e business critical) senza disconnettere eventuali database secondari. Quando si esegue l'aggiornamento, è consigliabile aggiornare prima tutti i database secondari, quindi aggiornare il database primario. Quando si esegue il downgrade, invertire l'ordine: eseguire prima il downgrade del primario, quindi eseguire il downgrade di tutti i database secondari. Quando si aggiorna o si effettua il downgrade del database a un livello di servizio diverso, viene applicata questa raccomandazione.

Questa sequenza è consigliata in modo specifico per evitare il problema per cui il database secondario in uno SKU inferiore viene sottoposto a overload e deve essere reinizializzato durante un processo di aggiornamento o di downgrade. È anche possibile evitare il problema rendendo la replica primaria di sola lettura, a scapito dell'effetto di tutti i carichi di lavoro di lettura/scrittura sul database primario. 

> [!NOTE]
> Se è stato creato il database secondario come parte della configurazione del gruppo di failover, non è consigliabile eseguire il downgrade del database secondario. In questo modo si garantisce che il livello dei dati abbia una capacità sufficiente per elaborare il carico di lavoro normale dopo che il failover viene attivato.

## <a name="preventing-the-loss-of-critical-data"></a>Evitare la perdita di dati critici

A causa della latenza elevata delle reti WAN, per la copia continua viene usato un meccanismo di replica asincrona. La replica asincrona rende inevitabile una perdita parziale dei dati nel caso si verifichi un problema. Tuttavia, alcune applicazione potrebbero non essere soggette alla perdita dei dati. Per proteggere questi aggiornamenti critici, uno sviluppatore di applicazioni può chiamare la procedura di sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) subito dopo il commit della transazione. La chiamata a `sp_wait_for_database_copy_sync` blocca il thread chiamante finché l'ultima transazione di cui è stato eseguito il commit non è stata trasmessa al database secondario. Tuttavia, non attende che le transazioni trasmesse vengano riprodotta e che ne venga eseguito il commit nel database secondario. `sp_wait_for_database_copy_sync` ha come ambito un collegamento di copia continua specifico. La procedura può essere chiamata da qualsiasi utente che abbia diritti di connessione al database primario.

> [!NOTE]
> `sp_wait_for_database_copy_sync` impedisce la perdita di dati dopo il failover, ma non garantisce la sincronizzazione completa per l'accesso in lettura. Il ritardo causato da una chiamata di procedura `sp_wait_for_database_copy_sync` può essere significativo e dipende dalle dimensioni del log delle transazioni al momento della chiamata.

## <a name="failover-groups-and-point-in-time-restore"></a>Gruppi di failover e ripristino temporizzato

Per informazioni sull'uso del ripristino temporizzato con gruppi di failover, vedere [Point in Time Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore) (Recupero temporizzato).

## <a name="programmatically-managing-failover-groups"></a>Gestione di gruppi di failover a livello di codice

Come indicato in precedenza, i gruppi di failover automatico e la replica geografica attiva possono essere gestiti a livello di codice usando Azure PowerShell e l'API REST. Le tabelle seguenti descrivono il set di comandi disponibili. La replica geografica attiva include un set di API di Azure Resource Manager per la gestione, compresa l'[API REST del Database SQL di Azure](https://docs.microsoft.com/rest/api/sql/) e i [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Queste API richiedono l'uso di gruppi di risorse e supportano la sicurezza basata sui ruoli (Controllo degli accessi in base al ruolo). Per altre informazioni su come implementare i ruoli di accesso, vedere [Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md).

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>PowerShell: gestire il failover del database SQL con database singoli e pool elastici

| Cmdlet | Descrizione |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Questo comando crea un gruppo di failover e lo registra nei server primario e secondario|
| [Remove-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Rimuove il gruppo di failover dal server ed elimina tutti i database secondari incluso il gruppo |
| [Get-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Recupera la configurazione del gruppo di failover |
| [Set-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modifica la configurazione del gruppo di failover |
| [Switch-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Attiva il failover del gruppo di failover per il server secondario |
| [Add-AzSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Aggiunge uno o più database a un gruppo di failover del database SQL di Azure|
|  | |

> [!IMPORTANT]
> Per uno script di esempio, vedere [Configure and failover a failover group for a single database](scripts/sql-database-add-single-db-to-failover-group-powershell.md) (Configurare ed eseguire il failover di un gruppo di failover per un singolo database).
>

### <a name="powershell-managing-sql-database-failover-groups-with-managed-instances"></a>PowerShell: gestione dei gruppi di failover del database SQL con istanze gestite 

| Cmdlet | Descrizione |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Questo comando crea un gruppo di failover e lo registra nei server primario e secondario|
| [Set-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modifica la configurazione del gruppo di failover|
| [Get-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Recupera la configurazione del gruppo di failover|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Attiva il failover del gruppo di failover per il server secondario|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Rimuove un gruppo di failover|
|  | |

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>API REST: gestire i gruppi di failover del database SQL con database singoli e in pool

| API | Descrizione |
| --- | --- |
| [Create or Update Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Crea o aggiorna un gruppo di failover. |
| [Delete Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Rimuove il gruppo di failover dal server |
| [Failover (Planned)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Viene eseguito il failover dal server primario corrente a questo server. |
| [Force Failover Allow Data Loss](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |Viene eseguito il failover dal server primario corrente a questo server. Questa operazione potrebbe comportare la perdita di dati. |
| [Get Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Crea un gruppo di failover. |
| [List Failover Groups By Server](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Elenca i gruppi di failover in un server. |
| [Update Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Aggiorna un gruppo di failover. |
|  | |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>API REST: gestire i gruppi di failover con istanze gestite

| API | Descrizione |
| --- | --- |
| [Create or Update Failover Group](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Crea o aggiorna un gruppo di failover. |
| [Delete Failover Group](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Rimuove il gruppo di failover dal server |
| [Failover (Planned)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Viene eseguito il failover dal server primario corrente a questo server. |
| [Force Failover Allow Data Loss](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |Viene eseguito il failover dal server primario corrente a questo server. Questa operazione potrebbe comportare la perdita di dati. |
| [Get Failover Group](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Crea un gruppo di failover. |
| [List Failover Groups - List By Location](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Elenca i gruppi di failover in una posizione. |

## <a name="next-steps"></a>Passaggi successivi

- Per esercitazioni dettagliate, vedere
    - [Aggiungere un singolo database a un gruppo di failover](sql-database-single-database-failover-group-tutorial.md)
    - [Aggiungere un pool elastico a un gruppo di failover](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Aggiungere un'istanza gestita a un gruppo di failover](sql-database-managed-instance-failover-group-tutorial.md)
- Per script di esempio, vedere:
  - [Usare PowerShell per configurare la replica geografica attiva per un database singolo nel database SQL di Azure](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Usare PowerShell per configurare la replica geografica attiva per un database in pool nel database SQL di Azure](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Usare PowerShell per aggiungere un database singolo di database SQL di Azure a un gruppo di failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
- Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).
- Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md).
- Per ulteriori informazioni sui requisiti di autenticazione per un nuovo database e server primario, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).
