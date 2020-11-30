---
title: Guida alla migrazione del database DB2 al database SQL
description: Seguire questa guida per eseguire la migrazione dei database DB2 al database SQL di Azure.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 369f17a39b9d7b2f85fffb8b72a293558d16416e
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325163"
---
# <a name="migration-guide-db2-to-sql-database"></a>Guida alla migrazione: DB2 al database SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Questa guida illustra come eseguire la migrazione dei database DB2 al database SQL di Azure usando SQL Server Migration Assistant per DB2. 

Per altri scenari, vedere la [Guida alla migrazione del database](https://datamigration.microsoft.com/).

## <a name="prerequisites"></a>Prerequisiti 

Per eseguire la migrazione del database DB2 al database SQL, è necessario:

- Verificare che l'ambiente di origine sia supportato.
- Scaricare [SQL Server Migration Assistant (SSMA) per DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- un [database SQL di Azure](../../database/single-database-create-quickstart.md)di destinazione.


## <a name="pre-migration"></a>Pre-migrazione

Una volta soddisfatti i prerequisiti, si è pronti per individuare la topologia dell'ambiente e valutare la fattibilità della migrazione. 

### <a name="assess-and-convert"></a>Valutazione e conversione

Creare una valutazione usando SQL Server Migration Assistant (SSMA). 

Per creare una valutazione, seguire questa procedura:

1. Aprire SQL Server Migration Assistant (SSMA) per DB2. 
1. Selezionare **File** e quindi scegliere **Nuovo progetto**. 
1. Specificare un nome di progetto, un percorso in cui salvare il progetto e quindi selezionare database SQL di Azure come destinazione della migrazione dall'elenco a discesa. Selezionare **OK**.  

   :::image type="content" source="media/db2-to-sql-database-guide/new-project.png" alt-text="Specificare i dettagli del progetto e selezionare OK per salvare.":::


1. Immettere i valori per i dettagli della connessione DB2 nella finestra di dialogo **Connect to DB2** (Connetti a DB2). 

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-db2.png" alt-text="Connettersi all'istanza di DB2":::


1. Fare clic con il pulsante destro del mouse sullo schema DB2 di cui si vuole eseguire la migrazione e quindi scegliere **Create report** (Crea report). Verrà generato un report HTML. In alternativa, è possibile scegliere **Create report** (Crea report) dalla barra di spostamento dopo aver selezionato lo schema. 

   :::image type="content" source="media/db2-to-sql-database-guide/create-report.png" alt-text="Fare clic con il pulsante destro del mouse sullo schema e scegliere Create report (Crea report)":::

1. Leggere il report HTML per esaminare le statistiche di conversione e gli eventuali errori o avvisi. È anche possibile aprire il report in Excel per ottenere un inventario degli oggetti DB2 e dell'impegno necessario per eseguire le conversioni dello schema. La posizione predefinita del report è la cartella report all'interno di SSMAProjects.

   Ad esempio: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-database-guide/report.png" alt-text="Esaminare il report per identificare eventuali errori o avvisi":::


### <a name="validate-data-types"></a>Convalidare i tipi di dati

Convalidare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A questo scopo, attenersi alla procedura seguente: 

1. Selezionare **Tools** (Strumenti) dal menu. 
1. Selezionare **Project Settings** (Impostazioni progetto). 
1. Selezionare la scheda **Type mappings** (Mapping tipi). 

   :::image type="content" source="media/db2-to-sql-database-guide/type-mapping.png" alt-text="Selezionare lo schema e quindi la scheda dei mapping dei tipi":::

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando la tabella in **DB2 Metadata Explorer**. 

### <a name="schema-conversion"></a>Conversione dello schema 

Per convertire lo schema, seguire questa procedura:

1. (Facoltativo) Aggiungere query dinamiche o ad hoc alle istruzioni. Fare clic con il pulsante destro del mouse sul nodo e quindi scegliere **Add statements** (Aggiungi istruzioni). 
1. Selezionare **Connetti a database SQL di Azure**. 
    1. Immettere i dettagli della connessione per connettere il database nel database SQL di Azure. 
    1. Scegliere il database SQL di destinazione dall'elenco a discesa. 
    1. Selezionare **Connetti**. 

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-sql-database.png" alt-text="Inserire i dettagli per la connessione al server logico in Azure":::


1. Fare clic con il pulsante destro del mouse sullo schema e scegliere **Convert Schema** (Converti schema). In alternativa, è possibile scegliere **Convert Schema** (Converti schema) dalla barra di spostamento superiore dopo aver selezionato lo schema. 

   :::image type="content" source="media/db2-to-sql-database-guide/convert-schema.png" alt-text="Fare clic con il pulsante destro del mouse sullo schema e scegliere Convert Schema (Converti schema)":::

1. Al termine della conversione, confrontare ed esaminare la struttura dello schema per identificare i potenziali problemi e risolverli in base alle raccomandazioni. 

   :::image type="content" source="media/db2-to-sql-database-guide/compare-review-schema-structure.png" alt-text="Confrontare ed esaminare la struttura dello schema per identificare i potenziali problemi e risolverli in base alle raccomandazioni.":::

1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. Scegliere **Salva progetto** dal menu **File**. 


## <a name="migrate"></a>Migrazione

Dopo aver completato la valutazione dei database e corretto eventuali discrepanze, il passaggio successivo consiste nell'eseguire il processo di migrazione.

Per pubblicare lo schema ed eseguire la migrazione dei dati, seguire questa procedura:

1. Pubblicare lo schema: fare clic con il pulsante destro del mouse sul database nel nodo **database** in **Esplora metadati del database SQL di Azure** e scegliere **Sincronizza con database**.

   :::image type="content" source="media/db2-to-sql-database-guide/synchronize-with-database.png" alt-text="Fare clic con il pulsante destro del mouse sul database e scegliere Synchronize with Database (Sincronizza con il database)":::

1. Eseguire la migrazione dei dati: Fare clic con il pulsante destro del mouse sullo schema in **DB2 Metadata Explorer** e scegliere **Migrate Data** (Esegui migrazione dati). 

   :::image type="content" source="media/db2-to-sql-database-guide/migrate-data.png" alt-text="Fare clic con il pulsante destro del mouse sullo schema e scegliere Migrate Data (Esegui migrazione dati)":::

1. Specificare i dettagli della connessione per il database DB2 e il database SQL di Azure. 
1. Visualizzare il **report di migrazione dei dati**. 

   :::image type="content" source="media/db2-to-sql-database-guide/data-migration-report.png" alt-text="Esaminare il report di migrazione dei dati":::

1. Connettersi al database SQL di Azure usando SQL Server Management Studio e convalidare la migrazione riesaminando i dati e lo schema. 

   :::image type="content" source="media/db2-to-sql-database-guide/compare-schema-in-ssms.png" alt-text="Confrontare lo schema in SSMS":::

## <a name="post-migration"></a>Post-migrazione 

Dopo aver completato la fase di migrazione, è necessario eseguire una serie di attività post-migrazione per assicurarsi che tutto funzioni nel modo più corretto ed efficiente possibile.

### <a name="remediate-applications"></a>Correggere le applicazioni 

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. Per ottenere questo risultato, in alcuni casi sarà necessario apportare modifiche alle applicazioni.


### <a name="perform-tests"></a>Eseguire test

L'approccio di test per la migrazione del database prevede le attività seguenti:

1. **Sviluppare i test di convalida**: per testare la migrazione del database, è necessario usare query SQL. È necessario creare le query di convalida da eseguire sia sul database di origine che su quello di destinazione. Le query di convalida devono essere estese all'ambito definito.
1. **Configurare un ambiente di test**: l'ambiente di test deve contenere una copia del database di origine e del database di destinazione. Assicurarsi di isolare l'ambiente di test.
1. **Eseguire test di convalida**: eseguire i test di convalida sull'origine e sulla destinazione, quindi analizzare i risultati.
1. **Eseguire test delle prestazioni**: eseguire test delle prestazioni sull'origine e sulla destinazione, quindi analizzare e confrontare i risultati.

   > [!NOTE]
   > Per assistenza nello sviluppo e nell'esecuzione di test di convalida post-migrazione, prendere in considerazione la soluzione per la qualità dei dati offerta dal partner [QuerySurge](https://www.querysurge.com/company/partners/microsoft). 


## <a name="leverage-advanced-features"></a>Sfruttare le funzionalità avanzate 

Assicurarsi di sfruttare le funzionalità avanzate basate sul cloud offerte dal database SQL, ad esempio la [disponibilità elevata incorporata](../../database/high-availability-sla.md), il rilevamento delle [minacce](../../database/azure-defender-for-sql.md)e il [monitoraggio e l'ottimizzazione del carico di lavoro](../../database/monitor-tune-overview.md). 


Alcune funzionalità SQL Server sono disponibili solo quando il [livello di compatibilità del database](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) viene impostato sul livello di compatibilità più recente (150). 

## <a name="migration-assets"></a>Risorse per la migrazione 

Per ulteriore assistenza, vedere le risorse seguenti, che sono state sviluppate a supporto di un progetto di migrazione reale:

|Asset  |Descrizione  |
|---------|---------|
|[Strumento e modello di valutazione dei carichi di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Questo strumento indica le piattaforme di destinazione "più idonee" suggerite, la preparazione per il cloud e il livello di correzione di applicazioni/database per un determinato carico di lavoro. Offre funzionalità semplici e accessibili con un solo clic per l'esecuzione di calcoli e la generazione di report, che consentono di accelerare le valutazioni in ambienti estesi grazie a un processo decisionale automatizzato e uniforme per la piattaforma di destinazione.|
|[Pacchetto di individuazione e valutazione degli asset di dati DB2 zOS](https://github.com/Microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Dopo aver eseguito lo script SQL in un database, è possibile esportare i risultati in un file nel file system. Sono supportati diversi formati di file, incluso il formato CSV, in modo che sia possibile acquisire i risultati in strumenti esterni come i fogli di calcolo. Questo metodo può essere utile se si vogliono condividere facilmente i risultati con i team che non hanno installato il workbench.|
|[Script e artefatti di inventario per IBM DB2 LUW](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Questa risorsa include una query SQL per le tabelle di sistema IBM DB2 LUW versione 11.1 e fornisce un conteggio degli oggetti in base al tipo di schema e di oggetto, una stima approssimativa dei dati non elaborati in ogni schema e le dimensioni delle tabelle in ogni schema, con risultati archiviati in formato CSV.|
|[Guida all'installazione di DB2 LUW pureScale in Azure](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Questa guida funge da punto di partenza per un piano di implementazione di DB2. I requisiti varieranno da azienda ad azienda, ma lo stesso modello di base vale per tutte. Questo modello di architettura può essere usato anche per le applicazioni OLAP in Azure.|

Queste risorse sono state sviluppate come parte del programma Data SQL Ninja, sponsorizzato dal team di progettazione Azure Data Group. Obiettivo principale del programma Data SQL Ninja è rendere disponibili opportunità per velocizzare progetti di modernizzazione complessi e la migrazione delle piattaforme dati alla piattaforma dati di Microsoft Azure. Se si ritiene che l'organizzazione possa essere interessata a partecipare al programma Data SQL Ninja, contattare il team dell'account per richiedere l'invio di una candidatura.



## <a name="next-steps"></a>Passaggi successivi

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per supportare diversi scenari di migrazione di database e dati, nonché per le attività speciali, vedere [servizio e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per altre informazioni sul database SQL di Azure, vedere:
   - [Panoramica del database SQL](../../database/sql-database-paas-overview.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per il costo e il ridimensionamento dei carichi di lavoro migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Per informazioni dettagliate su come eseguire i test A/B di livello di accesso ai dati, vedere [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).