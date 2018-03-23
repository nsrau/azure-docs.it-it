---
title: Anteprima di sincronizzazione dati SQL di Azure | Microsoft Docs
description: Questa panoramica è un'introduzione all'anteprima di sincronizzazione dati SQL di Azure.
services: sql-database
author: douglaslms
manager: craigg
ms.service: sql-database
ms.custom: data-sync
ms.topic: article
ms.date: 11/13/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: 5abe3e5a56dfca263f109b86f473ac490da3eac7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync-preview"></a>Sincronizzare i dati tra più database cloud e locali con l'anteprima di sincronizzazione dati SQL

La sincronizzazione dati SQL è un servizio basato sul database SQL di Azure che consente di sincronizzare i dati selezionati bidirezionalmente tra più database SQL e istanze di SQL Server.

La sincronizzazione dati si basa sul concetto di gruppo di sincronizzazione. Un gruppo di sincronizzazione è un gruppo di database che si vuole sincronizzare.

Di seguito sono elencate le proprietà di un gruppo di sincronizzazione:

-   Lo **schema di sincronizzazione** descrive i dati da sincronizzare.

-   La **direzione di sincronizzazione** può essere bidirezionale o unidirezionale. Ovvero, la direzione di sincronizzazione può essere *dall'hub al membro* o *dal membro all'hub* o entrambe.

-   L'**intervallo di sincronizzazione** è la frequenza con cui viene eseguita la sincronizzazione.

-   I **criteri di risoluzione dei conflitti** sono criteri a livello di gruppo e le impostazioni possono essere *Priorità hub* o *Priorità client*.

La sincronizzazione dati usa una topologia hub-spoke per sincronizzare i dati. Uno dei database nel gruppo viene definito come database hub. Il resto dei database sono database membri. La sincronizzazione si verifica solo tra l'hub e i singoli membri.
-   Il **database hub** deve essere un database SQL di Azure.
-   I **database membri** possono essere database SQL, database di SQL Server locali o istanze di SQL Server in macchine virtuali di Azure.
-   Il **database di sincronizzazione** contiene i metadati e il log per la sincronizzazione dati. Il database di sincronizzazione deve essere un database SQL di Azure posizionato nella stessa area del database hub. Il database di sincronizzazione viene creato dal cliente ed è di sua proprietà.

