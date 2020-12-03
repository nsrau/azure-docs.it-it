---
title: Che cos'è sincronizzazione dati SQL per Azure?
description: Questa panoramica introduce sincronizzazione dati SQL per Azure, che consente di sincronizzare i dati tra più database cloud e locali.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1, fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/20/2019
ms.openlocfilehash: b23b5a81fdff8a05742092f517128e08723103fc
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531140"
---
# <a name="what-is-sql-data-sync-for-azure"></a>Che cos'è sincronizzazione dati SQL per Azure?

Sincronizzazione dati SQL è un servizio basato sul database SQL di Azure che consente di sincronizzare i dati selezionati in modo bidirezionale tra più database, sia in locale che nel cloud. 

> [!IMPORTANT]
> Azure sincronizzazione dati SQL attualmente non supporta Istanza gestita SQL di Azure.


## <a name="overview"></a>Panoramica 

La sincronizzazione dei dati si basa sul concetto di gruppo di sincronizzazione. Un gruppo di sincronizzazione è un gruppo di database che si desidera sincronizzare.

La sincronizzazione dati usa una topologia hub-spoke per sincronizzare i dati. Si definisce uno dei database nel gruppo di sincronizzazione come database hub. Il resto dei database sono database membri. La sincronizzazione avviene solo tra l'hub e i singoli membri.

- Il **database hub** deve essere un database SQL di Azure.
- I database **membro** possono essere database nel database SQL di Azure o in istanze di SQL Server.
- Il **database dei metadati di sincronizzazione** contiene i metadati e il log per la sincronizzazione dei dati. Il database dei metadati di sincronizzazione deve essere un database SQL di Azure che si trova nella stessa area del database hub. Il database dei metadati di sincronizzazione è creato dal cliente e di proprietà del cliente. È possibile avere un solo database dei metadati di sincronizzazione per area e sottoscrizione. Non è possibile eliminare o rinominare il database dei metadati di sincronizzazione mentre esistono gruppi di sincronizzazione o agenti di sincronizzazione. Microsoft consiglia di creare un nuovo database vuoto da usare come Database dei metadati di sincronizzazione. La sincronizzazione dati crea tabelle in questo database ed esegue un carico di lavoro frequente.

