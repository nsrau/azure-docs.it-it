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
ms.date: 11/07/2019
ms.openlocfilehash: 470e9a9c36b6b4ec2e40db5dfc47ae03fb6b5aa8
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421380"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Usare i gruppi di failover automatico per consentire il failover trasparente e coordinato di più database

Auto-failover groups is a SQL Database feature that allows you to manage replication and failover of a group of databases on a SQL Database server or all databases in a managed instance to another region. It is a declarative abstraction on top of the existing [active geo-replication](sql-database-active-geo-replication.md) feature, designed to simplify deployment and management of geo-replicated databases at scale. È possibile avviare manualmente il failover oppure delegarlo al servizio del database SQL in base a criteri definiti dall'utente. Grazie a questa seconda opzione è possibile ripristinare automaticamente più database correlati in un'area secondaria dopo un errore irreversibile o un altro evento imprevisto che comporta la perdita parziale o completa di disponibilità del servizio del database SQL nell'area primaria. A failover group can include one or multiple databases, typically used by the same application. È inoltre possibile usare i database secondari leggibili per l'offload dei carichi di lavoro delle query di sola lettura. Poiché i gruppi di failover automatico coinvolgono più database, questi ultimi devono essere configurati nel server primario. I gruppi di failover automatico supportano la replica di tutti i database nel gruppo in un solo server secondario in un'area diversa.

> [!NOTE]
> Se si usano database singoli o in pool in un server di database SQL e si intende avere più database secondari nella stessa area geografica o in aree diverse, usare la [replica geografica attiva](sql-database-active-geo-replication.md). 

Se si usano gruppi di failover automatico con criteri di failover automatico, eventuali interruzioni che influiscono su uno o più database nel gruppo determinano un failover automatico. Typically these are incidents that cannot be self-mitigated by the built-in automatic high availability operations. The examples of failover triggers include an incident caused by a SQL tenant ring or control ring being down due to an OS kernel memory leak on several compute nodes, or an incident caused by one or more tenant rings being down because a wrong network cable was cut during routine hardware decommissioning.  For more information, see [SQL Database High Availability](sql-database-high-availability.md).

I gruppi di failover automatico forniscono anche endpoint di listener di sola lettura e di sola scrittura che rimangono invariati durante i failover. Se si usa l'attivazione di failover manuale o automatica, il failover trasforma tutti i database secondari nel gruppo in database primari. Dopo aver completato il failover del database, il record DNS viene automaticamente aggiornato per reindirizzare gli endpoint alla nuova area. Per i dati RPO e RTO specifici consultare la [panoramica della continuità aziendale](sql-database-business-continuity.md).

Se si usano gruppi di failover automatico con criteri di failover automatico, eventuali interruzioni che influiscono sui database nel server di database SQL o nell'istanza gestita determinano un failover automatico. È possibile gestire il gruppo di failover automatico mediante:

- Il[portale di Azure](sql-database-implement-geo-distributed-database.md)
- [PowerShell: gruppo di failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [API REST: gruppo di failover](https://docs.microsoft.com/rest/api/sql/failovergroups).

Dopo il failover, verificare che nel nuovo database primario siano configurati i requisiti di autenticazione relativi al server e al database. Per tutti i dettagli, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

Per ottenere una reale continuità aziendale, l'aggiunta di ridondanza dei database tra i data center rappresenta solo una parte della soluzione. Per ripristinare un'applicazione (servizio) end-to-end dopo un problema grave, è necessario effettuare il ripristino di tutti i componenti del servizio e gli eventuali servizi dipendenti. Esempi di questi componenti includono il software client (ad esempio, un browser con JavaScript personalizzato), front-end Web, spazio di archiviazione e DNS. È fondamentale che tutti i componenti siano resilienti agli stessi problemi e diventino disponibili entro I'obiettivo del tempo di ripristino (RTO) dell'applicazione. È perciò necessario identificare tutti i servizi dipendenti e comprendere quali garanzie e funzionalità vengono fornite. È quindi necessario intraprendere le azioni appropriate per assicurare il funzionamento del servizio durante il failover dei servizi da cui dipende. Per altre informazioni sulla progettazione di soluzioni per il ripristino di emergenza, vedere [Progettazione di applicazioni per il ripristino di emergenza cloud con la replica geografica attiva in database SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Funzionalità e terminologia dei gruppi di failover automatico

- **Failover group (FOG)**

  A failover group is a named group of databases managed by a single SQL Database server or within a single managed instance that can fail over as a unit to another region in case all or some primary databases become unavailable due to an outage in the primary region. When created for managed instances, a failover group contains all user databases in the instance and therefore only one failover group can be configured on an instance.
  
  > [!IMPORTANT]
  > The name of the failover group must be globally unique within the `.database.windows.net` domain.

- **Server di database SQL**

     Con i server di database SQL è possibile inserire alcuni o tutti i database utente di un singolo server di database SQL in un gruppo di failover. Un server di database SQL supporta anche più gruppi di failover in un singolo server di database SQL.

- **Server/istanza primaria**

  The SQL Database server or managed instance that hosts the primary databases in the failover group.

- **Server/istanza secondaria**

  The SQL Database server or managed instance that hosts the secondary databases in the failover group. Il server o l'istanza secondaria non può trovarsi nella stessa area del server o dell'istanza primaria.

- **Aggiunta di database singoli al gruppo di failover**

  È possibile inserire più database singoli nello stesso server di database SQL all'interno dello stesso gruppo di failover. Se si aggiunge un database singolo al gruppo di failover, viene creato automaticamente un database secondario usando la stessa edizione e le stesse dimensioni di calcolo nel server secondario.  Tale server è stato specificato quando è stato creato il gruppo di failover. Se si aggiunge un database che ha già un database secondario nel server secondario, tale collegamento della replica geografica viene ereditato dal gruppo. Quando si aggiunge un database che dispone già di un database secondario in un server che non fa parte del gruppo di failover, viene creato un nuovo database secondario nel server secondario.

  > [!IMPORTANT]
  > Make sure that the secondary server doesn't have a database with the same name unless it is an existing secondary database. In failover groups for managed instance all user databases are replicated. Non è possibile selezionare un subset di database utente per la replica nel gruppo di failover.

- **Aggiunta di database nel pool elastico al gruppo di failover**

  È possibile inserire tutti o alcuni database all'interno di un pool elastico nello stesso gruppo di failover. Se il database primario si trova in un pool elastico, il database secondario viene creato automaticamente nel pool elastico con lo stesso nome (pool secondario). È necessario assicurarsi che il server secondario contenga un pool elastico con lo stesso identico nome e capacità sufficiente per ospitare i database secondari che verranno creati dal gruppo di failover. Se si aggiunge un database nel pool che ha già un database secondario nel pool secondario, tale collegamento della replica geografica viene ereditato dal gruppo. Quando si aggiunge un database che ha già un database secondario in un server che non fa parte del gruppo di failover, viene creato un nuovo database secondario nel pool secondario.
  
- **Zona DNS**

  A unique ID that is automatically generated when a new instance is created. A multi-domain (SAN) certificate for this instance is provisioned to authenticate the client connections to any instance in the same DNS zone. The two managed instances in the same failover group must share the DNS zone.
  
  > [!NOTE]
  > A DNS zone ID is not required for failover groups created for SQL Database servers.

- **Listener di lettura/scrittura del gruppo di failover**

  A DNS CNAME record that points to the current primary's URL. It is created automatically when the failover group is created and allows the read-write SQL workload to transparently reconnect to the primary database when the primary changes after failover. When the failover group is created on a SQL Database server, the DNS CNAME record for the listener URL is formed as `<fog-name>.database.windows.net`. When the failover group is created on a managed instance, the DNS CNAME record for the listener URL is formed as `<fog-name>.zone_id.database.windows.net`.

- **Listener di sola lettura del gruppo di failover**

  Un record CNAME DNS che punta al listener di sola lettura che punta all'URL del database secondario. It is created automatically when the failover group is created and allows the read-only SQL workload to transparently connect to the secondary using the specified load-balancing rules. When the failover group is created on a SQL Database server, the DNS CNAME record for the listener URL is formed as `<fog-name>.secondary.database.windows.net`. When the failover group is created on a managed instance, the DNS CNAME record for the listener URL is formed as `<fog-name>.zone_id.secondary.database.windows.net`.

- **Criteri di failover automatico**

  Per impostazione predefinita un gruppo di failover viene configurato con criteri di failover automatico. Il servizio del database SQL attiva il failover dopo che è stato rilevato l'errore ed è scaduto il periodo di prova. Il sistema deve verificare che l'interruzione del servizio non può essere risolta dall'[infrastruttura con disponibilità elevata incorporata del servizio del database SQL](sql-database-high-availability.md) a causa dell'entità dell'impatto. Se si desidera controllare il flusso di lavoro del failover dall'applicazione, è possibile disattivare il failover automatico.
  
  > [!NOTE]
  > Because verification of the scale of the outage and how quickly it can be mitigated involves human actions by the operations team, the grace period cannot be set below one hour.  This  limitation applies to all databases in the failover group regardless of their data synchronization state. 

- **Criteri di failover di sola lettura**

  Per impostazione predefinita, il failover del listener di sola lettura è disabilitato. Verificare che le prestazioni del database primario non siano interessate quando il database secondario è offline. Ciò significa anche che le sessioni di sola lettura non sono in grado di connettersi fino a quando il database secondario non viene recuperato. If you cannot tolerate downtime for the read-only sessions and are OK to temporarily use the primary for both read-only and read-write traffic at the expense of the potential performance degradation of the primary, you can enable failover for the read-only listener by configuring the `AllowReadOnlyFailoverToPrimary` property. In that case, the read-only traffic will be automatically redirected to the primary if the secondary is not available.

- **Failover pianificato**

   Il failover pianificato esegue la sincronizzazione completa tra il database primario e il database secondario prima che il database secondario passi al ruolo primario. In questo modo si esclude la perdita di dati. Il failover pianificato viene usato negli scenari seguenti:

  - Eseguire esercitazioni per il ripristino di emergenza in produzione quando la perdita di dati non è accettabile
  - Rilocare i database in un'area diversa
  - Ripristinare i database nell'area primaria dopo la risoluzione dell'interruzione (failback).

- **Failover non pianificato**

   Con il failover non pianificato o forzato il database secondario passa immediatamente al ruolo primario senza alcuna sincronizzazione. Questa operazione comporta la perdita di dati. Un failover non pianificato viene usato come metodo di ripristino durante le interruzioni quando non è accessibile il database primario. When the original primary is back online, it will automatically reconnect without synchronization and become a new secondary.

- **Failover manuale**

  È possibile avviare manualmente il failover in qualsiasi momento, indipendentemente dalla configurazione del failover automatico. Se non sono configurati criteri di failover automatico, è necessario eseguire il failover manuale per ripristinare i database del gruppo di failover nell'area secondaria. È possibile avviare il failover forzato o semplice (con sincronizzazione completa dei dati). Quest'ultimo può essere usato per rilocare il database primario nell'area secondaria. Quando il failover viene completato, i record DNS vengono aggiornati automaticamente per garantire la connettività al nuovo database primario

- **Periodo di tolleranza con perdita di dati**

  Poiché i database primario e secondario vengono sincronizzati tramite la replica asincrona, il failover può comportare la perdita di dati. È possibile personalizzare i criteri di failover automatico per riflettere la tolleranza dell'applicazione verso la perdita dei dati. By configuring `GracePeriodWithDataLossHours`, you can control how long the system waits before initiating the failover that is likely to result data loss.

- **Più gruppi di failover**

  È possibile configurare più gruppi di failover per la stessa coppia di server per controllare la scalabilità di failover. Ogni gruppo esegue il failover in modo indipendente. Se l'applicazione multi-tenant fa uso di pool elastici, è possibile usare questa funzionalità per combinare i database primari e secondari in ogni pool. In questo modo è possibile ridurre l'impatto di un'interruzione a solo la metà dei tenant.

  > [!NOTE]
  > Istanza gestita non supporta più gruppi di failover.
  
## <a name="permissions"></a>autorizzazioni

Permissions for a failover group are managed via [role-based access control (RBAC)](../role-based-access-control/overview.md). The [SQL Server Contributor](../role-based-access-control/built-in-roles.md#sql-server-contributor) role has all the necessary permissions to manage failover groups.

### <a name="create-failover-group"></a>Create failover group

To create a failover group, you need RBAC write access to both the primary and secondary servers, and to all databases in the failover group. For a managed instance, you need RBAC write access to both the primary and secondary managed instance, but permissions on individual databases are not relevant since individual managed instance databases cannot be added to or removed from a failover group. 

### <a name="update-a-failover-group"></a>Update a failover group

To update a failover group, you need RBAC write access to the failover group, and all databases on the current primary server or managed instance.  

### <a name="failover-a-failover-group"></a>Failover a failover group

To fail over a failover group, you need RBAC write access to the failover group on the new primary server or managed instance.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Procedure consigliate per l'uso di gruppi di failover con database singoli e pool elastici

Il gruppo di failover automatico deve essere configurato nel server di database SQL primario e lo connetterà al server di database SQL secondario in un'altra area di Azure. I gruppi possono includere alcuni o tutti i database in questi server. Il diagramma seguente illustra una configurazione tipica di un'applicazione cloud con ridondanza geografica con più database e un gruppo di failover automatico.

![failover automatico](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> See [Add single database to a failover group](sql-database-single-database-failover-group-tutorial.md) for a detailed step-by-step tutorial adding a single database to a failover group.

Quando si progetta un servizio facendo particolare attenzione alla continuità aziendale, seguire queste linee guida generali:

- **Usare uno o più gruppi di failover per gestire il failover di più database**

  È possibile creare uno o più gruppi di failover tra due server in aree diverse (server primario e secondario). Ogni gruppo può includere uno o più database che vengono ripristinati come unità nel caso in cui tutti o alcuni database primari diventino non disponibili a causa di un'interruzione nell'area primaria. Il gruppo di failover crea un database di replica geografica secondaria con lo stesso obiettivo di servizio di quello primario. Se si aggiunge una relazione di replica geografica esistente al gruppo di failover, verificare che il database di replica geografica secondario sia configurato con lo stesso livello di servizio e le stesse dimensioni di calcolo del database primario.
  
  > [!IMPORTANT]
  > Creating failover groups between two servers in different subscriptions is not currently supported for single databases and elastic pools. If you move the primary or secondary server to a different subscription after the failover group has been created, it could result in failures of the failover requests and other operations.

- **Usare il listener di lettura/scrittura per il carico di lavoro OLTP**

  Quando si eseguono operazioni OLTP, usare `<fog-name>.database.windows.net` come URL del server per indirizzare automaticamente le connessioni al database primario. Questo URL non cambia dopo il failover. Si noti che il failover comporta l'aggiornamento del record DNS in modo che le connessioni client vengano reindirizzate al nuovo database primario solo dopo l'aggiornamento della cache DNS del client.

- **Usare il listener di sola lettura per il carico di lavoro di sola lettura**

  Se è presente un carico di lavoro di sola lettura isolato logicamente che tollera un certo grado di obsolescenza dei dati, è possibile usare il database secondario nell'applicazione. Per sessioni di sola lettura usare `<fog-name>.secondary.database.windows.net` come URL del server per indirizzare automaticamente le connessioni al server secondario. It is also recommended that you indicate in connection string read intent by using `ApplicationIntent=ReadOnly`. If you want to ensure that the read-only workload can reconnect after failover or in case the secondary server goes offline, make sure to configure the `AllowReadOnlyFailoverToPrimary` property of the failover policy.

- **Prepararsi a un calo delle prestazioni**

  La decisione di failover di SQL è indipendente dal resto dell'applicazione o dagli altri servizi usati. L'applicazione potrebbe essere "mista" con alcuni componenti in un'area e alcuni componenti in un'altra. Per evitare la riduzione delle prestazioni, verificare la distribuzione dell'applicazione ridondante nell'area di ripristino di emergenza e seguire queste [linee guida sulla sicurezza di rete](#failover-groups-and-network-security).

  > [!NOTE]
  > L'applicazione nell'area di ripristino di emergenza non deve usare una stringa di connessione diversa.  

- **Prepararsi alla perdita di dati**

  If an outage is detected, SQL waits for the period you specified by `GracePeriodWithDataLossHours`. Il valore predefinito è 1 ora. If you cannot afford data loss, make sure to set `GracePeriodWithDataLossHours` to a sufficiently large number, such as 24 hours. Usare il failover manuale dei gruppi per eseguire il failback dal server secondario a quello primario.

  > [!IMPORTANT]
  > I pool elastici con 800 o meno DTU e più di 250 database con replica geografica possono riscontrare problemi quali failover pianificati più lunghi e un peggioramento delle prestazioni.  Questi problemi si verificano con maggiore probabilità nella scrittura di carichi di lavoro con utilizzo intensivo, quando gli endpoint con replica geografica sono ampiamente separati per area geografica o quando vengono utilizzati più endpoint secondari per ogni database.  I sintomi di questi problemi emergono quando il ritardo della replica geografica aumenta nel tempo.  Questo ritardo può essere monitorato mediante [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Se si verificano questi problemi, per prevenirli si può aumentare il numero di DTU nel pool o ridurre il numero di database replicati geograficamente nello stesso pool.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Best practices of using failover groups with managed instances

Il gruppo di failover automatico deve essere configurato nell'istanza primaria e la connetterà all'istanza secondaria in un'altra area di Azure.  Tutti i database nell'istanza verranno replicati nell'istanza secondaria.

Il diagramma seguente illustra una configurazione tipica di un'applicazione cloud con ridondanza geografica con un'istanza gestita un gruppo di failover automatico.

![failover automatico](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> See [Add managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md) for a detailed step-by-step tutorial adding a managed instance to use failover group.

If your application uses managed instance as the data tier, follow these general guidelines when designing for business continuity:

- **Creare l'istanza secondaria nella stessa zona DNS dell'istanza primaria**

  Per garantire connettività ininterrotta all'istanza primaria dopo il failover, l'istanza primaria e l'istanza secondaria devono essere nella stessa zona DNS. It will guarantee that the same multi-domain (SAN) certificate can be used to authenticate the client connections to either of the two instances in the failover group. Quando l'applicazione è pronta per la distribuzione in produzione, creare un'istanza secondaria in un'area diversa e assicurarsi che condivida la zona DNS con l'istanza primaria. You can do it by specifying a `DNS Zone Partner` optional parameter using the Azure portal, PowerShell, or the REST API.

> [!IMPORTANT]
> First instance created in the subnet determines DNS zone for all subsequent instances in the same subnet. This means that two instances from the same subnet cannot belong to different DNS zones.

  For more information about creating the secondary instance in the same DNS zone as the primary instance, see [Create a secondary managed instance](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

- **Consentire il traffico di replica tra due istanze**

  Poiché ogni istanza è isolata nella propria rete virtuale, è necessario consentire il traffico bidirezionale tra queste reti virtuali. Vedere [Gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Create a failover group between managed instances in different subscriptions**

  You can create a failover group between managed instances in two different subscriptions. When using PowerShell API you can do it by  specifying the `PartnerSubscriptionId` parameter for the secondary instance. When using REST API, each instance ID included in the `properties.managedInstancePairs` parameter can have its own subscriptionID.
  
  > [!IMPORTANT]
  > Azure Portal does not support failover groups across different subscriptions.

- **Configurare un gruppo di failover per gestire il failover dell'intera istanza**

  Il gruppo di failover gestisce il failover di tutti i database nell'istanza. Quando viene creato un gruppo, ogni database nell'istanza sarà automaticamente sottoposto a replica geografica nell'istanza secondaria. Non è possibile usare gruppi di failover per avviare un failover parziale di un subset dei database.

  > [!IMPORTANT]
  > Se un database viene rimosso dall'istanza primaria, l'operazione verrà automaticamente riflessa anche nell'istanza secondaria.

- **Usare il listener di lettura/scrittura per il carico di lavoro OLTP**

  Quando si eseguono operazioni OLTP, usare `<fog-name>.zone_id.database.windows.net` come URL del server per indirizzare automaticamente le connessioni al database primario. Questo URL non cambia dopo il failover. Il failover comporta l'aggiornamento del record DNS in modo che le connessioni client vengano reindirizzate al nuovo database primario solo dopo l'aggiornamento della cache DNS del client. Because the secondary instance shares the DNS zone with the primary, the client application will be able to reconnect to it using the same SAN certificate.

- **Connettersi direttamente al database secondario con replica geografica per le query di sola lettura**

  Se è presente un carico di lavoro di sola lettura isolato logicamente che tollera un certo grado di obsolescenza dei dati, è possibile usare il database secondario nell'applicazione. Per connettersi direttamente al database secondario con replica geografica, usare `server.secondary.zone_id.database.windows.net` come URL del server.

  > [!NOTE]
  > In determinati livelli di servizio il database SQL di Azure supporta l'uso di [repliche di sola lettura](sql-database-read-scale-out.md) per bilanciare i carichi di lavoro di query di sola lettura usando la capacità di una replica di sola lettura e il parametro `ApplicationIntent=ReadOnly` nella stringa di connessione. Dopo aver configurato un database secondario con replica geografica, sarà possibile usare questa funzionalità per connettersi a una replica di sola lettura nella posizione primaria o nella posizione con replica geografica.
  > - Per connettersi a una replica di sola lettura nella posizione primaria, usare `<fog-name>.zone_id.database.windows.net`.
  > - To connect to a read-only replica in the secondary location, use `<fog-name>.secondary.zone_id.database.windows.net`.

- **Prepararsi a un calo delle prestazioni**

  La decisione di failover di SQL è indipendente dal resto dell'applicazione o dagli altri servizi usati. L'applicazione potrebbe essere "mista" con alcuni componenti in un'area e alcuni componenti in un'altra. Per evitare la riduzione delle prestazioni, verificare la distribuzione dell'applicazione ridondante nell'area di ripristino di emergenza e seguire queste [linee guida sulla sicurezza di rete](#failover-groups-and-network-security).

- **Prepararsi alla perdita di dati**

  Se viene rilevata un'interruzione del servizio, SQL attiva automaticamente il failover di lettura/scrittura nel caso in cui la perdita di dati è nulla al meglio delle nostre conoscenze. In caso contrario, attende il periodo specificato da `GracePeriodWithDataLossHours`. Se è stato specificato `GracePeriodWithDataLossHours`, potrebbe verificarsi una perdita di dati. Durante le interruzioni del servizio, generalmente Azure predilige la disponibilità. Se non ci si può permettere una perdita di dati, assicurarsi di impostare GracePeriodWithDataLossHours su un numero sufficientemente elevato, ad esempio 24 ore.

  Subito dopo l'avvio del failover si verificherà l'aggiornamento DNS del listener di lettura/scrittura. Questa operazione non comporta la perdita di dati. Tuttavia, il processo di scambio dei ruoli del database può richiedere fino a 5 minuti in condizioni normali. Durante il processo alcuni database nella nuova istanza primaria rimarranno di sola lettura. If failover is initiated using PowerShell, the entire operation is synchronous. If it is initiated using the Azure portal, the UI will indicate completion status. Se viene avviato mediante l'API REST, usare il meccanismo di polling standard di Azure Resource Manager per il monitoraggio del completamento.

  > [!IMPORTANT]
  > Usare il failover manuale dei gruppi per ripristinare i database primari nella posizione originale. Quando viene risolta l'interruzione del servizio che ha determinato il failover, sarà possibile spostare i database primari nel percorso originale. A tale scopo è necessario avviare il failover manuale del gruppo.

- **Acknowledge known limitations of failover groups**

  Database rename is not supported for instances in failover group. You will need to temporarily delete failover group to be able to rename a database.

## <a name="failover-groups-and-network-security"></a>Gruppi di failover e sicurezza di rete

For some applications the security rules require that the network access to the data tier is restricted to a specific component or components such as a VM, web service etc. This requirement presents some challenges for business continuity design and the use of the failover groups. Consider the following options when implementing such restricted access.

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
2. [Creare un servizio di bilanciamento del carico pubblico](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) e assegnare l'indirizzo IP pubblico a tale servizio.
3. [Creare una rete virtuale e le macchine virtuali](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) per i componenti front-end.
4. [Creare un gruppo di sicurezza di rete](../virtual-network/security-overview.md) e configurare le connessioni in ingresso.
5. Assicurarsi che le connessioni in uscita siano aperte al database SQL di Azure usando il [tag di servizio](../virtual-network/security-overview.md#service-tags) 'Sql'.
6. Creare un [regola del firewall del database SQL](sql-database-firewall-configure.md) per consentire il traffico in ingresso dall'indirizzo IP pubblico creato nel passaggio 1.

Per altre informazioni su come configurare l'accesso in uscita e su quale indirizzo IP usare nelle regole del firewall, vedere [Connessioni in uscita del servizio di bilanciamento del carico](../load-balancer/load-balancer-outbound-connections.md).

La configurazione appena illustrata garantisce che il failover automatico non blocchi le connessioni tra i componenti front-end e presuppone che l'applicazione riesca a tollerare una maggiore latenza tra il front-end e il livello dati.

> [!IMPORTANT]
> Per garantire la continuità aziendale nel caso di interruzioni del servizio a livello di area è necessario verificare la ridondanza geografica sia per i componenti front-end che per i database.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Enabling geo-replication between managed instances and their VNets

When you set up a failover group between primary and secondary managed instances in two different regions, each instance is isolated using an independent virtual network. To allow replication traffic between these VNets ensure these prerequisites are met:

1. The two managed instances need to be in different Azure regions.
1. The two managed instances need to be the same service tier, and have the same storage size.
1. Your secondary managed instance must be empty (no user databases).
1. The virtual networks used by the managed instances need to be connected through a [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) or Express Route. When two virtual networks connect through an on-premises network, ensure there is no firewall rule blocking ports 5022, and 11000-11999. Il peering di reti virtuali globale non è supportato.
1. The two managed instance VNets cannot have overlapping IP addresses.
1. You need to set up your Network Security Groups (NSG) such that ports 5022 and the range 11000~12000 are open inbound and outbound for connections from the other managed instanced subnet. per consentire il traffico di replica tra le istanze

   > [!IMPORTANT]
   > Regole di sicurezza dei gruppi di sicurezza di rete non configurate correttamente determinano il blocco delle operazioni di copia del database.

1. The secondary instance is configured with the correct DNS zone ID. DNS zone is a property of a managed instance and virtual cluster, and its ID is included in the host name address. The zone ID is generated as a random string when the first managed instance is created in each VNet and the same ID is assigned to all other instances in the same subnet. Once assigned, the DNS zone cannot be modified. Managed instances included in the same failover group must share the DNS zone. You accomplish this by passing the primary instance's zone ID as the value of DnsZonePartner parameter when creating the secondary instance. 

   > [!NOTE]
   > For a detailed tutorial on configuring failover groups with managed instance, see [add a managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Aggiornamento o downgrade di un database primario

È possibile eseguire l'aggiornamento o il downgrade di un database primario a dimensioni di calcolo diverse (entro lo stesso livello di servizio e non tra il livello per utilizzo generico e business critical) senza disconnettere eventuali database secondari. When upgrading, we recommend that you upgrade all of the secondary databases first, and then upgrade the primary. When downgrading, reverse the order: downgrade the primary first, and then downgrade all of the secondary databases. Quando si aggiorna o si effettua il downgrade del database a un livello di servizio diverso, viene applicata questa raccomandazione.

This sequence is recommended specifically to avoid the problem where the secondary at a lower SKU gets overloaded and must be re-seeded during an upgrade or downgrade process. You could also avoid the problem by making the primary read-only, at the expense of impacting all read-write workloads against the primary.

> [!NOTE]
> Se è stato creato il database secondario come parte della configurazione del gruppo di failover, non è consigliabile eseguire il downgrade del database secondario. In questo modo si garantisce che il livello dei dati abbia una capacità sufficiente per elaborare il carico di lavoro normale dopo che il failover viene attivato.

## <a name="preventing-the-loss-of-critical-data"></a>Evitare la perdita di dati critici

A causa della latenza elevata delle reti WAN, per la copia continua viene usato un meccanismo di replica asincrona. La replica asincrona rende inevitabile una perdita parziale dei dati nel caso si verifichi un problema. Tuttavia, alcune applicazione potrebbero non essere soggette alla perdita dei dati. Per proteggere questi aggiornamenti critici, uno sviluppatore di applicazioni può chiamare la procedura di sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) subito dopo il commit della transazione. Calling `sp_wait_for_database_copy_sync` blocks the calling thread until the last committed transaction has been transmitted to the secondary database. Tuttavia, non attende che le transazioni trasmesse vengano riprodotta e che ne venga eseguito il commit nel database secondario. `sp_wait_for_database_copy_sync` is scoped to a specific continuous copy link. La procedura può essere chiamata da qualsiasi utente che abbia diritti di connessione al database primario.

> [!NOTE]
> `sp_wait_for_database_copy_sync` prevents data loss after failover, but does not guarantee full synchronization for read access. The delay caused by a `sp_wait_for_database_copy_sync` procedure call can be significant and depends on the size of the transaction log at the time of the call.

## <a name="failover-groups-and-point-in-time-restore"></a>Gruppi di failover e ripristino temporizzato

Per informazioni sull'uso del ripristino temporizzato con gruppi di failover, vedere [Point in Time Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore) (Recupero temporizzato).

## <a name="programmatically-managing-failover-groups"></a>Gestione di gruppi di failover a livello di codice

Come indicato in precedenza, i gruppi di failover automatico e la replica geografica attiva possono essere gestiti a livello di codice usando Azure PowerShell e l'API REST. Le tabelle seguenti descrivono il set di comandi disponibili. La replica geografica attiva include un set di API di Azure Resource Manager per la gestione, compresa l'[API REST del Database SQL di Azure](https://docs.microsoft.com/rest/api/sql/) e i [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Queste API richiedono l'uso di gruppi di risorse e supportano la sicurezza basata sui ruoli (Controllo degli accessi in base al ruolo). Per altre informazioni su come implementare i ruoli di accesso, vedere [Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Gestire il failover del database SQL con database singoli e pool elastici

| Cmdlet | Description |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Questo comando crea un gruppo di failover e lo registra nei server primario e secondario|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Rimuove il gruppo di failover dal server ed elimina tutti i database secondari incluso il gruppo |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Recupera la configurazione del gruppo di failover |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modifica la configurazione del gruppo di failover |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Attiva il failover del gruppo di failover per il server secondario |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Aggiunge uno o più database a un gruppo di failover del database SQL di Azure|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Manage SQL database failover groups with managed instances

| Cmdlet | Description |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Questo comando crea un gruppo di failover e lo registra nei server primario e secondario|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modifica la configurazione del gruppo di failover|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Recupera la configurazione del gruppo di failover|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Attiva il failover del gruppo di failover per il server secondario|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Rimuove un gruppo di failover|

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Gestire il failover del database SQL con database singoli e pool elastici

| Comando | Description |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Questo comando crea un gruppo di failover e lo registra nei server primario e secondario|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Rimuove il gruppo di failover dal server ed elimina tutti i database secondari incluso il gruppo |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Recupera la configurazione del gruppo di failover |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Modifies the configuration of the failover group and/or adds one or more databases to an Azure SQL Database failover group|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Attiva il failover del gruppo di failover per il server secondario |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Manage SQL database failover groups with managed instances

| Comando | Description |
| --- | --- |
| [az sql instance-failover-group create](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | Questo comando crea un gruppo di failover e lo registra nei server primario e secondario|
| [az sql instance-failover-group update](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | Modifica la configurazione del gruppo di failover|
| [az sql instance-failover-group show](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Recupera la configurazione del gruppo di failover|
| [az sql instance-failover-group set-primary](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Attiva il failover del gruppo di failover per il server secondario|
| [az sql instance-failover-group delete](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Rimuove un gruppo di failover |

* * *

> [!IMPORTANT]
> Per uno script di esempio, vedere [Configure and failover a failover group for a single database](scripts/sql-database-add-single-db-to-failover-group-powershell.md) (Configurare ed eseguire il failover di un gruppo di failover per un singolo database).

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: Manage SQL database failover groups with single and pooled databases

| API SmartBear Ready! | Description |
| --- | --- |
| [Create or Update Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Crea o aggiorna un gruppo di failover. |
| [Delete Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Rimuove il gruppo di failover dal server |
| [Failover (Planned)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Viene eseguito il failover dal server primario corrente a questo server. |
| [Force Failover Allow Data Loss](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |Viene eseguito il failover dal server primario corrente a questo server. Questa operazione potrebbe comportare la perdita di dati. |
| [Get Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Crea un gruppo di failover. |
| [List Failover Groups By Server](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Elenca i gruppi di failover in un server. |
| [Update Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Aggiorna un gruppo di failover. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST API: Manage failover groups with Managed Instances

| API SmartBear Ready! | Description |
| --- | --- |
| [Create or Update Failover Group](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Crea o aggiorna un gruppo di failover. |
| [Delete Failover Group](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Rimuove il gruppo di failover dal server |
| [Failover (Planned)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Viene eseguito il failover dal server primario corrente a questo server. |
| [Force Failover Allow Data Loss](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |Viene eseguito il failover dal server primario corrente a questo server. Questa operazione potrebbe comportare la perdita di dati. |
| [Get Failover Group](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Crea un gruppo di failover. |
| [List Failover Groups - List By Location](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Elenca i gruppi di failover in una posizione. |

## <a name="next-steps"></a>Passaggi successivi

- For detailed tutorials, see
    - [Add single database to a failover group](sql-database-single-database-failover-group-tutorial.md)
    - [Add elastic pool to a failover group](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Add a managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md)
- Per script di esempio, vedere:
  - [Use PowerShell to configure active geo-replication for a single database in Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Use PowerShell to configure active geo-replication for a pooled database in Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Use PowerShell to add an Azure SQL Database single database to a failover group](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
- Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).
- Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md).
- Per ulteriori informazioni sui requisiti di autenticazione per un nuovo database e server primario, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).