> [!NOTE]
> Se si usa un database locale, è necessario [configurare un agente locale](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Sincronizzare i dati tra database](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>Quando usare la sincronizzazione dati

La sincronizzazione dati è utile nei casi in cui i dati devono essere mantenuti aggiornati in diversi database SQL di Azure o database di SQL Server. Questi sono i principali casi d'uso per la sincronizzazione dati:

-   **Sincronizzazione dei dati ibrida:** con la sincronizzazione dei dati è possibile mantenere i dati sincronizzati tra database locali e database SQL di Azure per rendere possibili applicazioni ibride. Questa funzionalità può essere interessante per i clienti che stanno valutando il passaggio al cloud e vorrebbero trasferire alcune applicazioni in Azure.

-   **Applicazioni distribuite:** in molti casi è vantaggioso separare carichi di lavoro diversi in database differenti. Ad esempio, se si dispone di un database di produzione di grandi dimensioni, ma è anche necessario eseguire un carico di lavoro di report o analisi su tali dati, può essere utile avere un secondo database per questo carico di lavoro aggiuntivo. Questo approccio riduce al minimo l'impatto a livello di prestazioni sul carico di lavoro di produzione. È possibile usare la sincronizzazione dati per mantenere sincronizzati i due database.

-   **Applicazioni distribuite a livello globale:** molte aziende sono estese a più aree, a volte anche in paesi diversi. Per ridurre al minimo la latenza di rete, è consigliabile posizionare i dati in un'area vicina. Con sincronizzazione dati è possibile mantenere facilmente sincronizzati i database in aree in tutto il mondo.

La sincronizzazione dei dati non è appropriata per gli scenari seguenti:

-   Ripristino di emergenza

-   Scalabilità in lettura

-   ETL (da OLTP a OLAP)

-   Migrazione da SQL Server locale al database SQL di Azure

## <a name="how-does-data-sync-work"></a>Come funziona la sincronizzazione dati? 

-   **Rilevamento delle modifiche ai dati:** sincronizzazione dati tiene traccia delle modifiche tramite trigger di inserimento, aggiornamento ed eliminazione. Le modifiche vengono registrate in una tabella laterale nel database utente.

-   **Sincronizzazione dei dati:** il servizio di sincronizzazione dati è progettato in base a un modello hub-spoke. L'hub sincronizza singolarmente ogni membro. Le modifiche dall'hub vengono scaricate nel membro e quindi le modifiche dal membro vengono caricate nell'hub.

-   **Risoluzione dei conflitti:** sincronizzazione dati offre due opzioni per la risoluzione dei conflitti, ovvero *Priorità hub* o *Priorità client*.
    -   Se si seleziona *Priorità hub*, le modifiche nell'hub sovrascrivono sempre le modifiche nel membro.
    -   Se si seleziona *Priorità client*, le modifiche nel membro sovrascrivono sempre le modifiche nell'hub. In presenza di più di un membro, il valore finale dipende dal membro sincronizzato per primo.

## <a name="sync-req-lim"></a> Requisiti e limitazioni

### <a name="general-considerations"></a>Considerazioni generali

#### <a name="eventual-consistency"></a>Coerenza finale
Dato che la sincronizzazione dati è basata su trigger, la coerenza delle transazioni non è garantita. Microsoft garantisce che tutte le modifiche vengono apportate alla fine e che la sincronizzazione dati non causi perdite di dati.

#### <a name="performance-impact"></a>Impatto sulle prestazioni
La sincronizzazione dati usa trigger di inserimento, aggiornamento ed eliminazione per il rilevamento delle modifiche e crea tabelle laterali nel database utente per il rilevamento delle modifiche. Queste attività di rilevamento delle modifiche hanno un impatto sul carico di lavoro del database. Valutare il livello di servizio e aggiornare se necessario.

### <a name="general-requirements"></a>Requisiti generali

-   Ogni tabella deve avere una chiave primaria. Non modificare il valore della chiave primaria in alcuna riga. Se è necessario modificare un valore della chiave primaria, eliminare la riga e ricrearla con il nuovo valore della chiave primaria. 

-   L'isolamento dello snapshot deve essere abilitato. Per altre informazioni, vedere [Isolamento dello snapshot in SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Limitazioni generali

-   Una tabella non può includere colonne Identity che non sono la chiave primaria.

-   I nomi degli oggetti (database, tabelle e colonne) non possono contenere i caratteri stampabili punto (.), parentesi quadra aperta ([) o parentesi quadra chiusa (]).

-   L'autenticazione di Azure Active Directory non è supportata.

#### <a name="unsupported-data-types"></a>Tipi di dati non supportati

-   FileStream

-   Tipo definito dall'utente (UDT) SQL/CLR

-   XMLSchemaCollection (supportato da XML)

-   Cursor, Timestamp, Hierarchyid

#### <a name="limitations-on-service-and-database-dimensions"></a>Limitazioni alle dimensioni del servizio e del database

| **Dimensioni**                                                      | **Limite**              | **Soluzione alternativa**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Numero massimo di gruppi di sincronizzazione a cui può appartenere qualsiasi database.       | 5                      |                             |
| Numero massimo di endpoint in un singolo gruppo di sincronizzazione              | 30                     | Creare più gruppi di sincronizzazione |
| Numero massimo di endpoint locali in un singolo gruppo di sincronizzazione. | 5                      | Creare più gruppi di sincronizzazione |
| Nomi di database, tabella, schema e colonna                       | 50 caratteri per nome |                             |
| Tabelle in un gruppo di sincronizzazione                                          | 500                    | Creare più gruppi di sincronizzazione |
| Colonne in una tabella in un gruppo di sincronizzazione                              | 1000                   |                             |
| Dimensioni delle righe di dati in una tabella                                        | 24 MB                  |                             |
| Intervallo minimo di sincronizzazione                                           | 5 minuti              |                             |
|||

## <a name="faq-about-sql-data-sync"></a>Domande frequenti sulla sincronizzazione dati SQL

### <a name="how-much-does-the-sql-data-sync-preview-service-cost"></a>Quanto costa il servizio di anteprima di sincronizzazione dati SQL?

Durante l'anteprima, non è previsto alcun addebito per l'anteprima di sincronizzazione dati SQL.  Tuttavia, si accumuleranno ancora gli addebiti per il trasferimento dei dati dovuti allo spostamento dei dati da e verso l'istanza di database SQL. Per altre informazioni, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Quali aree supportano la sincronizzazione dati?

L'anteprima di sincronizzazione dati SQL è disponibile in tutte le aree di cloud pubblico.

### <a name="is-a-sql-database-account-required"></a>È necessario disporre di un account di database SQL? 

Sì. È necessario disporre di un account di database SQL per ospitare il database hub.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>È possibile usare la sincronizzazione dati solo tra database SQL Server locali? 
Non direttamente. Tuttavia, è possibile sincronizzare in maniera indiretta tra i database SQL Server locali creando un database hub in Azure e aggiungendo i database locali al gruppo di sincronizzazione.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>È possibile usare la sincronizzazione dati per effettuare il seeding dei dati da un database di produzione a un database vuoto, mantenendoli poi sincronizzati? 
Sì. Creare manualmente lo schema nel nuovo database effettuando lo scripting dall'originale. Dopo aver creato lo schema, aggiungere le tabelle a un gruppo di sincronizzazione per copiare i dati e mantenerli sincronizzati.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>È necessario usare la sincronizzazione dati SQL per eseguire il backup e il ripristino dei database?

Non è consigliabile usare l'anteprima di sincronizzazione dati SQL per creare un backup dei dati. Non è possibile eseguire il backup e il ripristino a una temporizzazione specificata, perché le sincronizzazioni dell'anteprima di sincronizzazione dati SQL sono senza versione. Inoltre, l'anteprima di sincronizzazione dati SQL non esegue il backup di altri oggetti SQL, come ad esempio stored procedure, e non esegue rapidamente l'equivalente di un'operazione di ripristino.

Per una tecnica di backup consigliata, vedere [Copiare un database SQL di Azure](sql-database-copy.md).

### <a name="is-collation-supported-in-sql-data-sync"></a>Le regole di confronto sono supportate nella sincronizzazione dati SQL?

Sì. La sincronizzazione dati SQL supporta le regole di confronto negli scenari seguenti:

-   Se le tabelle dello schema di sincronizzazione selezionate non sono già nel database hub o membro, quando si distribuisce il gruppo di sincronizzazione, il servizio crea automaticamente le tabelle e le colonne corrispondenti con le impostazioni delle regole di confronto selezionate nei database di destinazione vuoti.

-   Se le tabelle da sincronizzare sono già presenti sia nel database hub che nel database membro, la sincronizzazione dati SQL richiede che le colonne chiavi primarie abbiano le stesse regole di confronto in entrambi i database hub e membro per una distribuzione corretta del gruppo di sincronizzazione. Per le colonne diverse dalle colonne chiavi primarie non sono previste restrizioni relative alle regole di confronto.

### <a name="is-federation-supported-in-sql-data-sync"></a>La federazione è supportata nella sincronizzazione dati SQL?

Il database radice di federazione può essere usato nel servizio di anteprima di sincronizzazione dati SQL senza alcuna limitazione. Non è possibile aggiungere l'endpoint del database federato alla versione corrente dell'anteprima di sincronizzazione dati SQL.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla sincronizzazione dati SQL, vedere:

-   [Configurare la sincronizzazione dati SQL](sql-database-get-started-sql-data-sync.md)
-   [Procedure consigliate per la sincronizzazione dati SQL di Azure](sql-database-best-practices-data-sync.md)
-   [Monitorare la sincronizzazione dati SQL di Azure con OMS Log Analytics](sql-database-sync-monitor-oms.md)
-   [Risolvere i problemi della sincronizzazione dati SQL di Azure](sql-database-troubleshoot-data-sync.md)

-   Esempi di PowerShell completi che illustrano come configurare la sincronizzazione dati SQL:
    -   [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Scaricare la documentazione dell'API REST di sincronizzazione dati SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQL](sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
