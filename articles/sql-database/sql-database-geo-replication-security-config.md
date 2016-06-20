<properties
	pageTitle="Come gestire la sicurezza dopo il ripristino di emergenza"
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
	ms.date="05/10/2016"
	ms.author="carlrab" />

# Come gestire la sicurezza dopo il ripristino di emergenza

>[AZURE.NOTE] [Active Geo-Replication](sql-database-geo-replication-overview.md) è ora disponibile per tutti i database in tutti i livelli di servizio.

## Panoramica dei requisiti di autenticazione per il ripristino di emergenza

Questo argomento illustra i requisiti di autenticazione per configurare e controllare la [replica geografica attiva](sql-database-geo-replication-overview.md) e i passaggi necessari per configurare l'accesso utente al database secondario. Descrive anche come abilitare l'accesso al database ripristinato dopo il ripristino geografico. Per altre informazioni sulle opzioni di ripristino, vedere [Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio](sql-database-disaster-recovery.md).

## Ripristino di emergenza con gli utenti indipendenti

Con la [versione 12 del database SQL](sql-database-v12-whats-new.md), quest'ultimo ora supporta gli utenti indipendenti. A differenza degli utenti tradizionali per i quali deve essere eseguito il mapping agli account di accesso nel database master, un utente indipendente viene gestito completamente dal database stesso. Questo approccio presenta due vantaggi. Nello scenario di ripristino di emergenza, gli utenti possono continuare a connettersi al nuovo database primario o al database ripristinato con il ripristino geografico senza alcuna configurazione aggiuntiva, perché il database gestisce gli utenti. Dal punto di vista dell'accesso, questa configurazione offre anche vantaggi a livello di scalabilità e prestazioni. Per altre informazioni, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).

Il compromesso principale è rappresentato dal fatto che il processo di ripristino di emergenza su vasta scala è più complesso da gestire. Quando si dispone di più database che usano lo stesso account di accesso, la gestione delle credenziali usate dagli utenti indipendenti in più database può annullare il vantaggio rappresentato dagli utenti indipendenti. Ad esempio, i criteri di rotazione delle password richiedono l'applicazione coerente delle modifiche in più database anziché la modifica singola della password per l'accesso nel database master. Per questo motivo l'uso degli utenti indipendenti non è consigliato in presenza di molti database che usano lo stesso nome utente e la stessa password.

## Come configurare gli account di accesso e gli utenti

Se si usano account di accesso e utenti tradizionali, invece di utenti indipendenti, è necessario eseguire altri passaggi per assicurare che nel server di database secondario siano presenti gli stessi account di accesso. Le sezioni seguenti illustrano i passaggi da eseguire, oltre a considerazioni aggiuntive.

### Impostare l'accesso utente a un database secondario o ripristinato

Per fare in modo che il database secondario sia utilizzabile come database secondario di sola lettura e per garantire l'accesso appropriato al nuovo database primario o al database ripristinato con il ripristino geografico, è necessario che nel database master del server di destinazione sia presente la configurazione di sicurezza appropriata prima del ripristino.

Le autorizzazioni specifiche per ogni passaggio sono descritte più avanti in questo argomento.

La preparazione dell'accesso utente a un database secondario con replica geografica deve essere eseguita durante la configurazione della replica geografica. La preparazione dell'accesso utente ai database ripristinati con il ripristino geografico deve essere eseguita in qualsiasi momento quando il server originale è online, ad esempio nel contesto dell'analisi del ripristino di emergenza.

>[AZURE.NOTE] Se si esegue il failover o il ripristino geografico in un server che non presenta account di accesso configurati adeguatamente, l'accesso sarà limitato all'account di amministratore del server.

La configurazione degli account di accesso nel server di destinazione prevede i tre passaggi seguenti:


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

- Per altre informazioni sulla gestione dell'accesso al database e degli account di accesso, vedere [Protezione del database SQL: gestire l'accesso al database e la sicurezza degli account di accesso](sql-database-manage-logins.md).
- Per altre informazioni sugli utenti di database indipendente, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).

## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalizzare il database SQL di Azure ripristinato](sql-database-recovered-finalize.md)
- [Domande frequenti su continuità aziendale e ripristino di emergenza nel database SQL](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0608_2016-->