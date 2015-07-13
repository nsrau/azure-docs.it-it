<properties
   pageTitle="Usare Data factory di Azure con SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l'uso di Data factory di Azure con Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="lodipalm"/>

# Usare Data factory di Azure con SQL Data Warehouse

Data factory di Azure offre un metodo completamente gestito per l'orchestrazione del trasferimento di dati e l'esecuzione di stored procedure in SQL Data Warehouse. In questo modo è possibile configurare e pianificare più facilmente procedure ETL (Extract, Transform, Load) complesse con SQL Data Warehouse. Per una panoramica più completa di Data factory di Azure, vedere [Documentazione di Data factory][].

## Spostamento dei dati 

Data factory di Azure consente lo spostamento di dati tra origini locali e diversi servizi di Azure. In generale, l'attuale integrazione con Data factory di Azure supporta lo spostamento dei dati da e verso le posizioni seguenti:

+ Archivio BLOB di Azure
+ Database SQL di Azure
+ Server SQL locale
+ SQL Server in IaaS

Per informazioni su come impostare un'attività di copia, vedere [Copiare i dati con Data factory di Azure][].

> [AZURE.NOTE]Attualmente non è possibile usare Data factory di Azure per trasferire dati in colonne non nullable in SQL Data Warehouse.

## Stored procedure
 Così come può essere usato per pianificare il trasferimento dei dati, Data factory di Azure può essere usato anche per orchestrare l'esecuzione di stored procedure. Ciò consente di creare pipeline più complesse ed estende la capacità di Data factory di Azure per sfruttare la potenza di elaborazione di SQL Data Warehouse.

## Passaggi successivi
Per una panoramica dell'integrazione, vedere [Panoramica dell'integrazione di SQL Data Warehouse][]. Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->

[Copiare i dati con Data factory di Azure]: ./data-factory-copy-activity/
[Panoramica sullo sviluppo per SQL Data Warehouse]: ./sql-data-warehouse-overview-develop/
[Panoramica dell'integrazione di SQL Data Warehouse]: ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
[Documentazione di Data factory]: https://azure.microsoft.com/documentation/services/data-factory/

<!---HONumber=July15_HO1-->