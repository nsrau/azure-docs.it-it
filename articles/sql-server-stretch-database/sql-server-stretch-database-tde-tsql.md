<properties
   pageTitle="Abilitare Transparent Data Encryption (TDE) per Estensione database di SQL Server su Azure TSQL | Microsoft Azure"
   description="Abilitare Transparent Data Encryption (TDE) per Estensione database di SQL Server su Azure TSQL"
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
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>  

# Abilitare Transparent Data Encryption (TDE) per Estensione database su Azure (Transact-SQL)
> [AZURE.SELECTOR]
- [Portale di Azure](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

La funzionalità Transparent Data Encryption (TDE) consente di proteggersi da attività dannose eseguendo in tempo reale la crittografia e la decrittografia dei database, dei backup associati e dei file di log delle transazioni inattivi, senza dover apportare modifiche all'applicazione.

TDE esegue la crittografia dell'archiviazione di un intero database usando una chiave simmetrica detta "chiave di crittografia del database". La chiave di crittografia del database è protetta da un certificato server incorporato. Il certificato server incorporato è univoco per ogni server Azure. Microsoft ruota automaticamente questi certificati almeno ogni 90 giorni. Per una descrizione generale della funzionalità TDE, vedere [Transparent Data Encryption (TDE)].

##Abilitazione della crittografia

Per abilitare la funzionalità TDE per un database di Azure che archivia i dati migrati da un database SQL Server con Estensione abilitata, eseguire le operazioni seguenti:

1. Connettere il database *master* sul server Azure che ospita il database usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

##Disabilitazione della crittografia

Per disabilitare TDE per un database di Azure che archivia i dati migrati da un database SQL Server con Estensione abilitata, eseguire le operazioni seguenti:

1. Connettere il database *master* usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

##Verifica della crittografia

Per verificare lo stato della crittografia per un database di Azure che archivia i dati migrati da un database SQL Server con Estensione abilitata, eseguire le operazioni seguenti:

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

<!---HONumber=AcomDC_0810_2016-->
