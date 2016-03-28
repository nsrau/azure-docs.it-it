<properties
	pageTitle="Abilitare Database Estensione per una tabella | Microsoft Azure"
	description="Scoprire come configurare una tabella per Database Estensione."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Abilitare Database Estensione per una tabella

Per configurare una tabella per Database Estensione, selezionare **Estensione | Abilita** per una tabella in SQL Server Management Studio per aprire la procedura guidata **Abilitare la tabella per l’estensione**. È possibile anche usare Transact-SQL per abilitare Database Estensione in una tabella.

-   Se si archiviano dati cronologici in una tabella separata, è possibile eseguire la migrazione dell'intera tabella.

-   Se la tabella contiene dati attuali e cronologici, è possibile specificare un predicato del filtro per selezionare le righe di cui eseguire la migrazione. In CTP 3.1 tramite RC0, l'opzione che consente di specificare un predicato del filtro non è disponibile nella procedura guidata Abilita estensione per la tabella. È necessario usare l'istruzione ALTER TABLE per configurare una tabella per Database Estensione con questa opzione. Per altre informazioni, vedere [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

**Prerequisiti**. Se si seleziona **Estensione | Abilita** per una tabella e non è stato ancora abilitato Database Estensione per il database, la procedura guidata configura prima il database per Database Estensione. Seguire la procedura illustrata nell'articolo relativo alla [procedura guidata per l'abilitazione dell'estensione per il database](sql-server-stretch-database-wizard.md) anziché quella descritta qui.

**Autorizzazioni**. Per abilitare Database Estensione in un database o una tabella è necessario disporre delle autorizzazioni db\_owner. Anche per abilitare Database Estensione in una tabella sono necessarie le autorizzazioni ALTER nella tabella.

## <a name="EnableWizardTable"></a>Con la procedura guidata abilitare Database Estensione in una tabella
**Avviare la procedura guidata**
1.  In SQL Server Management Studio, in Esplora oggetti, selezionare la tabella in cui si desidera abilitare l'estensione.

2.  Fare clic con il pulsante destro del mouse e selezionare **Estensione**, quindi **Abilita** per avviare la procedura guidata.

**Introduzione** Ricontrollare lo scopo della procedura guidata e i prerequisiti.

**Selezionare le tabelle di database** Verificare che la tabella da abilitare sia visualizzata e selezionata.

In CTP 3.1 tramite RC0, è possibile eseguire solo la migrazione dell'intera tabella con la procedura guidata. Se si desidera specificare un predicato per selezionare le righe ed eseguire la migrazione da una tabella contenente dati attuali e cronologici, eseguire l'istruzione ALTER TABLE per specificare un predicato dopo aver chiuso la procedura guidata o uscire dalla procedura guidata ed eseguire l'istruzione ALTER TABLE come illustrato più avanti in questo argomento.

**Riepilogo** Esaminare i valori immessi dall'utente e le opzioni selezionate nella procedura guidata. Selezionare quindi **Fine** per abilitare l'estensione.

**Risultati** Esaminare i risultati.

## <a name="EnableTSQLTable"></a>Con Transact-SQL abilitare Database Estensione in una tabella
Per configurare una tabella per Database Estensione, eseguire il comando ALTER TABLE.

1.  È possibile usare la clausola `FILTER_PREDICATE = <predicate>` per specificare un predicato e selezionare le righe di cui eseguire la migrazione se la tabella contiene sia dati attuali che cronologici. Il predicato deve chiamare una funzione inline con valori di tabella. Per altre informazioni, vedere l'articolo su come [scrivere una funzione con valori di tabella inline per selezionare le righe (Database Estensione)](sql-server-stretch-database-predicate-function.md). Se non si specifica alcun predicato del filtro, viene eseguita la migrazione dell'intera tabella.

    > [!IMPORTANTE] Se si specifica un predicato del filtro con esecuzione inadeguata, la migrazione dei dati sarà a sua volta inadeguata. Database Estensione applica il predicato del filtro alla tabella tramite l'operatore CROSS APPLY.

    In CTP 3.1 tramite RC0, questa opzione non è disponibile nella procedura guidata per l'abilitazione dell'estensione per il database. È necessario usare l'istruzione ALTER TABLE per configurare una tabella per Database Estensione con questa opzione. Per altre informazioni, vedere ALTER TABLE (Transact-SQL) https://msdn.microsoft.com/library/ms190273.aspx.

2.  Specificare `MIGRATION_STATE = OUTBOUND` per avviare immediatamente la migrazione dei dati o `MIGRATION_STATE = PAUSED` per rimandarne l'avvio.

Ecco un esempio in cui viene eseguita la migrazione dell'intera tabella e viene avviata immediatamente la migrazione dei dati.

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;
GO;
```
Ecco un esempio in cui viene eseguita la migrazione solo delle righe identificate dalla funzione con valori di tabella inline `dbo.fn_stretchpredicate` e viene posticipata la migrazione dei dati. Per altre informazioni sul predicato del filtro, vedere l'articolo su come [scrivere una funzione con valori di tabella inline per selezionare le righe (Database Estensione)](sql-server-stretch-database-predicate-function.md).

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = PAUSED ) )
```

## Vedere anche

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0316_2016-->