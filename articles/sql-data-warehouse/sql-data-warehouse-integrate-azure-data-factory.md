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
   ms.date="09/23/2015"
   ms.author="lodipalm"/>

# Usare Data factory di Azure con SQL Data Warehouse

Data factory di Azure offre un metodo completamente gestito per l'orchestrazione del trasferimento di dati e l'esecuzione di stored procedure in SQL Data Warehouse. In questo modo è possibile configurare e pianificare più facilmente procedure ETL (Extract, Transform, Load) complesse con SQL Data Warehouse. Per una panoramica più completa di Data factory di Azure, vedere [Documentazione di Data factory][].

## Spostamento dei dati 

Data factory di Azure consente lo spostamento di dati tra origini locali e diversi servizi di Azure. In generale, l'attuale integrazione con Data factory di Azure supporta lo spostamento dei dati da e verso le posizioni seguenti:

+ Archivio BLOB di Azure
+ Database SQL di Azure
+ Server SQL locale
+ SQL Server in IaaS

Per informazioni su come impostare un'attività di copia, vedere [Copiare i dati con Data factory di Azure](../data-factory/data-factory-data-movement-activities.md).

## Stored procedure
 Così come può essere usato per pianificare il trasferimento dei dati, Data factory di Azure può essere usato anche per orchestrare l'esecuzione di stored procedure. Ciò consente di creare pipeline più complesse ed estende la capacità di Data factory di Azure per sfruttare la potenza di elaborazione di SQL Data Warehouse.

## Passaggi successivi
Per una panoramica dell'integrazione, vedere [Panoramica dell'integrazione di SQL Data Warehouse](sql-data-warehouse-overview-integrate.md). Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse](sql-data-warehouse-overview-develop.md).

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: https://azure.microsoft.com/it-IT/documentation/articles/data-factory-azure-sql-connector/
[SQL Data Warehouse development overview]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-overview-integrate/

<!--MSDN references-->

<!--Other Web references-->
[Documentazione di Data factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Copy data with Azure Data Factory]: https://azure.microsoft.com/it-IT/documentation/articles/data-factory-data-movement-activities/

<!---HONumber=Oct15_HO1-->