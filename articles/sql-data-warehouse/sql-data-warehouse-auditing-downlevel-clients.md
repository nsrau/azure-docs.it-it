<properties
   pageTitle="SQL Data Warehouse supporto client di livello inferiore per controllo dati | Microsoft Azure"
   description="Informazioni sul supporto di client di livello inferiore di SQL Data Warehouse per il controllo dati"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/29/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# SQL Data Warehouse - Supporto client di livello inferiore per controllo e maschera dati dinamica

> [AZURE.SELECTOR]
- [Proteggere un database in SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md)
- [Introduzione al rilevamento delle minacce](sql-data-warehouse-security-threat-detection.md)
- [Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse](sql-data-warehouse-encryption-tde.md)
- [Introduzione a Transparent Data Encryption (TDE)](sql-data-warehouse-encryption-tde-tsql.md)
- [Servizio di controllo di Azure SQL Data Warehouse](sql-data-warehouse-auditing-overview.md)
- [SQL Data Warehouse - Supporto client di livello inferiore per controllo e maschera dati dinamica](sql-data-warehouse-auditing-downlevel-clients.md)


[Controllo](sql-data-warehouse-auditing-overview.md) funziona con i client SQL che supportano il reindirizzamento TDS.

Qualsiasi client che implementa TDS 7.4 deve supportare anche il reindirizzamento. Rappresentano un'eccezione JDBC 4.0, in cui non è del tutto supportata la funzionalità di reindirizzamento, e Tedious per Node.JS, in cui non è implementato il reindirizzamento.

Per i "client di livello inferiore", ad esempio quelli che supportano la versione 7.3 di TDS e inferiori, il nome di dominio completo del server nella stringa di connessione deve essere modificato:

Nome di dominio completo del server originale nella stringa di connessione: <*server name*>.database.windows.net

Nome di dominio completo del server modificato nella stringa di connessione: <*server name*>.database.**secure**.windows.net

Un elenco parziale di "client di livello inferiore" include:

- .NET 4.0 e versioni precedenti,
- ODBC 10.0 e versioni precedenti.
- JDBC (mentre JDBC supporta TDS 7.4, la funzionalità di reindirizzamento TDS non è del tutto supportata)
- Tedious (per Node.JS)

**Nota:** la modifica del nome di dominio completo del server citata in precedenza può risultare utile per applicare un criterio di controllo a livello di server SQL senza la necessità di una procedura di configurazione in ogni database (attenuazione temporanea).

<!---HONumber=AcomDC_0907_2016-->