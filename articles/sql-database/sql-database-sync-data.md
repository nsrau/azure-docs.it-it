---
title: Sincronizzazione dati SQL di Azure | Microsoft Docs
description: Questa panoramica è un'introduzione alla sincronizzazione dati SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 08/09/2018
ms.openlocfilehash: a287f985ce015ac6b886f4e5c2b86d6b3793e7d5
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721836"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL

La sincronizzazione dati SQL è un servizio basato sul database SQL di Azure che consente di sincronizzare i dati selezionati bidirezionalmente tra più database SQL e istanze di SQL Server.

> [!IMPORTANT]
> Al momento, la sincronizzazione dati SQL di Azure **non** supporta Istanza gestita di database SQL di Azure.

## <a name="architecture-of-sql-data-sync"></a>Architettura della sincronizzazione dati SQL

La sincronizzazione dati si basa sul concetto di gruppo di sincronizzazione. Un gruppo di sincronizzazione è un gruppo di database che si vuole sincronizzare.

La sincronizzazione dati usa una topologia hub-spoke per sincronizzare i dati. Uno dei database nel gruppo di sincronizzazione viene definito come database hub. Il resto dei database sono database membri. La sincronizzazione si verifica solo tra l'hub e i singoli membri.
-   Il **database hub** deve essere un database SQL di Azure.
-   I **database membri** possono essere database SQL, database di SQL Server locali o istanze di SQL Server in macchine virtuali di Azure.
-   Il **database di sincronizzazione** contiene i metadati e il log per la sincronizzazione dati. Il database di sincronizzazione deve essere un database SQL di Azure posizionato nella stessa area del database hub. Il database di sincronizzazione viene creato dal cliente ed è di sua proprietà.

> [!NOTE]
> Se si usa un database locale come database membro, è necessario [installare e configurare un agente di sincronizzazione locale](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Sincronizzare i dati tra database](media/sql-database-sync-data/sync-data-overview.png)

Di seguito sono elencate le proprietà di un gruppo di sincronizzazione:

-   Lo **schema di sincronizzazione** descrive i dati da sincronizzare.

-   La **direzione di sincronizzazione** può essere bidirezionale o unidirezionale. Ovvero, la direzione di sincronizzazione può essere *dall'hub al membro* o *dal membro all'hub* oppure entrambe.

-   L'**intervallo di sincronizzazione** descrive la frequenza con cui viene eseguita la sincronizzazione.

-   I **criteri di risoluzione dei conflitti** sono criteri a livello di gruppo e le impostazioni possono essere *Priorità hub* o *Priorità client*.

## <a name="when-to-use-data-sync"></a>Quando usare la sincronizzazione dati

La sincronizzazione dei dati è utile nei casi in cui i dati devono essere mantenuti aggiornati in diversi database SQL di Azure o SQL Server. Questi sono i principali casi d'uso per la sincronizzazione dati:

-   **Sincronizzazione dei dati ibrida:** con la sincronizzazione dei dati è possibile mantenere sincronizzati i dati tra i database locali e i database SQL di Azure per rendere possibili applicazioni ibride. Questa funzionalità può essere interessante per i clienti che stanno valutando il passaggio al cloud e vorrebbero trasferire alcune applicazioni in Azure.

-   **Applicazioni distribuite:** in molti casi è utile separare carichi di lavoro diversi in database differenti. Ad esempio, se si dispone di un database di produzione di grandi dimensioni, ma è anche necessario eseguire un carico di lavoro di report o analisi su tali dati, può essere utile avere un secondo database per questo carico di lavoro aggiuntivo. Questo approccio riduce al minimo l'impatto a livello di prestazioni sul carico di lavoro di produzione. È possibile usare la sincronizzazione dati per mantenere sincronizzati i due database.

-   **Applicazioni distribuite a livello globale:** molte aziende sono estese a più aree, a volte anche in paesi diversi. Per ridurre al minimo la latenza di rete, è consigliabile posizionare i dati in un'area vicina. Con sincronizzazione dati è possibile mantenere facilmente sincronizzati i database in aree in tutto il mondo.

La sincronizzazione dei dati non è la soluzione preferita per gli scenari seguenti:

