---
title: Introduzione alle tabelle temporali nel database SQL di Azure | Microsoft Docs
description: Informazioni introduttive sull&quot;uso delle tabelle temporali nel database SQL di Azure.
services: sql-database
documentationcenter: 
author: bonova
manager: jhubbard
editor: 
ms.assetid: c8c0f232-0751-4a7f-a36e-67a0b29fa1b8
ms.service: sql-database
ms.custom: development
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sql-database
ms.date: 01/10/2017
ms.author: bonova
translationtype: Human Translation
ms.sourcegitcommit: 10b40214ad4c7d7bb7999a5abce1c22100b617d8
ms.openlocfilehash: e00345ddd9e52e2613789ba78c48e8f993d2415c


---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Introduzione alle tabelle temporali nel database SQL di Azure
Le tabelle temporali sono una nuova funzionalità di programmabilità del database SQL di Azure che consente di monitorare e analizzare la cronologia completa delle modifiche ai dati, senza dover scrivere codice personalizzato. Le tabelle temporali mantengono i dati strettamente correlati al contesto temporale, in modo che i fatti archiviati possano essere interpretati come validi solo entro il periodo specifico. Questa proprietà delle tabelle temporali consente di eseguire un'analisi efficace basata sul tempo e di ottenere informazioni accurate dall'evoluzione dei dati.

## <a name="temporal-scenario"></a>Scenario temporale
Questo articolo illustra la procedura per l'utilizzo delle tabelle temporali in uno scenario di applicazione. Si supponga di voler tenere traccia delle attività dell'utente in un nuovo sito Web sviluppato da zero o in un sito Web esistente che si vuole estendere con l'analisi delle attività dell'utente. In questo esempio semplificato si presuppone che il numero di pagine Web visitate in un arco di tempo sia un indicatore da acquisire e monitorare nel database del sito Web, ospitato in un database SQL di Azure. L'obiettivo dell'analisi cronologica delle attività dell'utente è ottenere informazioni per riprogettare il sito Web e offrire un'esperienza migliore ai visitatori.

Il modello di database per questo scenario è molto semplice: la metrica dell'attività utente è rappresentata con un singolo campo integer, **PageVisited**, e viene acquisita insieme alle informazioni di base sul profilo utente. Per l'analisi basata sul tempo si usa anche una serie di righe per ogni utente e ogni riga rappresenta il numero di pagine visitate da un determinato utente all'interno di un arco di tempo specifico.

