<properties
   pageTitle="Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse - TSQL | Microsoft Azure"
   description="Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse - TSQL"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="03/23/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Introduzione a Transparent Data Encryption (TDE)
> [AZURE.SELECTOR]
- [Portale di Azure classico](sql-data-warehouse-encryption-tde.md)
- [TSQL](sql-data-warehouse-encryption-tde-tsql.md)

La funzionalità Transparent Data Encryption (TDE) di SQL Data Warehouse di Azure consente di proteggersi da attività dannose eseguendo in tempo reale la crittografia e la decrittografia dei database, dei backup associati e dei file di log delle transazioni inattivi, senza dover apportare modifiche all'applicazione.

TDE esegue la crittografia dell'archiviazione di un intero database usando una chiave simmetrica detta "chiave di crittografia del database". Nel database SQL la chiave di crittografia del database è protetta da un certificato server incorporato. Il certificato server incorporato è univoco per ogni server di database SQL. Microsoft ruota automaticamente questi certificati almeno ogni 90 giorni. Per una descrizione generale della funzionalità TDE, vedere [Transparent Data Encryption (TDE)].

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


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0330_2016-->