| Scenario | Alcune soluzioni raccomandate |
|----------|----------------------------|
| Ripristino di emergenza | [Backup con ridondanza geografica di Azure](sql-database-automated-backups.md) |
| Scalabilità in lettura | [Usare le repliche di sola lettura per bilanciare il carico dei carichi di lavoro di query di sola lettura (anteprima)](sql-database-read-scale-out.md) |
| ETL (da OLTP a OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) o [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services?view=sql-server-2017) |
| Migrazione da SQL Server locale al database SQL di Azure | [Servizio Migrazione del database di Azure](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-does-data-sync-work"></a>Come funziona la sincronizzazione dati? 

-   **Rilevamento delle modifiche ai dati:** la sincronizzazione dei dati tiene traccia delle modifiche tramite trigger di inserimento, aggiornamento ed eliminazione. Le modifiche vengono registrate in una tabella laterale nel database utente. Si noti che BULK INSERT non attiva i trigger per impostazione predefinita. Se FIRE_TRIGGERS non è specificato, non viene eseguito alcun trigger di inserimento. Aggiungere l'opzione FIRE_TRIGGERS in modo che la sincronizzazione dei dati possa tenere traccia di tali inserimenti. 

-   **Sincronizzazione dei dati:** la sincronizzazione dei dati è progettata in base a un modello hub-spoke. L'hub sincronizza singolarmente ogni membro. Le modifiche dall'hub vengono scaricate nel membro e quindi le modifiche dal membro vengono caricate nell'hub.

-   **Risoluzione dei conflitti:** la sincronizzazione dei dati offre due opzioni per la risoluzione dei conflitti, ovvero *Priorità hub* o *Priorità client*.
    -   Se si seleziona *Priorità hub*, le modifiche nell'hub sovrascrivono sempre le modifiche nel membro.
    -   Se si seleziona *Priorità client*, le modifiche nel membro sovrascrivono sempre le modifiche nell'hub. In presenza di più di un membro, il valore finale dipende dal membro sincronizzato per primo.

## <a name="get-started-with-sql-data-sync"></a>Introduzione alla sincronizzazione dati SQL

### <a name="set-up-data-sync-in-the-azure-portal"></a>Configurare la sincronizzazione dati nel portale di Azure

-   [Configurare la sincronizzazione dati SQL](sql-database-get-started-sql-data-sync.md)
-   Agente di sincronizzazione dei dati: [Agente di sincronizzazione dei dati per la sincronizzazione dati SQL di Azure](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Configurare la sincronizzazione dati con PowerShell

-   [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)

-   [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Rivedere le procedure consigliate per la sincronizzazione dati

-   [Procedure consigliate per la sincronizzazione dati SQL di Azure](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Nel caso in cui si siano verificati problemi

-   [Risolvere i problemi della sincronizzazione dati SQL di Azure](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Coerenza e prestazioni

#### <a name="eventual-consistency"></a>Coerenza finale
Dato che la sincronizzazione dati è basata su trigger, la coerenza delle transazioni non è garantita. Microsoft garantisce che tutte le modifiche vengono apportate alla fine e che la sincronizzazione dati non causi perdite di dati.

#### <a name="performance-impact"></a>Impatto sulle prestazioni
La sincronizzazione dati usa trigger di inserimento, aggiornamento ed eliminazione per il rilevamento delle modifiche e crea tabelle laterali nel database utente per il rilevamento delle modifiche. Queste attività di rilevamento delle modifiche hanno un impatto sul carico di lavoro del database. Valutare il livello di servizio e aggiornare se necessario.

Sulle prestazioni del database possono incidere anche il provisioning e il deprovisioning eseguiti durante la creazione, l'aggiornamento e l'eliminazione dei gruppi di sincronizzazione. 

## <a name="sync-req-lim"></a> Requisiti e limitazioni

### <a name="general-requirements"></a>Requisiti generali

-   Ogni tabella deve avere una chiave primaria. Non modificare il valore della chiave primaria in alcuna riga. Se è necessario modificare un valore della chiave primaria, eliminare la riga e ricrearla con il nuovo valore della chiave primaria. 

-   L'isolamento dello snapshot deve essere abilitato. Per altre informazioni, vedere [Isolamento dello snapshot in SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Limitazioni generali

-   Una tabella non può includere colonne Identity che non sono la chiave primaria.

-   Una chiave primaria non può avere i tipi di dati seguenti: sql_variant, binary, varbinary, image, xml. 

-   Usare i tipi di dati che seguono come chiave primaria con la massima cautela, perché supportano solo la precisione al secondo: time, datetime, datetime2, datetimeoffset.

-   I nomi degli oggetti (database, tabelle e colonne) non possono contenere i caratteri stampabili punto (.), parentesi quadra aperta ([) o parentesi quadra chiusa (]).

-   L'autenticazione di Azure Active Directory non è supportata.

-   Le tabelle con lo stesso nome ma con schema diverso (ad esempio, dbo.customers e sales.customers) non sono supportate.

#### <a name="unsupported-data-types"></a>Tipi di dati non supportati

-   FileStream

-   Tipo definito dall'utente (UDT) SQL/CLR

-   XMLSchemaCollection (supportato da XML)

-   Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Tipi di colonna non supportati

La sincronizzazione dati non sincronizza le colonne di sola lettura o generate dal sistema. Ad esempio: 

-   Colonne calcolate.

-   Colonne generate dal sistema per le tabelle temporali.

#### <a name="limitations-on-service-and-database-dimensions"></a>Limitazioni alle dimensioni del servizio e del database

| **Dimensioni**                                                      | **Limite**              | **Soluzione alternativa**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Numero massimo di gruppi di sincronizzazione a cui può appartenere qualsiasi database.       | 5                      |                             |
| Numero massimo di endpoint in un singolo gruppo di sincronizzazione              | 30                     |                             |
| Numero massimo di endpoint locali in un singolo gruppo di sincronizzazione. | 5                      | Creare più gruppi di sincronizzazione |
| Nomi di database, tabella, schema e colonna                       | 50 caratteri per nome |                             |
| Tabelle in un gruppo di sincronizzazione                                          | 500                    | Creare più gruppi di sincronizzazione |
| Colonne in una tabella in un gruppo di sincronizzazione                              | 1000                   |                             |
| Dimensioni delle righe di dati in una tabella                                        | 24 MB                  |                             |
| Intervallo minimo di sincronizzazione                                           | 5 minuti              |                             |
|||
> [!NOTE]
> Se è presente un unico gruppo di sincronizzazione, questo può contenere fino a 30 endpoint. Se esistono più gruppi di sincronizzazione, il numero totale di endpoint in tutti i gruppi di sincronizzazione non può essere maggiore di 30. Se un database appartiene a più gruppi di sincronizzazione, vale come più endpoint.

## <a name="faq-about-sql-data-sync"></a>Domande frequenti sulla sincronizzazione dati SQL

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Quanto costa il servizio di sincronizzazione dati SQL?

Non viene applicato alcun addebito per il servizio di sincronizzazione dati SQL di per sé.  Tuttavia, si accumuleranno ancora gli addebiti per il trasferimento dei dati dovuti allo spostamento dei dati da e verso l'istanza di database SQL. Per altre informazioni, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Quali aree supportano la sincronizzazione dati?

La sincronizzazione dati SQL è disponibile in tutte le aree geografiche.

### <a name="is-a-sql-database-account-required"></a>È necessario disporre di un account di database SQL? 

Sì. È necessario disporre di un account di database SQL per ospitare il database hub.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>È possibile usare la sincronizzazione dati solo tra database SQL Server locali? 
Non direttamente. Tuttavia, è possibile sincronizzare in maniera indiretta tra i database SQL Server locali creando un database hub in Azure e aggiungendo i database locali al gruppo di sincronizzazione.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>È possibile usare la sincronizzazione dei dati tra database SQL che appartengono a sottoscrizioni diverse?
Sì. È possibile eseguire la sincronizzazione tra database SQL che appartengono a gruppi di risorse di proprietà di sottoscrizioni diverse.
-   Se le sottoscrizioni appartengono allo stesso tenant e sono disponibili le autorizzazioni per tutte le sottoscrizioni, è possibile configurare il gruppo di sincronizzazione nel portale di Azure.
-   In caso contrario, è necessario usare PowerShell per aggiungere i membri di sincronizzazione che appartengono a sottoscrizioni diverse.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china"></a>È possibile usare la sincronizzazione dei dati tra database SQL appartenenti a cloud diversi, ad esempio al cloud pubblico di Azure e ad Azure Cina?
Sì. È possibile eseguire la sincronizzazione tra database SQL appartenenti a cloud diversi. Per aggiungere i membri di sincronizzazione appartenenti a sottoscrizioni diverse, è necessario usare PowerShell.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>È possibile usare la sincronizzazione dati per effettuare il seeding dei dati da un database di produzione a un database vuoto e quindi sincronizzarli?

Sì. Creare manualmente lo schema nel nuovo database effettuando lo scripting dall'originale. Dopo aver creato lo schema, aggiungere le tabelle a un gruppo di sincronizzazione per copiare i dati e mantenerli sincronizzati.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>È necessario usare la sincronizzazione dati SQL per eseguire il backup e il ripristino dei database?

Non è consigliabile usare la sincronizzazione dati SQL per creare un backup dei dati. Non è possibile eseguire il backup e il ripristino in un punto specifico nel tempo, perché le sincronizzazioni della sincronizzazione dati SQL sono senza versione. Inoltre, la sincronizzazione dati SQL non esegue il backup di altri oggetti SQL, ad esempio le stored procedure, e non esegue rapidamente l'equivalente di un'operazione di ripristino.

Per una tecnica di backup consigliata, vedere [Copiare un database SQL di Azure](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Con la sincronizzazione dei dati si possono sincronizzare tabelle e colonne crittografate?

-   Se un database usa Always Encrypted, è possibile sincronizzare solo le tabelle e colonne *non* crittografate. Non è possibile sincronizzare le colonne crittografate, perché la sincronizzazione dei dati non può decrittografare i dati.

-   Se una colonna usa la crittografia a livello di colonna (CLE), è possibile sincronizzare la colonna, purché le dimensioni delle righe siano minori rispetto alle dimensioni massime di 24 MB. La sincronizzazione dei dati considera la colonna crittografata in base alla chiave (CLE) come normali dati binari. Per decrittografare i dati in altri membri di sincronizzazione, è necessario avere lo stesso certificato.

### <a name="is-collation-supported-in-sql-data-sync"></a>Le regole di confronto sono supportate nella sincronizzazione dati SQL?

Sì. La sincronizzazione dati SQL supporta le regole di confronto negli scenari seguenti:

-   Se le tabelle dello schema di sincronizzazione selezionate non sono già nel database hub o membro, quando si distribuisce il gruppo di sincronizzazione, il servizio crea automaticamente le tabelle e le colonne corrispondenti con le impostazioni delle regole di confronto selezionate nei database di destinazione vuoti.

-   Se le tabelle da sincronizzare sono già presenti sia nel database hub che nel database membro, la sincronizzazione dati SQL richiede che le colonne chiavi primarie abbiano le stesse regole di confronto in entrambi i database hub e membro per una distribuzione corretta del gruppo di sincronizzazione. Per le colonne diverse dalle colonne chiavi primarie non sono previste restrizioni relative alle regole di confronto.

### <a name="is-federation-supported-in-sql-data-sync"></a>La federazione è supportata nella sincronizzazione dati SQL?

Il database radice di federazione può essere usato nel servizio di sincronizzazione dati SQL senza alcuna limitazione. Non è possibile aggiungere l'endpoint del database federato alla versione corrente della sincronizzazione dati SQL.

## <a name="next-steps"></a>Passaggi successivi

### <a name="update-the-schema-of-a-synced-database"></a>Aggiornare lo schema di un database sincronizzato

È necessario aggiornare lo schema di un database in un gruppo di sincronizzazione? Le modifiche allo schema non vengono replicate automaticamente. Per alcune soluzioni, vedere gli articoli seguenti:

-   [Automatizzare la replica delle modifiche dello schema nella sincronizzazione dati SQL di Azure](sql-database-update-sync-schema.md)

-   [Usare PowerShell per aggiornare lo schema di sincronizzazione in un gruppo di sincronizzazione esistente](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorare e risolvere i problemi

La sincronizzazione dati SQL ha le prestazioni previste? Per monitorare l'attività e risolvere i problemi, vedere gli articoli seguenti:

-   [Monitorare la sincronizzazione dati SQL con Log Analytics](sql-database-sync-monitor-oms.md)

-   [Risolvere i problemi della sincronizzazione dati SQL di Azure](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Ulteriori informazioni sul database SQL di Azure

Per altre informazioni sul database SQL, vedere gli articoli seguenti:

-   [Panoramica del database SQL](sql-database-technical-overview.md)

-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