![Schema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Per mantenere le informazioni sulle attività non è necessario eseguire alcuna operazione nell'app. Con le tabelle temporali questo processo è automatizzato e offre piena flessibilità in fase di progettazione del sito Web e più tempo da dedicare all'analisi dei dati vera e propria. L'unica cosa da fare è assicurarsi che la tabella **WebSiteInfo** sia configurata come [temporale con controllo delle versioni di sistema](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Di seguito sono descritti i passaggi necessari per usare le tabelle temporali in questo scenario.

## <a name="step-1-configure-tables-as-temporal"></a>Passaggio 1: Configurare le tabelle come temporali
A seconda che si tratti dello sviluppo di una nuova applicazione o dell'aggiornamento di una esistente, creare le tabelle temporali o modificare tabelle esistenti aggiungendo attributi temporali. In genere, lo scenario può essere una combinazione di queste due opzioni. Eseguire queste azioni usando [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) o qualsiasi altro strumento di sviluppo Transact-SQL.

> [!IMPORTANT]
> È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Creare una nuova tabella
Usare la voce di menu contestuale "Nuova tabella con controllo delle versioni di sistema" in Esplora oggetti di SSMS per aprire l'editor di query con uno script modello di tabella temporale e quindi usare "Imposta valori per parametri modello" (CTRL+MAIUSC+M) per popolare il modello:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

In SSDT scegliere il modello "Tabella temporale (con controllo delle versioni di sistema)" quando si aggiungono nuovi elementi al progetto di database. Verrà aperta la progettazione tabelle e sarà possibile specificare facilmente il layout di tabella:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Per creare una tabella temporale è anche possibile specificare direttamente le istruzioni Transact-SQL, come illustrato nell'esempio seguente. Si noti che gli elementi obbligatori di ogni tabella temporale sono la definizione PERIOD e la clausola SYSTEM_VERSIONING con un riferimento a un'altra tabella utente in cui vengono archiviate le versioni delle righe della cronologia:

````
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
````

Quando si crea una tabella temporale con controllo delle versioni di sistema, viene creata automaticamente la tabella della cronologia associata con la configurazione predefinita. La tabella della cronologia predefinita contiene un indice albero B cluster nelle colonne periodo (end, start) con la compressione di pagina abilitata. Questa configurazione è ottimale per la maggior parte degli scenari in cui vengono usate le tabelle temporali, soprattutto per il [controllo dei dati](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

In questo caso specifico, l'obiettivo è eseguire l'analisi delle tendenze basata sul tempo su una cronologia di dati più estesa e set di dati più grandi. Per l'archiviazione della tabella della cronologia occorre quindi scegliere un indice columnstore cluster. Un columnstore cluster offre ottimi livelli di compressione e prestazioni per le query analitiche. La flessibilità delle tabelle temporali permette di configurare gli indici nelle tabelle temporali e correnti in modo completamente indipendente. 

> [!NOTE]
> Gli indici columnstore sono disponibili solo per il livello di servizio Premium.
>

Lo script seguente mostra come modificare l'indice predefinito nella tabella della cronologia nel columnstore cluster:

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

In Esplora oggetti le tabelle temporali sono rappresentate con un'icona specifica per facilitarne l'identificazione, mentre la relativa tabella della cronologia viene visualizzata come nodo figlio.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Rendere temporale una tabella esistente
In uno scenario alternativo, la tabella WebsiteUserInfo esiste già ma non è stata progettata per mantenere una cronologia delle modifiche. In questo caso, è possibile estendere semplicemente la tabella esistente rendendola temporale, come illustrato nell'esempio seguente:

````
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

## <a name="step-2-run-your-workload-regularly"></a>Passaggio 2: Eseguire regolarmente il carico di lavoro
Il vantaggio principale delle tabelle temporali è che non è necessario modificare il sito Web in alcun modo per eseguire il rilevamento delle modifiche. Dopo la creazione, le tabelle temporali mantengono in modo trasparente le versioni precedenti delle righe ogni volta che si apportano modifiche ai dati. 

Per poter sfruttare il rilevamento automatico delle modifiche per questo particolare scenario, è sufficiente aggiornare la colonna **PagesVisited** ogni volta che l'utente termina la sessione sul sito Web:

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

È importante notare che la query di aggiornamento non deve necessariamente conoscere l'ora esatta in cui si è verificata l'operazione effettiva né come verranno mantenuti i dati cronologici per analisi future. Entrambi gli aspetti vengono gestiti automaticamente dal database SQL di Azure. Il diagramma seguente illustra come vengono generati i dati di cronologia a ogni aggiornamento.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Passaggio 3: Eseguire l'analisi dei dati cronologici
Quando il controllo delle versioni di sistema temporale è abilitato, per l'analisi dei dati cronologici è sufficiente eseguire una query. Questo articolo contiene alcuni esempi relativi agli scenari di analisi più comuni. Per informazioni dettagliate, vedere le varie opzioni introdotte con la clausola [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3).

Per visualizzare i primi 10 utenti ordinati in base al numero di pagine Web visitate nell'ora precedente, eseguire questa query:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

È possibile modificare facilmente questa query per analizzare le visite ai siti di un giorno prima, un mese prima o in qualsiasi momento nel passato.

Per eseguire analisi statistiche di base per il giorno precedente, usare questo esempio:

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

Per cercare le attività di un utente specifico entro un periodo di tempo, usare la clausola CONTAINED IN:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

La visualizzazione grafica risulta particolarmente utile per le query temporali, perché permette di visualizzare tendenze e modelli d'uso in modo molto semplice e intuitivo:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Evoluzione dello schema di tabella
In genere, è necessario modificare lo schema di tabella temporale durante lo sviluppo di app. A tale scopo, è sufficiente eseguire normali istruzioni ALTER TABLE e il database SQL di Azure propagherà in modo appropriato le modifiche alla tabella della cronologia. Lo script seguente mostra come aggiungere altri attributi per il rilevamento:

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

Analogamente, è possibile modificare la definizione di colonna mentre il carico di lavoro è attivo:

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Infine, è possibile rimuovere una colonna non più necessaria.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````

In alternativa, usare la versione più recente di [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) per modificare lo schema di tabella temporale durante la connessione al database (modalità online) o nell'ambito del progetto di database (modalità offline).

## <a name="controlling-retention-of-historical-data"></a>Controllo della conservazione dei dati cronologici
Con le tabelle temporali con controllo delle versioni di sistema, la tabella della cronologia può aumentare le dimensioni del database più delle normali tabelle. Una tabella della cronologia di grandi dimensioni che continua a crescere può diventare un problema a causa dei costi di archiviazione e influire negativamente sulle prestazioni durante l'esecuzione di query temporali. Di conseguenza, lo sviluppo di criteri di conservazione dei dati per la gestione dei dati nella tabella della cronologia è un aspetto importante della pianificazione e della gestione del ciclo di vita di ogni tabella temporale. Con il database SQL di Azure è possibile adottare uno degli approcci seguenti per la gestione dei dati cronologici nella tabella temporale:

* [Partizionamento di tabelle](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Script di pulizia personalizzato](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sulle tabelle temporali, vedere la [documentazione MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Visitare Channel 9 per ascoltare un [vero caso di successo di implementazione temporale](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e guardare una [dimostrazione temporale dal vivo](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).




<!--HONumber=Jan17_HO2-->


