<properties 
   pageTitle="Query Performance Insight del database SQL di Azure" 
   description="Il monitoraggio delle prestazioni delle query identifica le query principali a livello di utilizzo di CPU per un database SQL di Azure." 
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
   ms.date="04/28/2016"
   ms.author="sstein"/>

# Query Performance Insight del database SQL di Azure


La gestione e l'ottimizzazione delle prestazioni dei database relazionali è un'attività complessa che richiede un'esperienza significativa e un investimento elevato in termini di tempo. Query Performance Insight consente di dedicare meno tempo alla risoluzione dei problemi delle prestazioni del database, offrendo i vantaggi seguenti:​

- Informazioni più approfondite sull'utilizzo delle risorse del database (DTU). 
- Query principali a livello di utilizzo di CPU, che possono essere ottimizzate per migliorare le prestazioni. 
- Capacità di eseguire il drill-down nei dettagli di una query. ​

## Prerequisiti

- Query Performance Insight è disponibile solo con il database SQL di Azure V12.
- Per Query Performance Insight è necessario che l'[archivio query](https://msdn.microsoft.com/library/dn817826.aspx) sia in esecuzione nel database. Nel portale verrà richiesto di attivare l'archivio query, se non è già in esecuzione.

 
## Autorizzazioni

Le autorizzazioni di [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md) seguenti sono necessarie per usare Query Performance Insight:

- Le autorizzazioni **Lettore**, **Proprietario**, **Collaboratore**, **Collaboratore database SQL** o **Collaboratore SQL Server** sono necessarie per visualizzare le query principali che utilizzano le risorse e i grafici. 
- Le autorizzazioni **Proprietario**, **Collaboratore**, **Collaboratore database SQL** o **Collaboratore SQL Server** sono necessarie per visualizzare il testo della query.



## Uso di Query Performance Insight

Query Performance Insight è facile da usare:

- Esaminare l'elenco delle query principali a livello di utilizzo delle risorse. 
- Selezionare una singola query per visualizzarne i dettagli.
- Aprire [Performance Advisor](sql-database-index-advisor.md) e verificare se sono disponibili indicazioni.
- Eseguire lo zoom avanti per informazioni dettagliate.
- 
    ![dashboard prestazioni](./media/sql-database-query-performance/performance.png)

> [AZURE.NOTE] Per consentire al database SQL di fornire approfondimenti sulle prestazioni delle query, è necessario che l'archivio query acquisisca un paio di ore di dati. Se il database non ha alcuna attività o l'archivio query non è attivo in un determinato periodo di tempo, i grafici saranno vuoti quando viene visualizzato quel periodo di tempo. È possibile abilitare l'archivio query in qualsiasi momento, se non è in esecuzione.



## Esaminare le query principali a livello di utilizzo di CPU

