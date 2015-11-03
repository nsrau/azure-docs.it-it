<properties
	pageTitle="Usare OLTP in memoria per migliorare le prestazioni transazionali di SQL Azure | Microsoft Azure"
	description="Adottare OLTP in memoria per migliorare le prestazioni transazionali in un database SQL esistente."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# Uso della tecnologia in memoria (anteprima) per migliorare le prestazioni delle applicazioni SQL di Azure

Seguire questa procedura per ottimizzare le prestazioni transazionali del database SQL Premium di Azure esistente usando la tecnologia [In memoria](sql-database-in-memory.md).

Per il confronto, scegliere un carico di lavoro simile al carico di lavoro di produzione con un numero simile di connessioni simultanee e rapporto di lettura/scrittura. Per ridurre al minimo la latenza di rete, è consigliabile eseguire test di carico di lavoro nella stessa area di Azure del database.

## Passaggio 1: Copiare i dati in un nuovo database Premium
1.	Esportare il database di produzione in un file bacpac usando:

	A. La funzionalità Esporta nel [portale](https://portal.azure.com/) oppure

	B. La funzionalità Esporta l'applicazione livello dati in SQL Server Management Studio.

2.	Importare il file bacpac in un nuovo database Premium in un server V12:

	A. Nel portale: passare al server e selezionare l'opzione Importa database. Assicurarsi di selezionare un piano tariffario Premium.

	B. In SQL Server Management Studio (SSMS): connettersi al server, fare clic con il pulsante destro del mouse sul nodo Database e selezionare Importa applicazione livello dati.


## Passaggio 2: Identificare gli oggetti per eseguire la migrazione a OLTP In memoria
SQL Server Management Studio (SSMS) include un report per l'analisi delle prestazioni delle transazioni che è possibile eseguire in un database con un carico di lavoro attivo, per identificare le tabelle e le stored procedure candidate per la migrazione a OLTP in memoria. Per altre informazioni, vedere [Determinare se una tabella o una stored procedure deve essere trasferita a OLTP in memoria](https://msdn.microsoft.com/library/dn205133.aspx).

1.	Connettersi al database di produzione tramite SSMS. In alternativa, se si ha un carico di lavoro in esecuzione nel nuovo database di test, è anche possibile connettersi a quel database.
2.	Fare clic con il pulsante destro del mouse sul database e selezionare Report -> Report standard -> Report di analisi delle prestazioni delle transazioni. Il report consente di identificare le tabelle e le stored procedure che, in base all'utilizzo, possono trarre vantaggio da OLTP In memoria.


## Passaggio 3: Eseguire la migrazione delle tabelle
1.	Connettersi al nuovo database di test tramite SSMS. Per evitare di usare l'opzione WITH (SNAPSHOT) nelle query, è consigliabile impostare l'opzione di database MEMORY\_OPTIMIZED\_ELEVATE\_TO\_SNAPSHOT.
2.	Una volta stabilita la connessione al nuovo database di test, eseguire le operazioni seguenti:

   	    ALTER DATABASE CURRENT SET MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT=ON

3.	Eseguire la migrazione della tabella basata su disco con ottimizzazione per la memoria tramite uno di questi approcci:

	A. Ottimizzazione guidata per la memoria di SSMS: quando si è connessi al database di test, fare clic con il pulsante destro del mouse sulla tabella e selezionare Ottimizzazione guidata per la memoria. Usare questo strumento per determinare se la tabella contiene eventuali funzionalità che non sono supportate con ottimizzazione per la memoria. In caso contrario, può essere eseguita la migrazione dello schema e dei dati effettivi. Per alte informazioni, vedere l'[argomento relativo all'Ottimizzazione guidata per la memoria su MSDN](https://msdn.microsoft.com/library/dn284308.aspx).

	B. Migrazione manuale: connettersi al nuovo database di test tramite SSMS.

Seguire questa procedura per eseguire la migrazione di una tabella:

1.	Generare uno script per la tabella facendo clic con il pulsante destro del mouse sulla tabella e scegliendo Crea script per tabella -> CREA in -> Nuova finestra di query.
2.	Modificare l'indice CLUSTERED in NONCLUSTERED e aggiungere l'opzione con WITH (MEMORY\_OPTIMIZED=ON).
3.	Se la tabella usa funzionalità non supportate, implementare le soluzioni alternative. In MSDN sono documentate le procedure per gestire le [funzionalità comunemente non supportate](https://msdn.microsoft.com/library/dn247639.aspx).
4.	Rinominare la tabella esistente in sp\_rename.
5.	Creare la nuova tabella con ottimizzazione per la memoria eseguendo lo script CREATE TABLE.
6.	Copiare i dati tramite l'istruzione seguente: ''INSERT INTO <new_memory_optimized_table> SELECT * FROM <old_disk_based_table>

## Passaggio 4 (facoltativo): Eseguire la migrazione di stored procedure

Connettersi al database SQL di Azure tramite [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) anteprima di settembre 2015 o versioni successive.

Identificare tutte le funzionalità non supportate nelle stored procedure compilate in modo nativo tramite l'esecuzione di [Assistente compilazione nativa](https://msdn.microsoft.com/library/dn284308.aspx) nella procedura precedente. Soluzioni alternative per funzionalità non supportate comuni sono documentate in [MSDN](https://msdn.microsoft.com/library/dn296678.aspx).

Due aspetti da considerare durante la migrazione di una stored procedure in modalità nativa:

- I moduli nativi richiedono le seguenti opzioni:

	- NATIVE\_COMPILATION
	- SCHEMABINDING



- I moduli nativi usano [blocchi ATOMICI](https://msdn.microsoft.com/library/dn452281.aspx) per la gestione delle transazioni, non sono necessarie istruzioni esplicite BEGIN TRAN/COMMIT/ROLLBACK.

Una tipica stored procedure compilata in modo nativo ha l'aspetto seguente:


   	    CREATE PROCEDURE schemaname.procedurename
   		@param1 type1, …
   		WITH NATIVE_COMPILATION, SCHEMABINDING
   		AS
   		BEGIN ATOMIC WITH (TRANSACTION ISOLATION LEVEL = SNAPSHOT, LANGUAGE = N'your_language')

   		…

   		END



Si noti che mentre SNAPSHOT è livello di isolamento della tabella con ottimizzazione per la memoria più comunemente usato, sono supportati anche REPEATABLE READ e SERIALIZABLE.

##### Seguire questa procedura per eseguire la migrazione di una stored procedure:

1.	Creare uno script per la stored procedure precedente, facendo clic con il pulsante destro del mouse sulla procedura e scegliendo Crea script per stored procedure -> CREA in -> Nuova finestra di query.
2.	Riscrivere l'intestazione della stored procedure usando il modello precedente e rimuovere le istruzioni BEGIN TRAN/ROLLBACK/COMMIT.
3.	Se la stored procedure usa funzionalità non supportate, implementare le soluzioni alternative. In MSDN sono documentate le procedure per gestire le [funzionalità comunemente non supportate](https://msdn.microsoft.com/library/dn296678.aspx).
4.	Usare DROP per eliminare la stored procedure o rinominare quella precedente con sp\_rename.
5.	Creare la nuova stored procedure compilata in modo nativo eseguendo lo script CREATE PROCEDURE.

## Passaggio 5: Eseguire il carico di lavoro
Eseguire il carico di lavoro di test con le tabelle con ottimizzazione per la memoria e stored procedure compilate in modo nativo e misurare il miglioramento delle prestazioni.

## Passaggi successivi

[Monitorare l'archiviazione in memoria](https://azure.microsoft.com/documentation/articles/sql-database-in-memory-oltp-monitoring/).

[Monitoraggio del database SQL di Azure tramite le visualizzazioni di gestione dinamica](sql-database-monitoring-with-dmvs.md)

<!---HONumber=Nov15_HO1-->