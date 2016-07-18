<properties
   pageTitle="Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse - TSQL | Microsoft Azure"
   description="Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse - TSQL"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/07/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# Introduzione a Transparent Data Encryption (TDE)


> [AZURE.SELECTOR]
- [Proteggere un database in SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md)
- [Introduzione al rilevamento delle minacce](sql-data-warehouse-security-threat-detection.md)
- [Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse](sql-data-warehouse-encryption-tde.md)
- [Introduzione a Transparent Data Encryption (TDE)](sql-data-warehouse-encryption-tde-tsql.md)
- [Servizio di controllo di Azure SQL Data Warehouse](sql-data-warehouse-auditing-overview.md)
- [SQL Data Warehouse - Supporto client di livello inferiore per controllo e maschera dati dinamica](sql-data-warehouse-auditing-downlevel-clients.md)


La funzionalità Transparent Data Encryption (TDE) di SQL Data Warehouse di Azure consente di proteggersi da attività dannose eseguendo in tempo reale la crittografia e la decrittografia dei database, dei backup associati e dei file di log delle transazioni inattivi, senza dover apportare modifiche all'applicazione.

TDE esegue la crittografia dell'archiviazione di un intero database usando una chiave simmetrica detta "chiave di crittografia del database". Nel database SQL la chiave di crittografia del database è protetta da un certificato server incorporato. Il certificato server incorporato è univoco per ogni server di database SQL. Microsoft ruota automaticamente questi certificati almeno ogni 90 giorni. L’algoritmo di crittografia utilizzato da SQL Data Warehouse è AES-256. Per una descrizione generale della funzionalità TDE, vedere [Transparent Data Encryption (TDE)].

##Abilitazione della crittografia

Per abilitare TDE per un SQL Data Warehouse, seguire questa procedura:

1. Connettere il database *master* sul server che ospita il database usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

##Disabilitazione della crittografia

Per disabilitare TDE per un SQL Data Warehouse, seguire questa procedura:

1. Connettere il database *master* usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

##Verifica della crittografia

Per verificare lo stato della crittografia per un SQL Data Warehouse, seguire questa procedura:

1. Connettere il database *master* o dell'istanza usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
SELECT
	[name],
	[is_encrypted]
FROM
	sys.databases;
```

Il risultato ```1``` indica un database crittografato, ```0``` indica un database non crittografato.

##Viste a gestione dinamica della crittografia  

- [sys.databases][]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys][]


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0706_2016-->