Eseguire le operazioni seguenti nel [portale](http://portal.azure.com):

1. Passare a un database SQL e fare clic su **Tutte le impostazioni** > **Prestazioni** > **Query**. 

    ![Query Performance Insight][1]

    Verrà aperta la visualizzazione relativa alle query principali e verrà mostrato l'elenco delle query principali a livello di utilizzo di CPU.

1. Per informazioni dettagliate, fare clic nei vari punti del grafico.<br>La prima riga visualizza la percentuale di uso di DTU complessiva per il database, mentre le barre visualizzano la percentuale di CPU usata dalle query selezionate durante l'intervallo selezionato (ad esempio, se si seleziona **Settimana precedente** ogni barra rappresenta 1 giorno).

    ![query principali][2]

    La griglia inferiore rappresenta informazioni aggregate per le query visibili.

    -	Utilizzo medio della CPU per query durante l'intervallo osservabile. 
    -	Durata totale per query.
    -	Numero totale di esecuzioni per una query specifica.


	Selezionare o deselezionare singole query per includerle o escluderle dal grafico.


1. Se i dati non vengono più aggiornati, fare clic sul pulsante **Aggiorna**.
1. Facoltativamente, fare clic su **Impostazioni** per personalizzare la modalità di visualizzazione dei dati relativi all'utilizzo di CPU o per mostrare un periodo di tempo diverso.

    ![Scheda Impostazioni](./media/sql-database-query-performance/settings.png)

## Visualizzazione dei dettagli delle singole query

Per visualizzare i dettagli relativi alle query:

1. Fare clic su qualsiasi query nell'elenco delle query principali.

    ![informazioni dettagliate](./media/sql-database-query-performance/details.png)

4. Verrà aperta la visualizzazione dettagliata e l'utilizzo di CPU delle query verrà suddiviso nel tempo.
3. Per informazioni dettagliate, fare clic sul grafico.<br>La prima riga mostra la percentuale di utilizzo di DTU complessiva e le barre indicano la percentuale di CPU utilizzata dalla query selezionata.
4. Esaminare i dati per visualizzare metriche dettagliate, ad esempio durata, numero di esecuzioni e percentuale di utilizzo delle risorse per ogni intervallo di esecuzione della query.
    
    ![dettagli sulle query][3]

1. Facoltativamente, fare clic su **Impostazioni** per personalizzare la modalità di visualizzazione dei dati relativi all'utilizzo di CPU o per mostrare un periodo di tempo diverso.


## 	Ottimizzare la configurazione dell'archivio query per Informazioni dettagliate prestazioni query

Durante l'uso di Informazioni dettagliate prestazioni query, possono essere visualizzati messaggi dell'archivio query simili ai seguenti:

- "L'archivio query ha raggiunto la capacità massima e non può raccogliere nuovi dati."
- "L'archivio query è in modalità di sola lettura per questo database e non può raccogliere dati dettagliati sulle prestazioni."
- "I parametri dell'archivio query non sono impostati in modo ottimale per Informazioni dettagliate prestazioni query."

Questi messaggi in genere vengono visualizzati quando l'archivio query non è in grado di raccogliere nuovi dati. Per risolvere questo problema sono disponibili alcune opzioni:

-	Modificare i criteri di conservazione e acquisizione dell'archivio query
-	Aumentare le dimensioni dell'archivio query 
-	Cancellare l'archivio query

### Criteri di conservazione e acquisizione consigliati

Esistono due tipi di criteri di conservazione:

- Basati sulle dimensioni: se impostati su AUTOMATICO i dati verranno automaticamente cancellati al raggiungimento delle dimensioni massime.
- Basati sul tempo: per impostazione predefinita verranno impostati su 30 giorni in modo tale che, se verrà esaurito lo spazio, l'archivio query eliminerà le informazioni di query antecedenti a 30 giorni. 

I criteri di acquisizione possono essere impostati su:

- **Tutte**: acquisisce tutte le query. Questa è l'opzione predefinita.
- **Automatico**: le query poco frequenti e con durata di compilazione ed esecuzione trascurabile vengono ignorate. Le soglie per il conteggio delle esecuzioni e la durata di compilazione ed esecuzione vengono stabilite internamente.
- **Nessuna**: l'archivio query interrompe l'acquisizione di nuove query.
	
È consigliabile impostare tutti i criteri su AUTOMATICO e i criteri di pulizia dei dati su 30 giorni:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
    	
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Aumentare le dimensioni dell'archivio query. È possibile eseguire questa operazione connettendosi a un database ed eseguendo la query seguente:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Cancellare l'archivio query. Tenere presente che in questo modo verranno eliminate tutte le informazioni correnti nell'archivio query:

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## Riepilogo

Query Performance Insight semplifica la comprensione dell'impatto del carico di lavoro della query e la relativa correlazione all'utilizzo delle risorse del database. Questa funzionalità consente di ottenere informazioni sulle query principali a livello di utilizzo di risorse e di identificare facilmente le query da correggere prima che si verifichino problemi. Fare clic su **Informazioni dettagliate prestazioni query** per un database per visualizzare le query che usano la maggior quantità di risorse (CPU).




## Passaggi successivi

Per indicazioni aggiuntive sul miglioramento delle prestazioni del database SQL, vedere [Performance Advisor](sql-database-index-advisor.md) nel pannello **Informazioni dettagliate prestazioni query**.

![Performance Advisor](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png

<!---HONumber=AcomDC_0504_2016-->