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
	ms.date="08/05/2016"
	ms.author="douglasl"/>

# Abilitare Database Estensione per una tabella

Per configurare una tabella per Database Estensione, selezionare **Estensione | Abilita** per una tabella in SQL Server Management Studio per aprire la procedura guidata **Abilitare la tabella per l’estensione**. È anche possibile usare Transact-SQL per abilitare l'estensione database in una tabella esistente o per creare una nuova tabella con l'estensione database abilitata.

-   Se si archiviano dati inattivi in una tabella separata, è possibile eseguire la migrazione dell'intera tabella.

-   Se la tabella contiene sia dati usati molto di frequente che dati usati raramente è possibile specificare una funzione di filtro per selezionare le righe per la migrazione.

**Prerequisiti**. Se si seleziona **Stretch | Abilita** per una tabella e non è stata ancora abilitata l'estensione database per il database, la procedura guidata configura prima il database per l'estensione database. Seguire la procedura illustrata nell'articolo [Introduzione all'esecuzione della procedura guidata Abilitare il database per l'estensione](sql-server-stretch-database-wizard.md) anziché quella descritta in questo argomento.

**Autorizzazioni**. Per abilitare Database Estensione in un database o una tabella è necessario disporre delle autorizzazioni db\_owner. Anche per abilitare Database Estensione in una tabella sono necessarie le autorizzazioni ALTER nella tabella.

 >   [AZURE.NOTE] Se in seguito si disabilita Estensione database, tenere presente che la disabilitazione di Estensione database per una tabella o un database non elimina l'oggetto remoto. Per eliminare la tabella o il database remoto è necessario rimuoverli usando il portale di gestione di Azure. Gli oggetti remoti continuano a comportare l'addebito di costi in Azure fino a quando non vengono eliminati manualmente.
 
## <a name="EnableWizardTable"></a>Usare la procedura guidata per abilitare Estensione database in una tabella
**Avviare la procedura guidata**

1.  In SQL Server Management Studio, in Esplora oggetti, selezionare la tabella in cui si desidera abilitare l'estensione.

2.  Fare clic con il pulsante destro del mouse e selezionare **Stretch** e quindi **Abilita** per avviare la procedura guidata.

**Introduzione**

Esaminare lo scopo della procedura guidata e i prerequisiti.

**Selezionare le tabelle di database**

Verificare che la tabella da abilitare sia visualizzata e selezionata.

È possibile eseguire la migrazione di un'intera tabella o specificare una funzione di filtro semplice nella procedura guidata. Per usare un tipo di funzione di filtro diversa per selezionare le righe per la migrazione, completare una delle operazioni seguenti.

-   Chiudere la procedura guidata ed eseguire l'istruzione ALTER TABLE per abilitare l'estensione per la tabella e specificare una funzione di filtro.

-   Eseguire l'istruzione ALTER TABLE per specificare una funzione di filtro dopo aver chiuso la procedura guidata. Per i passaggi necessari, vedere [Aggiungere una funzione del filtro dopo l'esecuzione della procedura guidata](sql-server-stretch-database-predicate-function.md#addafterwiz).

La sintassi di ALTER TABLE è descritta più avanti in questo argomento.

**Riepilogo**

Esaminare i valori immessi dall'utente e le opzioni selezionate nella procedura guidata. Selezionare quindi **Fine** per abilitare l'estensione.

**Risultati**

Esaminare i risultati.

## <a name="EnableTSQLTable"></a>Usare Transact-SQL per abilitare Estensione database in una tabella
È possibile usare Transact-SQL per abilitare l'estensione database in una tabella esistente o per creare una nuova tabella con l'estensione database abilitata.

### Opzioni
Usare le opzioni seguenti quando si esegue CREATE TABLE o ALTER TABLE per abilitare l'estensione database in una tabella.

-   Facoltativamente, è possibile usare la clausola `FILTER_PREDICATE = <function>` per specificare una funzione per selezionare le righe di cui eseguire la migrazione se la tabella contiene dati sia attivi che inattivi. Il predicato deve chiamare una funzione inline con valori di tabella. Per altre informazioni, vedere [Usare una funzione di filtro per selezionare righe di cui eseguire la migrazione](sql-server-stretch-database-predicate-function.md). Se non si specifica una funzione del filtro, viene eseguita la migrazione dell'intera tabella.

    >   [AZURE.NOTE] Se si specifica una funzione del filtro con esecuzione inadeguata, la migrazione dei dati sarà a sua volta inadeguata. Estensione database applica la funzione del filtro alla tabella tramite l'operatore CROSS APPLY.

-   Specificare `MIGRATION_STATE = OUTBOUND` per avviare immediatamente la migrazione dei dati o `MIGRATION_STATE = PAUSED` per rimandarne l'avvio.

### Abilitare l'estensione database per una tabella esistente
Per configurare una tabella esistente per l'estensione database, eseguire il comando ALTER TABLE.

Ecco un esempio in cui viene eseguita la migrazione dell'intera tabella e viene avviata immediatamente la migrazione dei dati.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <table name>  
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;  
GO
```
Ecco un esempio in cui viene eseguita la migrazione delle sole righe identificate dalla funzione inline con valori di tabella `dbo.fn_stretchpredicate` e viene rimandata la migrazione dei dati. Per altre informazioni sulla funzione di filtro, vedere [Usare una funzione di filtro per selezionare righe di cui eseguire la migrazione](sql-server-stretch-database-predicate-function.md).

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <table name>  
    SET ( REMOTE_DATA_ARCHIVE = ON (  
        FILTER_PREDICATE = dbo.fn_stretchpredicate(),  
        MIGRATION_STATE = PAUSED ) ) ;  
 GO
```

Per altre informazioni, vedere [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

### Creare una nuova tabella con l'estensione database abilitata
Per creare una nuova tabella con l'estensione database abilitata, eseguire il comando CREATE TABLE.

Ecco un esempio in cui viene eseguita la migrazione dell'intera tabella e viene avviata immediatamente la migrazione dei dati.

```tsql
USE <Stretch-enabled database name>;
GO
CREATE TABLE <table name>
    ( ... )  
    WITH ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;  
GO
```

Ecco un esempio in cui viene eseguita la migrazione delle sole righe identificate dalla funzione inline con valori di tabella `dbo.fn_stretchpredicate` e viene rimandata la migrazione dei dati. Per altre informazioni sulla funzione di filtro, vedere [Usare una funzione di filtro per selezionare righe di cui eseguire la migrazione](sql-server-stretch-database-predicate-function.md).

```tsql
USE <Stretch-enabled database name>;
GO
CREATE TABLE <table name>
    ( ... )  
    WITH ( REMOTE_DATA_ARCHIVE = ON (  
        FILTER_PREDICATE = dbo.fn_stretchpredicate(),  
        MIGRATION_STATE = PAUSED ) ) ;  
GO  
```

Per altre informazioni, vedere [CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx).


## Vedere anche

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

[CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx)

<!---HONumber=AcomDC_0810_2016-->