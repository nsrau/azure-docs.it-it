<properties 
   pageTitle="Query Performance Insight del database SQL di Azure" 
   description="Il monitoraggio delle prestazioni delle query identifica le query principali a livello di utilizzo di DTU per un database SQL di Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="09/30/2015"
   ms.author="sstein"/>

# Query Performance Insight del database SQL di Azure


La gestione e l'ottimizzazione delle prestazioni dei database relazionali è un'attività complessa che richiede un'esperienza significativa e un investimento elevato in termini di tempo. Query Performance Insight consente di dedicare meno tempo alla risoluzione dei problemi delle prestazioni del database, offrendo i vantaggi seguenti:​

- Informazioni più approfondite sull'utilizzo delle risorse del database (DTU). 
- Query principali a livello di utilizzo di DTU, che possono essere ottimizzate per migliorare le prestazioni. 
- Capacità di eseguire il drill-down nei dettagli di una query. ​

> [AZURE.NOTE]Query Performance Insight è attualmente disponibile in modalità di anteprima e solo nel [portale di anteprima di Azure](https://portal.azure.com/).



## Prerequisiti

- Query Performance Insight è disponibile solo con il database SQL di Azure V12.
- Query Performance Insight richiede che l'[archivio query](https://msdn.microsoft.com/library/dn817826.aspx) sia in esecuzione, quindi l'archivio viene abilitato automaticamente quando ci si iscrive a Query Performance Insight.
 
 
## Autorizzazioni

Le autorizzazioni di [Controllo degli accessi in base al ruolo](role-based-access-control-configure.md) seguenti sono necessarie per usare Query Performance Insight:

- Le autorizzazioni **Lettore**, **Proprietario**, **Collaboratore**, **Collaboratore database SQL** o **Collaboratore SQL Server** sono necessarie per visualizzare le query principali che utilizzano le risorse e i grafici. 
- Le autorizzazioni **Proprietario**, **Collaboratore**, **Collaboratore database SQL** o **Collaboratore SQL Server** sono necessarie per visualizzare il testo della query.



## Uso di Query Performance Insight

Query Performance Insight è facile da usare:

- Esaminare l'elenco delle query principali a livello di utilizzo delle risorse. 
- Selezionare una singola query per visualizzarne i dettagli.
- Fare clic su **Modifica grafico** per personalizzare la modalità di visualizzazione dei dati relativi all'utilizzo di DTU o per mostrare un periodo di tempo diverso.



> [AZURE.NOTE]Per consentire al database SQL di fornire approfondimenti sulle prestazioni delle query, è necessario che l'archivio query acquisisca un paio di ore di dati. Se il database non ha alcuna attività o l'archivio query non è attivo in un determinato periodo di tempo, i grafici saranno vuoti quando viene visualizzato quel periodo di tempo. È possibile abilitare l'archivio query in qualsiasi momento, se non è in esecuzione.





## Esaminare le query principali a livello di utilizzo di DTU

Eseguire le operazioni seguenti nel [portale di anteprima](https://portal.azure.com):

1. Passare a un database SQL e fare clic su **Query Performance Insight**. 

    ![Query Performance Insight][1]

    Verrà aperta la visualizzazione relativa alle query principali e verrà mostrato l'elenco delle query principali a livello di utilizzo di DTU.

1. Per informazioni dettagliate, fare clic sul grafico.<br>La prima riga mostra la percentuale di utilizzo di DTU complessiva per il database, mentre le barre mostrano la percentuale di DTU utilizzata dalle query selezionate. Selezionare o deselezionare singole query per includerle o escluderle dal grafico.

    ![query principali][2]

1. Facoltativamente, fare clic su **Modifica grafico** per personalizzare la modalità di visualizzazione dei dati relativi all'utilizzo di DTU o per mostrare un periodo di tempo diverso.

## Visualizzazione dei dettagli delle singole query

Per visualizzare i dettagli relativi alle query:

1. Fare clic su qualsiasi query nell'elenco delle query principali.<br>La visualizzazione dettagliata verrà aperta e l'utilizzo di DTU delle query verrà suddiviso nel tempo. 
3. Per informazioni dettagliate, fare clic sul grafico.<br>La prima riga mostra la percentuale di utilizzo di DTU complessiva e le barre indicano la percentuale di DTU utilizzata dalla query selezionata.
4. Esaminare i dati per visualizzare metriche dettagliate, ad esempio durata, numero di esecuzioni e percentuale di utilizzo delle risorse per ogni intervallo di esecuzione della query.
    
    ![dettagli sulle query][3]

1. Facoltativamente, fare clic su **Visualizza script** per visualizzare il testo della query, quindi fare clic su **Modifica grafico** per personalizzare la modalità di visualizzazione dei dati relativi all'utilizzo di DTU o per mostrare un periodo di tempo diverso.




## Riepilogo

Query Performance Insight semplifica la comprensione dell'impatto del carico di lavoro della query e la relativa correlazione all'utilizzo delle risorse del database. Questa funzionalità consente di ottenere informazioni sulle query principali a livello di utilizzo di risorse e di identificare facilmente le query da correggere prima che si verifichino problemi. Fare clic sul riquadro **Query Performance Insight** nel pannello del database per visualizzare le query principali a livello di utilizzo delle risorse (DTU).




## Passaggi successivi

I carichi di lavoro dei database sono dinamici e cambiano in modo continuo. Monitorare le query e continuare a perfezionarle per ottimizzare le prestazioni.

Per indicazioni aggiuntive sul miglioramento delle prestazioni del database SQL, vedere l'[advisor dell'indice](sql-database-index-advisor.md).

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png

<!---HONumber=Oct15_HO3-->