<properties
	pageTitle="Configurazione della sicurezza per la replica geografica attiva"
	description="Questo argomento descrive le considerazioni relative alla sicurezza per la gestione di scenari di replica geografica attiva per il database SQL."
	services="sql-database"
	documentationCenter="na"
	authors="carlrabeler"
	manager="jhubbard"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="04/27/2016"
	ms.author="carlrab" />

# Configurazione della sicurezza per la replica geografica

>[AZURE.NOTE] [Active Geo-Replication](sql-database-geo-replication-overview.md) è ora disponibile per tutti i database in tutti i livelli di servizio.

## Panoramica dei requisiti di autenticazione per la replica geografica attiva
Questo argomento illustra i requisiti di autenticazione per configurare e controllare la [replica geografica attiva](sql-database-geo-replication-overview.md) e i passaggi necessari per configurare l'accesso utente al database secondario. Per altre informazioni sull'uso della replica geografica, vedere [Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio](sql-database-disaster-recovery.md).

## Uso della replica geografica attiva con utenti indipendenti
Con la [versione 12 del database SQL](sql-database-v12-whats-new.md), quest'ultimo ora supporta gli utenti indipendenti. A differenza degli utenti tradizionali per i quali deve essere eseguito il mapping agli account di accesso nel database master, un utente indipendente viene gestito completamente dal database stesso. Questo approccio presenta due vantaggi. Nello scenario della replica geografica, gli utenti possono continuare a connettersi al database secondario senza alcuna configurazione aggiuntiva, perché il database gestisce gli utenti. Dal punto di vista dell'accesso, questa configurazione offre anche vantaggi a livello di scalabilità e prestazioni. Per altre informazioni, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).

Quando si dispone di più database che usano lo stesso account di accesso, la gestione delle credenziali usate dagli utenti indipendenti in più database può annullare il vantaggio rappresentato dagli utenti indipendenti. Ad esempio, quando la password viene modificata, sarà necessario applicare separatamente la modifica per l'utente indipendente in ogni database, anziché cambiare la password dell'account una sola volta a livello del server. Per tale motivo l'uso degli utenti indipendenti non è consigliato se si hanno molti database che usano lo stesso nome utente e la stessa password.

## Uso di utenti e account di accesso con la replica geografica attiva
Se si usano account di accesso e utenti tradizionali invece di utenti indipendenti, è necessario eseguire passaggi aggiuntivi per garantire che gli stessi account di accesso esistano anche nel server del database secondario. Le sezioni seguenti illustrano i passaggi da eseguire, oltre a considerazioni aggiuntive.

### Impostare l'accesso utente a un database secondario
Per poter usare il database secondario come database secondario di sola lettura o come database primario disponibile dopo un failover, il database deve avere la configurazione di sicurezza appropriata.

L'amministratore del server o gli utenti con autorizzazioni appropriate possono completare i passaggi di configurazione descritti in questo argomento. Le autorizzazioni specifiche per ogni passaggio sono descritte più avanti in questo argomento.

La preparazione dell'accesso utente a un database secondario online di replica geografica attiva può essere eseguita in qualsiasi momento e prevede i tre passaggi descritti di seguito:

1. Determinare gli account di accesso che possono accedere al database primario.
2. Trovare il SID di questi account di accesso nel server di origine.
3. Creare gli account di accesso nel server di destinazione con il SID corrispondente del server di origine.

>[AZURE.NOTE] Se gli account di accesso nel server di destinazione non sono mappati correttamente agli utenti nel database secondario, l'accesso a quest'ultimo come database di sola lettura o l'accesso al nuovo database primario dopo il failover sarà consentito solo all'amministratore del server.

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

>[AZURE.NOTE] Gli utenti **INFORMATION\_SCHEMA** e **sys** hanno SID *NULL*, mentre il SID dell'utente **guest** è **0x00**. Se il database è stato creato dall'amministratore del server invece che da un membro del ruolo **DbManager**, il SID dell'utente **dbo** potrebbe iniziare con *0x01060000000001648000000000048454*.

#### 3\. Creare gli account di accesso nel server di destinazione:
L'ultimo passaggio consiste nel generare gli account di accesso con i SID appropriati nel server o nei server di destinazione. La sintassi di base è la seguente.

	CREATE LOGIN [<login name>]
	WITH PASSWORD = <login password>,
	SID = <desired login SID>

>[AZURE.NOTE] Se si vuole concedere l'accesso utente al database secondario, ma non al database primario, è possibile modificare l'account di accesso utente nel server primario usando la sintassi seguente.
>
>ALTER LOGIN <login name> DISABLE
>
>DISABLE non modifica la password, pertanto è sempre possibile abilitare l'accesso, se necessario.

## Passaggi successivi
Per altre informazioni sulla replica geografica attiva, vedere [Replica geografica attiva](sql-database-geo-replication-overview.md).


## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalizzare il database SQL di Azure ripristinato](sql-database-recovered-finalize.md)
- [Domande frequenti su continuità aziendale e ripristino di emergenza nel database SQL](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0504_2016-->