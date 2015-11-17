<properties 
	pageTitle="Codice del buffer circolare XEvent per il database SQL | Microsoft Azure" 
	description="Fornisce un esempio di codice Transact-SQL reso semplice e veloce tramite l'uso della destinazione del buffer circolare nel database SQL di Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor="" 
	tags=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/22/2015" 
	ms.author="genemi"/>


# Codice di destinazione del buffer circolare per gli eventi estesi nel database SQL


Si desidera un esempio di codice completo per il modo più semplice e rapido per acquisire e segnalare informazioni per un evento esteso durante un test. La destinazione più semplice per i dati degli eventi estesi è la [destinazione del buffer circolare](http://msdn.microsoft.com/library/ff878182.aspx).


In questo argomento viene presentato un esempio di codice Transact-SQL che:


1. Crea una tabella con i dati a scopo dimostrativo.

2. Crea una sessione per un evento esteso esistente, ovvero **sqlserver.sql\_statement\_starting**.
 - L'evento è limitato a istruzioni SQL che contengono una determinata stringa di aggiornamento: **statement LIKE '%UPDATE tabEmployee%'**.
 - Sceglie di inviare l'output dell'evento a una destinazione di tipo buffer circolare, ovvero **package0.ring\_buffer**.

3. Avvia la sessione dell'evento.

4. Esegue un paio di semplici istruzioni SQL UPDATE.

5. Esegue un'istruzione SQL SELECT per recuperare l'output dell'evento dal buffer circolare.
 - **sys.dm\_xe\_database\_session\_targets** e altre viste a gestione dinamica (DMV) sono unite.

6. Arresta la sessione dell'evento.

7. Elimina la destinazione del buffer circolare, per rilasciare le relative risorse.

8. Elimina la sessione dell'evento e la tabella dimostrativa.


## Prerequisiti


- Un account e una sottoscrizione di Azure. È possibile iscriversi per una [versione di prova gratuita](http://azure.microsoft.com/pricing/free-trial/).


- Qualsiasi database in cui è possibile creare una tabella.
 - Facoltativamente, è possibile [creare un database dimostrativo **AdventureWorksLT**](sql-database-get-started.md) in pochi minuti.


- SQL Server Management Studio (ssms.exe), anteprima di agosto 2015 o versione successiva. È possibile scaricare la versione più recente di ssms.exe da:
 - [Un collegamento nell'argomento.](http://msdn.microsoft.com/library/mt238290.aspx)
 - [Un collegamento diretto al download.](http://go.microsoft.com/fwlink/?linkid=616025)
 - Microsoft consiglia di aggiornare periodicamente ssms.exe.


## Esempio di codice


Con alcune lievi modifiche, è possibile eseguire il seguente esempio di codice di buffer circolare nel database SQL di Azure o in Microsoft SQL Server. La differenza è la presenza del nodo '\_database' nel nome di alcune viste a gestione dinamica (DMV) nella clausola FROM nel passaggio 5. Ad esempio:

- sys.dm\_xe**\_database**\_session\_targets
- sys.dm\_xe\_session\_targets


&nbsp;


```
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
	(SELECT * FROM sys.objects
		WHERE type = 'U' and name = 'tabEmployee')
BEGIN
	DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
	EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
	EmployeeId           int                not null  identity(1,1),
	EmployeeKudosCount   int                not null  default 0,
	EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
	VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
	(SELECT * from sys.database_event_sessions
		WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
	DROP EVENT SESSION eventsession_gm_azuresqldb51
		ON DATABASE;
END
GO


CREATE
	EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	ADD EVENT
		sqlserver.sql_statement_starting
			(
			ACTION (sqlserver.sql_text)
			WHERE statement LIKE '%UPDATE tabEmployee%'
			)
	ADD TARGET
		package0.ring_buffer
			(SET
				max_memory = 500   -- Units of KB.
			);
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
		se.name  AS [session-name],
		ev.event_name,
		ac.action_name,
		st.target_name,
		se.session_source,
		st.target_data,
		CAST(st.target_data AS XML)  AS [target_data_XML]
	FROM
				   sys.dm_xe_database_session_event_actions  AS ac
		INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name            = ac.event_name
		                                                           AND ev.event_session_address = ac.event_session_address
		INNER JOIN sys.dm_xe_database_session_object_columns AS oc  ON oc.event_session_address = ac.event_session_address
		INNER JOIN sys.dm_xe_database_session_targets        AS st  ON st.event_session_address = ac.event_session_address
		INNER JOIN sys.dm_xe_database_sessions               AS se  ON ac.event_session_address = se.address
	WHERE
		oc.column_name = 'occurrence_number'
		AND
		se.name        = 'eventsession_gm_azuresqldb51'
		AND
		ac.action_name = 'sql_text'
	ORDER BY
		se.name,
		ev.event_name,
		ac.action_name,
		st.target_name,
		se.session_source;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;


## Contenuto del buffer circolare


È stato usato ssms.exe per eseguire l'esempio di codice.


Per visualizzare i risultati, è stato fatto clic sulla cella sotto l'intestazione di colonna **target\_data\_XML**.

Quindi, nel riquadro dei risultati, è stato fatto clic sulla cella sotto l'intestazione di colonna **target\_data\_XML**. In questo modo è stata creata un'altra scheda del file in ssms.exe, in cui è stato visualizzato il contenuto della cella del risultato, come XML.


L'output è illustrato nella sezione seguente. Sembra lungo, ma è composto solo da due elementi **<event>**.


&nbsp;


```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### Rilasciare le risorse usate dal buffer circolare


Al termine dell'uso del buffer circolare, è possibile rimuoverlo e rilasciare le relative risorse eseguendo un'istruzione **ALTER** simile alla seguente:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	DROP TARGET package0.ring_buffer;
GO
```


La definizione della sessione dell'evento viene aggiornata, ma non eliminata. Successivamente, è possibile aggiungere un'altra istanza del buffer circolare alla sessione dell'evento:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	ADD TARGET
		package0.ring_buffer
			(SET
				max_memory = 500   -- Units of KB.
			);
```


## Altre informazioni


L'argomento principale per gli eventi estesi in un database SQL di Azure è:


- [Considerazioni sugli eventi estesi nel database SQL](sql-database-xevent-db-diff-from-svr.md), che illustra alcuni aspetti degli eventi estesi che presentano differenze tra il database SQL di Azure e Microsoft SQL Server.


Altri argomenti con esempi di codice per gli eventi estesi sono disponibili ai collegamenti seguenti. È comunque necessario controllare regolarmente qualsiasi esempio per verificare se è destinato a Microsoft SQL Server o al database SQL di Azure. È quindi possibile decidere se sono necessarie alcune modifiche per eseguire l'esempio.


- Esempio di codice per il database SQL di Azure: [codice di destinazione del file evento per gli eventi estesi nel database SQL](sql-database-xevent-code-event-file.md)


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->

<!---HONumber=Nov15_HO2-->