> [!NOTE]
> Se si usa un database locale come database membro, è necessario [installare e configurare un agente di sincronizzazione locale](sql-data-sync-sql-server-configure.md#add-on-prem).

![Sincronizzare i dati tra database](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

Un gruppo di sincronizzazione presenta le proprietà seguenti:

- Lo **schema di sincronizzazione** descrive i dati da sincronizzare.
- La **direzione di sincronizzazione** può essere bidirezionale o unidirezionale. Ovvero, la direzione di sincronizzazione può essere *dall'hub al membro* o *dal membro all'hub* oppure entrambe.
- L'**intervallo di sincronizzazione** descrive la frequenza con cui viene eseguita la sincronizzazione.
- I **criteri di risoluzione dei conflitti** sono criteri a livello di gruppo e le impostazioni possono essere *Priorità hub* o *Priorità client*.

## <a name="when-to-use"></a>Utilizzo

La sincronizzazione dei dati è utile nei casi in cui i dati devono essere mantenuti aggiornati in diversi database nel database SQL di Azure o in SQL Server. Questi sono i principali casi d'uso per la sincronizzazione dati:

- **Sincronizzazione dei dati ibrida:** Con la sincronizzazione dati è possibile garantire la sincronizzazione dei dati tra i database in SQL Server e il database SQL di Azure per abilitare le applicazioni ibride. Questa funzionalità può essere interessante per i clienti che stanno valutando il passaggio al cloud e vorrebbero trasferire alcune applicazioni in Azure.
- **Applicazioni distribuite:** in molti casi è vantaggioso separare carichi di lavoro diversi in database differenti. Ad esempio, se si dispone di un database di produzione di grandi dimensioni, ma è anche necessario eseguire un carico di lavoro di report o analisi su tali dati, può essere utile avere un secondo database per questo carico di lavoro aggiuntivo. Questo approccio riduce al minimo l'impatto a livello di prestazioni sul carico di lavoro di produzione. È possibile usare la sincronizzazione dati per mantenere sincronizzati i due database.
- **Applicazioni distribuite a livello globale:** Molte aziende si estendono in diverse aree geografiche e anche in diversi paesi. Per ridurre al minimo la latenza di rete, è consigliabile posizionare i dati in un'area vicina. Con sincronizzazione dati è possibile mantenere facilmente sincronizzati i database in aree in tutto il mondo.

La sincronizzazione dati non è la soluzione preferita per gli scenari seguenti:

| Scenario | Alcune soluzioni raccomandate |
|----------|----------------------------|
| Ripristino di emergenza | [Backup con ridondanza geografica di Azure](automated-backups-overview.md) |
| Scalabilità in lettura | [Usare le repliche di sola lettura per bilanciare il carico dei carichi di lavoro di query di sola lettura (anteprima)](read-scale-out.md) |
| ETL (da OLTP a OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) o [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services) |
| Migrazione da SQL Server al database SQL di Azure. Tuttavia, sincronizzazione dati SQL possibile utilizzare una volta completata la migrazione, per garantire che l'origine e la destinazione siano mantenute sincronizzate.  | [Servizio Migrazione del database di Azure](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-it-works"></a>Come funziona

- **Rilevamento delle modifiche ai dati:** sincronizzazione dati tiene traccia delle modifiche tramite trigger di inserimento, aggiornamento ed eliminazione. Le modifiche vengono registrate in una tabella laterale nel database utente. Si noti che BULK INSERT non attiva i trigger per impostazione predefinita. Se FIRE_TRIGGERS non è specificato, non viene eseguito alcun trigger di inserimento. Aggiungere l'opzione FIRE_TRIGGERS in modo che la sincronizzazione dei dati possa tenere traccia di tali inserimenti. 
- **Sincronizzazione dei dati:** La sincronizzazione dei dati è progettata in un modello hub e spoke. L'hub viene sincronizzato con ogni membro singolarmente. Le modifiche dall'hub vengono scaricate nel membro e le modifiche apportate dal membro vengono caricate nell'hub.
- **Risoluzione dei conflitti:** sincronizzazione dati offre due opzioni per la risoluzione dei conflitti, ovvero *Priorità hub* o *Priorità client*.
  - Se si seleziona *Priorità hub*, le modifiche nell'hub sovrascrivono sempre le modifiche nel membro.
  - Se si seleziona *Priorità client*, le modifiche nel membro sovrascrivono sempre le modifiche nell'hub. In presenza di più di un membro, il valore finale dipende dal membro sincronizzato per primo.

## <a name="compare-with-transactional-replication"></a>Confronto con la replica transazionale

| | Sincronizzazione dei dati | Replica transazionale |
|---|---|---|
| **Vantaggi** | - Supporto attivo/attivo<br/>- Bidirezionale tra database locali e database SQL di Azure | - Latenza inferiore<br/>- Coerenza delle transazioni<br/>- Riutilizzo topologia esistente dopo la migrazione <br/>-Supporto per Istanza gestita SQL di Azure |
| **Svantaggi** | - Nessuna coerenza delle transazioni<br/>- Maggiore impatto sulle prestazioni | -Non è possibile pubblicare dal database SQL di Azure <br/>-    Alti costi di manutenzione |

## <a name="get-started"></a>Introduzione 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Configurare la sincronizzazione dati nel portale di Azure

- [Configurare la sincronizzazione dati SQL](sql-data-sync-sql-server-configure.md)
- Agente di sincronizzazione dei dati: [Agente di sincronizzazione dei dati per la sincronizzazione dati SQL di Azure](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>Configurare la sincronizzazione dati con PowerShell

- [Usare PowerShell per sincronizzare più database nel database SQL di Azure](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [Usare PowerShell per la sincronizzazione tra un database nel database SQL di Azure e un database in un'istanza di SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Rivedere le procedure consigliate per la sincronizzazione dati

- [Procedure consigliate per la sincronizzazione dati SQL di Azure](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Nel caso in cui si siano verificati problemi

- [Risolvere i problemi della sincronizzazione dati SQL di Azure](./sql-data-sync-troubleshoot.md)

## <a name="consistency-and-performance"></a>Coerenza e prestazioni

### <a name="eventual-consistency"></a>Coerenza finale

Poiché la sincronizzazione dei dati è basata su trigger, la coerenza transazionale non è garantita. Microsoft garantisce che tutte le modifiche vengano apportate alla fine e che la sincronizzazione dei dati non provochi la perdita di dati.

### <a name="performance-impact"></a>Impatto sulle prestazioni

La sincronizzazione dati usa trigger di inserimento, aggiornamento ed eliminazione per il rilevamento delle modifiche e crea tabelle laterali nel database utente per il rilevamento delle modifiche. Queste attività di rilevamento delle modifiche hanno un impatto sul carico di lavoro del database. Valutare il livello di servizio e aggiornare se necessario.

Sulle prestazioni del database possono incidere anche il provisioning e il deprovisioning eseguiti durante la creazione, l'aggiornamento e l'eliminazione dei gruppi di sincronizzazione.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Requisiti e limitazioni

### <a name="general-requirements"></a>Requisiti generali

- Ogni tabella deve avere una chiave primaria. Non modificare il valore della chiave primaria in alcuna riga. Se è necessario modificare un valore della chiave primaria, eliminare la riga e ricrearla con il nuovo valore della chiave primaria.

> [!IMPORTANT]
> La modifica del valore di una chiave primaria esistente provocherà il comportamento difettoso seguente:
> - I dati tra l'hub e il membro possono essere persi anche se la sincronizzazione non segnala alcun problema.
> - La sincronizzazione può avere esito negativo perché la tabella di rilevamento include una riga non esistente dall'origine a causa della modifica della chiave primaria.

- L'isolamento dello snapshot deve essere abilitato sia per i membri della sincronizzazione che per l'hub. Per altre informazioni, vedere [Isolamento dello snapshot in SQL Server](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Limitazioni generali

- Una tabella non può contenere una colonna Identity che non è la chiave primaria.
- Per utilizzare la sincronizzazione dati, una tabella deve disporre di un indice cluster.
- Una chiave primaria non può contenere i tipi di dati seguenti: sql_variant, Binary, varbinary, image, XML.
- Usare i tipi di dati che seguono come chiave primaria con la massima cautela, perché supportano solo la precisione al secondo: time, datetime, datetime2, datetimeoffset.
- I nomi degli oggetti (database, tabelle e colonne) non possono contenere i caratteri stampabili (.), parentesi quadra aperta ([) o parentesi quadra chiusa (]).
- Un nome di tabella non può contenere caratteri stampabili:! "# $%' () * +-spazio
- L'autenticazione Azure Active Directory non è supportata.
- Se sono presenti tabelle con lo stesso nome ma con uno schema diverso (ad esempio, dbo. Customers e Sales. Customers), è possibile aggiungere in Sync solo una delle tabelle.
- Le colonne con tipi di dati User-Defined non sono supportate
- Lo stato di trasferimento di server tra sottoscrizioni diverse non è supportato. 

#### <a name="unsupported-data-types"></a>Tipi di dati non supportati

- FileStream
- Tipo definito dall'utente (UDT) SQL/CLR
- XMLSchemaCollection (supportato da XML)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Tipi di colonna non supportati

La sincronizzazione dati non sincronizza le colonne di sola lettura o generate dal sistema. Ad esempio:

- Colonne calcolate.
- Colonne generate dal sistema per le tabelle temporali.

#### <a name="limitations-on-service-and-database-dimensions"></a>Limitazioni alle dimensioni del servizio e del database

| **Dimensioni**                                                  | **Limite**              | **Soluzione alternativa**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Numero massimo di gruppi di sincronizzazione a cui può appartenere qualsiasi database.       | 5                      |                             |
| Numero massimo di endpoint in un singolo gruppo di sincronizzazione              | 30                     |                             |
| Numero massimo di endpoint locali in un singolo gruppo di sincronizzazione. | 5                      | Creare più gruppi di sincronizzazione |
| Nomi di database, tabella, schema e colonna                       | 50 caratteri per nome |                             |
| Tabelle in un gruppo di sincronizzazione                                          | 500                    | Creare più gruppi di sincronizzazione |
| Colonne in una tabella in un gruppo di sincronizzazione                              | 1000                   |                             |
| Dimensioni delle righe di dati in una tabella                                        | 24 MB                  |                             |

> [!NOTE]
> Se è presente un unico gruppo di sincronizzazione, questo può contenere fino a 30 endpoint. Se esistono più gruppi di sincronizzazione, il numero totale di endpoint in tutti i gruppi di sincronizzazione non può essere maggiore di 30. Se un database appartiene a più gruppi di sincronizzazione, vale come più endpoint.

### <a name="network-requirements"></a>Requisiti di rete

Quando viene stabilito il gruppo di sincronizzazione, il servizio di sincronizzazione dati deve connettersi al database hub. Quando si stabilisce il gruppo di sincronizzazione, nel server SQL di Azure deve essere presente la configurazione seguente nelle `Firewalls and virtual networks` Impostazioni:

 * *Negare l'accesso alla rete pubblica* deve essere impostato su *disattivato*.
 * *Consentire ai servizi e alle risorse di Azure di accedere a questo server* deve essere impostato su *Sì* oppure è necessario creare regole IP per gli [indirizzi IP utilizzati dal servizio di sincronizzazione dati](network-access-controls-overview.md#data-sync).

Dopo la creazione e il provisioning del gruppo di sincronizzazione, è possibile disabilitare queste impostazioni. L'agente di sincronizzazione si connetterà direttamente al database hub ed è possibile usare le [regole IP del firewall](firewall-configure.md) del server o gli [endpoint privati](private-endpoint-overview.md) per consentire all'agente di accedere al server Hub.

> [!NOTE]
> Se si modificano le impostazioni dello schema del gruppo di sincronizzazione, sarà necessario consentire al servizio di sincronizzazione dati di accedere nuovamente al server in modo che sia possibile eseguire di nuovo il provisioning del database hub.

## <a name="faq-about-sql-data-sync"></a>Domande frequenti sulla sincronizzazione dati SQL

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Quanto costa il servizio di sincronizzazione dati SQL?

Non sono previsti addebiti per il servizio sincronizzazione dati SQL stesso. Tuttavia, gli addebiti per il trasferimento dei dati vengono comunque raccolti per lo spostamento dei dati all'interno e all'esterno dell'istanza del database SQL. Per altre informazioni, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Quali aree supportano la sincronizzazione dei dati?

La sincronizzazione dati SQL è disponibile in tutte le aree geografiche.

### <a name="is-a-sql-database-account-required"></a>È necessario disporre di un account di database SQL?

Sì. È necessario disporre di un account del database SQL per ospitare il database hub.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>È possibile usare la sincronizzazione dei dati per sincronizzare solo i database SQL Server

Non direttamente. È possibile eseguire la sincronizzazione tra SQL Server database indirettamente, tuttavia, creando un database hub in Azure e quindi aggiungendo i database locali al gruppo di sincronizzazione.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>È possibile usare la sincronizzazione dei dati per la sincronizzazione tra database nel database SQL che appartengono a sottoscrizioni diverse

Sì. È possibile eseguire la sincronizzazione tra database appartenenti a gruppi di risorse appartenenti a sottoscrizioni diverse.

- Se le sottoscrizioni appartengono allo stesso tenant e sono disponibili le autorizzazioni per tutte le sottoscrizioni, è possibile configurare il gruppo di sincronizzazione nel portale di Azure.
- In caso contrario, è necessario usare PowerShell per aggiungere i membri di sincronizzazione che appartengono a sottoscrizioni diverse.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>È possibile usare la sincronizzazione dei dati per sincronizzare i database nel database SQL che appartengono a cloud diversi, ad esempio il cloud pubblico di Azure e Azure Cina 21Vianet

Sì. È possibile eseguire la sincronizzazione tra database appartenenti a cloud diversi. È necessario usare PowerShell per aggiungere i membri di sincronizzazione che appartengono a sottoscrizioni diverse.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>È possibile usare la sincronizzazione dei dati per effettuare il seeding dei dati da un database di produzione a un database vuoto e quindi sincronizzarli?

Sì. Creare manualmente lo schema nel nuovo database effettuando lo scripting dall'originale. Dopo aver creato lo schema, aggiungere le tabelle a un gruppo di sincronizzazione per copiare i dati e mantenerli sincronizzati.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>È opportuno usare la sincronizzazione dei dati SQL per effettuare il backup e ripristino dei database?

Non è consigliabile usare sincronizzazione dati SQL per creare un backup dei dati. Non è possibile eseguire il backup e il ripristino a un momento specifico perché le sincronizzazioni di sincronizzazione dati SQL non sono sottoposte a controllo delle versioni. Inoltre, sincronizzazione dati SQL non esegue il backup di altri oggetti SQL, ad esempio le stored procedure, e non esegue rapidamente l'equivalente di un'operazione di ripristino.

Per una tecnica di backup consigliata, vedere [copiare un database nel database SQL di Azure](database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Con la sincronizzazione dei dati si possono sincronizzare tabelle e colonne crittografate?

- Se un database usa Always Encrypted, è possibile sincronizzare solo le tabelle e colonne *non* crittografate. Non è possibile sincronizzare le colonne crittografate, perché la sincronizzazione dei dati non può decrittografare i dati.
- Se una colonna usa la crittografia a livello di colonna (CLE), è possibile sincronizzare la colonna, purché le dimensioni delle righe siano minori rispetto alle dimensioni massime di 24 MB. La sincronizzazione dei dati considera la colonna crittografata in base alla chiave (CLE) come normali dati binari. Per decrittografare i dati in altri membri di sincronizzazione, è necessario avere lo stesso certificato.

### <a name="is-collation-supported-in-sql-data-sync"></a>Le regole di confronto sono supportate nella sincronizzazione dei dati SQL?

Sì. La sincronizzazione dati SQL supporta le regole di confronto negli scenari seguenti:

- Se le tabelle dello schema di sincronizzazione selezionate non sono già presenti nei database dell'hub o del membro, quando si distribuisce il gruppo di sincronizzazione, il servizio crea automaticamente le tabelle e le colonne corrispondenti con le impostazioni delle regole di confronto selezionate nei database di destinazione vuoti.
- Se le tabelle da sincronizzare sono già presenti sia nel database hub che nel database membro, la sincronizzazione dati SQL richiede che le colonne chiavi primarie abbiano le stesse regole di confronto in entrambi i database hub e membro per una distribuzione corretta del gruppo di sincronizzazione. Per le colonne diverse dalle colonne chiavi primarie non sono previste restrizioni relative alle regole di confronto.

### <a name="is-federation-supported-in-sql-data-sync"></a>La federazione è supportata nella sincronizzazione dei dati SQL?

Il database radice di federazione può essere usato nel servizio di sincronizzazione dati SQL senza alcuna limitazione. Non è possibile aggiungere l'endpoint del database federato alla versione corrente di sincronizzazione dati SQL.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>È possibile usare la sincronizzazione dei dati per sincronizzare i dati esportati da Dynamics 365 usando la funzionalità Bring your own database (BYOD)?

La funzionalità Bring your own database di Dynamics 365 consente agli amministratori di esportare le entità di dati dall'applicazione nel proprio database SQL Microsoft Azure. La sincronizzazione dei dati può essere usata per sincronizzare questi dati in altri database se i dati vengono esportati con **push incrementale** (il push completo non è supportato) e **Abilita trigger nel database di destinazione** è impostata su **Sì**.

## <a name="next-steps"></a>Passaggi successivi

### <a name="update-the-schema-of-a-synced-database"></a>Aggiornare lo schema di un database sincronizzato

È necessario aggiornare lo schema di un database in un gruppo di sincronizzazione? Le modifiche dello schema non vengono replicate automaticamente. Per alcune soluzioni, vedere gli articoli seguenti:

- [Automatizzare la replica delle modifiche dello schema con sincronizzazione dati SQL in Azure](./sql-data-sync-update-sync-schema.md)
- [Usare PowerShell per aggiornare lo schema di sincronizzazione in un gruppo di sincronizzazione esistente](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Monitoraggio e risoluzione dei problemi

Il sincronizzazione dati SQL è come previsto? Per monitorare l'attività e risolvere i problemi, vedere gli articoli seguenti:

- [Monitorare la sincronizzazione dati SQL con i log di Monitoraggio di Azure](./monitor-tune-overview.md)
- [Risolvere i problemi della sincronizzazione dati SQL di Azure](./sql-data-sync-troubleshoot.md)

### <a name="learn-more-about-azure-sql-database"></a>Ulteriori informazioni sul database SQL di Azure

Per altre informazioni sul database SQL di Azure, vedere gli articoli seguenti:

- [Panoramica del database SQL](sql-database-paas-overview.md)
- [Gestione del ciclo di vita del database](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
