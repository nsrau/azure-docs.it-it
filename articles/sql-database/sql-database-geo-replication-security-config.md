<properties
	pageTitle="Configurazione della sicurezza per la replica geografica attiva o standard"
	description="Questo argomento descrive le considerazioni relative alla sicurezza per la gestione di scenari di replica geografica attiva o standard per il database SQL."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="10/22/2015"
	ms.author="jroth" />

# Configurazione della sicurezza per la replica geografica attiva o standard

## Panoramica
Questo argomento illustra i requisiti di autenticazione per configurare e controllare la [replica geografica attiva e standard](sql-database-geo-replication-overview.md) e i passaggi necessari per configurare l'accesso utente al database secondario. Per altre informazioni sull'uso della replica geografica, vedere [Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio](sql-database-disaster-recovery.md).

## Uso di utenti indipendenti
Con la [versione 12 del database SQL](sql-database-v12-whats-new.md), quest'ultimo ora supporta gli utenti indipendenti. A differenza degli utenti tradizionali per i quali deve essere eseguito il mapping agli account di accesso nel database master, un utente indipendente viene gestito completamente dal database stesso. Questo approccio presenta due vantaggi. Nello scenario della replica geografica, gli utenti possono continuare a connettersi al database secondario senza alcuna configurazione aggiuntiva, perché il database gestisce gli utenti. Dal punto di vista dell'accesso, questa configurazione offre anche vantaggi a livello di scalabilità e prestazioni. Per altre informazioni, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).

Nel caso di utenti indipendenti, se più database usano lo stesso account di accesso, è necessario gestire gli utenti separatamente per ogni database, ad esempio per la modifica di una password, invece di gestire l'account di accesso a livello di server.

>[AZURE.NOTE]Se si vuole modificare indipendentemente l'accesso in lettura del database primario e secondario, è necessario usare account di accesso e utenti tradizionali. Gli utenti indipendenti non possono essere gestiti nel database secondario indipendentemente da quello primario.

## Uso di account di accesso e utenti tradizionali
Se si usano account di accesso e utenti tradizionali, invece di utenti indipendenti, è necessario eseguire altri passaggi per assicurare che nel server di database secondario siano presenti gli stessi account di accesso. Le sezioni seguenti illustrano i passaggi da eseguire, oltre a considerazioni aggiuntive.

### Configurare l'accesso utente per il database secondario online
Per poter usare il database secondario come un database di sola lettura (database secondario online) o come una copia del database disponibile in una situazione di failover, il database secondario deve avere la configurazione di sicurezza appropriata.

I passaggi descritti più avanti in questo argomento possono essere completati correttamente solo dall'amministratore del server. Le autorizzazioni specifiche per ogni passaggio sono descritte più avanti in questo argomento.

La preparazione dell'accesso utente a un database secondario online di replica geografica attiva può essere eseguita in qualsiasi momento e prevede i tre passaggi descritti di seguito:

1. Determinare gli account di accesso che possono accedere al database primario.
2. Trovare il SID di questi account di accesso nel server di origine.
3. Generare gli account di accesso nel server di destinazione con il SID corrispondente del server di origine.

#### 1\. Determinare gli account di accesso che possono accedere al database primario:
Il primo passaggio della procedura consiste nel determinare quali account di accesso devono essere duplicati nel server di destinazione. A questo scopo, è necessario usare una coppia di istruzioni SELECT, una nel database master logico nel server di origine e una nel database primario stesso.

Solo l'amministratore del server o un membro del ruolo del server **LoginManager** può determinare gli account di accesso nel server di origine con l'istruzione SELECT seguente.

	SELECT [name], [sid] 
	FROM [sys].[sql_logins] 
	WHERE [type_desc] = 'SQL_Login'

Solo un membro del ruolo del database db\_owner, l'utente dbo o l'amministratore del server può determinare tutte le entità utente nel database primario.

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

#### 2\. Trovare il SID degli account di accesso identificati nel passaggio 1:
Confrontando l'output delle query illustrate nella sezione precedente e trovando una corrispondenza per i SID, è possibile eseguire il mapping dell'account di accesso del server all'utente del database. Gli account di accesso che hanno un utente del database con un SID corrispondente hanno anche l'accesso utente a quel database come entità utente.

La query seguente può essere usata per visualizzare tutte le entità utente e i relativi SID in un database. Questa query può essere eseguita solo da un membro del ruolo del database db\_owner o dall'amministratore del server.

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

>[AZURE.NOTE]Gli utenti **INFORMATION\_SCHEMA** e **sys** hanno SID *NULL*, mentre il SID dell'utente **guest** è **0x00**. Se il database è stato creato dall'amministratore del server invece che da un membro del ruolo **DbManager**, il SID dell'utente **dbo** potrebbe iniziare con *0x01060000000001648000000000048454*.

#### 3\. Generare gli account di accesso nel server di destinazione:
L'ultimo passaggio consiste nel generare gli account di accesso con i SID appropriati nel server o nei server di destinazione. La sintassi di base è la seguente.

	CREATE LOGIN [<login name>]
	WITH PASSWORD = <login password>,
	SID = <desired login SID>

>[AZURE.NOTE]Se si vuole concedere l'accesso utente al database secondario, ma non al database primario, è possibile modificare l'account di accesso utente nel server primario usando la sintassi seguente.
>
>ALTER LOGIN <login name> DISABLE
>
>DISABLE non modifica la password, pertanto è sempre possibile abilitare l'accesso, se necessario.

## Configurare l'accesso utente a seguito della terminazione di una relazione di copia continua
In caso di failover, la relazione di copia continua tra il database primario e quelli secondari deve essere arrestata. Per informazioni su questo processo, vedere [Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio](sql-database-disaster-recovery.md).

Nel caso di replica geografica standard l'utente non può accedere al database secondario offline, quindi le modifiche agli account utente devono essere apportate al momento della terminazione della relazione di copia continua.

Se i SID degli account di accesso non vengono duplicati nel server di destinazione, l'accesso al database secondario dopo la terminazione è limitato solo all'amministratore del server. Se l'utente che avvia la replica è membro del ruolo DbManager, non avrà accesso al database secondario, a meno che il SID dell'account di accesso non venga duplicato dal server di origine. Questa situazione si verifica per l'intera durata del processo di replica.

Quando la replica viene terminata, nell'ambito del processo di terminazione l'entità utente [dbo] viene modificata, in modo da corrispondere al SID dell'account di accesso relativo all'utente che ha avviato la replica e l'accesso di quell'utente viene ripristinato. Questo non avviene per gli altri utenti del database.

L'account utente e l'account di accesso associato usato per avviare l'operazione di terminazione devono essere presenti nel server e nel database di destinazione per essere certi che l'account utente possa accedere al database secondario una volta completata la terminazione.

Per altre informazioni sui passaggi necessari dopo un failover, vedere [Finalizzare il database SQL di Azure ripristinato](sql-database-recovered-finalize.md).

## Passaggi successivi
Per altre informazioni sulla replica geografica e su altre funzionalità di continuità aziendale del database SQL, vedere [Panoramica della continuità aziendale](sql-database-business-continuity.md).

<!---HONumber=Oct15_HO4-->