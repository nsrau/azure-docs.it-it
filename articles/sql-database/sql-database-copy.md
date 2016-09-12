<properties
	pageTitle="Copiare un database SQL di Azure | Microsoft Azure"
	description="Creare una copia di un database SQL di Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/16/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>  



# Copiare un database SQL di Azure

> [AZURE.SELECTOR]
- [Panoramica](sql-database-copy.md)
- [Portale di Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

È possibile utilizzare i [backup automatici del database SQL](sql-database-automated-backups.md) di Azure per creare una copia del database SQL. Durante l'operazione viene copiata la coda del log di transazione e quindi vengono usati i backup completo, differenziale e dei log di transazione che fanno parte dei backup automatici per creare un backup coerente con il database di origine al momento del backup del log di transazione finale.

È possibile creare la copia del database nello stesso server o su un altro server. Il livello di servizio e il livello di prestazioni (piano tariffario) della copia del database sono gli stessi del database di origine. Al termine del processo di copia, la copia diventa un database indipendente e completamente funzionante. Gli account di accesso, gli utenti e le autorizzazioni possono essere gestiti in modo indipendente.

Quando si copia un database nello stesso server logico, è possibile usare gli stessi account di accesso per entrambi i database. L'entità di sicurezza usata per copiare il database diventa il proprietario del database (DBO) nel nuovo database. Tutti gli utenti del database, le relative autorizzazioni e i relativi identificatori di sicurezza (SID) vengono copiati nella copia del database.

Quando la copia del database viene eseguita su un server logico diverso, l'entità di sicurezza sul nuovo server diventa il proprietario del database nel nuovo database. Gli utenti del database che sono utenti indipendenti possono essere utilizzati nel database copiato. Quando il database viene copiato in un nuovo server, gli utenti basati su account di accesso potrebbero non essere funzionanti perché gli account di accesso non esistono nel nuovo server e, qualora esistano, i SID potrebbero non corrispondere. Dopo che il nuovo database è online nel server di destinazione, usare l'istruzione [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) per modificare il mapping degli utenti del nuovo database agli account di accesso nel server di destinazione. Per risolvere gli utenti isolati, vedere [Risolvere i problemi relativi agli utenti isolati (SQL Server)](https://msdn.microsoft.com/library/ms175475.aspx).


Per copiare un database SQL è necessario quanto segue:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione di Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Un database SQL da copiare. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).

## Passaggi successivi

- Vedere [Copiare un database SQL di Azure tramite il portale di Azure](sql-database-copy-portal.md) per copiare un database tramite il portale di Azure.
- Vedere [Copiare un database SQL di Azure con PowerShell](sql-database-copy-powershell.md) per copiare un database tramite PowerShell.
- Vedere [Copiare un database SQL di Azure con Transact-SQL](sql-database-copy-transact-sql.md) per copiare un database usando Transact-SQL.
- Vedere [Come gestire la sicurezza dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) per informazioni sulla gestione di utenti e account di accesso durante la copia di un database in un server logico diverso.



## Risorse aggiuntive

- [Gestire gli accessi](sql-database-manage-logins.md)
- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)
- [Esportare il database in un BACPAC](sql-database-export.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0831_2016-->