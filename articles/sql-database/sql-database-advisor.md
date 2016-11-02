<properties 
   pageTitle="Advisor per database SQL di Azure" 
   description="L'advisor per database SQL di Azure offre raccomandazioni per i database SQL esistenti che consentono di migliorare le prestazioni correnti delle query." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="06/23/2016"
   ms.author="sstein"/>

# Advisor per database SQL

> [AZURE.SELECTOR]
- [Panoramica di SQL Database Advisor](sql-database-advisor.md)
- [Portale](sql-database-advisor-portal.md)

Il database SQL di Azure fornisce raccomandazioni per la creazione e la rimozione di indici, la parametrizzazione di query e la risoluzione dei problemi di schema. L'advisor per database SQL consente di valutare le prestazioni analizzando la cronologia relativa all'utilizzo del database SQL. È consigliabile usare le raccomandazioni più adatte per l'esecuzione del carico di lavoro tipico del database.

Sono disponibili le raccomandazioni seguenti per i server V12 (non sono disponibili raccomandazioni per i server V11). Attualmente è possibile impostare l'applicazione automatica delle raccomandazioni per la creazione e l'eliminazione di indici. Per informazioni dettagliate, vedere la sezione sulla [gestione automatica degli indici](sql-database-advisor-portal.md#enable-automatic-index-management).

## Raccomandazioni relative alla creazione di indici 

Le raccomandazioni relative alla **creazione di indici** vengono visualizzate quando il servizio Database SQL rileva un indice mancante che, se creato, potrebbe risultare vantaggioso per il carico di lavoro dei database (solo indici non cluster).

## Raccomandazioni relative all'eliminazione di indici

Le raccomandazioni relative all'**eliminazione di indici** vengono visualizzate quando il servizio Database SQL rileva indici duplicati (attualmente in anteprima e applicabile solo agli indici duplicati).

## Raccomandazioni relative alla creazione di query con parametri

Le raccomandazioni relative alla **creazione di query con parametri** vengono visualizzate quando il servizio Database SQL rileva la presenza di una o più query che vengono ricompilate costantemente ma finiscono con lo stesso piano di esecuzione di query. Questo fa emergere l'opportunità di applicare l'impostazione forzata di parametri, in modo da permettere la memorizzazione nella cache e il riutilizzo delle query in futuro, con conseguente miglioramento delle prestazioni e riduzione dell'utilizzo delle risorse.

Ogni query eseguita inizialmente su SQL Server deve essere compilata per generare un piano di esecuzione che verrà usato per eseguire la query. Ogni piano generato viene aggiunto alla cache dei piani e le esecuzioni successive della stessa query possono riusare il piano dalla cache, eliminando la necessità di un'ulteriore compilazione.

Le applicazioni che inviano query che includono valori senza parametri possono causare un overhead delle prestazioni, in cui per ogni query di questo tipo con valori di parametri diversi viene compilato di nuovo il piano di esecuzione. In molti casi le stesse query con valori di parametri diversi generano gli stessi piani di esecuzione, ma questi piani vengono comunque aggiunti separatamente alla cache dei piani. Queste ricompilazioni usano risorse del database, aumentano la durata della query e determinano l'overflow della cache dei piani con conseguente rimozione dei piani dalla cache. Questo comportamento di SQL Server può essere modificato impostando l'opzione di parametrizzazione forzata nel database.

Per stimare l'impatto di questa raccomandazione, viene illustrato un confronto tra l'utilizzo effettivo e quello previsto della CPU (nel caso di applicazione della raccomandazione). Oltre al risparmio della CPU, anche la durata della query diminuirà per il tempo impiegato nella compilazione. Anche il sovraccarico della cache dei piani sarà nettamente ridotto, consentendo alla maggioranza dei piani di rimanere nella cache e di essere riusata. Questa raccomandazione può essere applicata rapidamente e facilmente facendo clic sul comando "Applica".

Dopo l'applicazione, la raccomandazione abilita la parametrizzazione forzata entro pochi minuti sul database e inizia il processo di monitoraggio che dura circa 24 ore. Trascorso questo intervallo, sarà possibile visualizzare il rapporto di convalida che mostra l'utilizzo della CPU del database 24 ore prima e dopo l'applicazione della raccomandazione. SQL Database Advisor dispone di un meccanismo di sicurezza che ripristina automaticamente la raccomandazione applicata in caso di rilevamento di una regressione delle prestazioni.

## Raccomandazioni relative alla correzione di problemi di schema

Le raccomandazioni relative alla **correzione di problemi di schema** vengono visualizzate quando il servizio Database SQL rileva un'anomalia nel numero di errori SQL correlati allo schema nel database SQL di Azure. In genere questa indicazione viene visualizzata quando il database rileva più errori correlati allo schema (nome di colonna non valido, il nome di oggetto non valido e così via) nell'arco di un'ora.

"I problemi di schema" rappresentano una classe di errori di sintassi in SQL Server che si verificano quando la definizione della query SQL e la definizione dello schema del database non sono allineate (ad esempio, una delle colonne previste dalla query potrebbe essere assente nella tabella di destinazione o viceversa).

La raccomandazione relativa alla "correzione di problemi di schema" viene visualizzata quando il servizio Database SQL di Azure rileva un'anomalia nel numero di errori SQL correlati allo schema nel database SQL di Azure. La tabella seguente illustra gli errori correlati a problemi di schema:

|Codice di errore SQL|Message|
|--------------|-------|
|201|La procedura o funzione '*' richiede il parametro '*', che non è stato specificato.|
|207|Il nome di colonna '*' non è valido.|
|208|Il nome di oggetto '*' non è valido. |
|213|Il nome della colonna o il numero dei valori specificati non corrisponde alla definizione della tabella. |
|2812|Non è stato possibile trovare la stored procedure '*'. |
|8144|Numero eccessivo di argomenti specificati per la procedura o la funzione *. |

## Passaggi successivi

Monitorare le raccomandazioni e continuare ad applicarle in modo da migliorare le prestazioni. I carichi di lavoro dei database sono dinamici e cambiano in modo continuo. L'advisor per database SQL continuerà a monitorare e fornire raccomandazioni potenzialmente utili per migliorare le prestazioni del database.

 - Vedere l'articolo su [SQL Database Advisor nel portale di Azure](sql-database-advisor-portal.md) per istruzioni sul relativo uso.
 - Vedere l'articolo [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) per imparare a esaminare l'impatto sulle prestazioni delle query principali.

## Risorse aggiuntive

- [Archivio query](https://msdn.microsoft.com/library/dn817826.aspx)
- [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
- [Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_0629_2016-->