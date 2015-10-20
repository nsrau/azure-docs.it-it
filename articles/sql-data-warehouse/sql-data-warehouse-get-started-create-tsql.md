<properties
	pageTitle="Creare un'istanza di SQL Data Warehouse con TSQL | Microsoft Azure"
	description="Informazioni su come creare un'istanza di Azure SQL Data Warehouse con TSQL"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/07/2015"
   ms.author="lodipalm"/>

#Creare SQL Data Warehouse con TSQL 

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-TSQL.md)
- [PowerShell](sql-data-warehouse-get-started-create-powershell.md)

Questo articolo illustrerà come creare un'istanza di SQL Data Warehouse usando Transact SQL. Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Visual Studio. Per una copia gratuita di Visual Studio, vedere la pagina [Download di Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).
- Un server SQL versione 12. Per creare SQL Data Warehouse, sarà necessario un server SQL versione 12. Se non è disponibile un server SQL 12, è consigliabile crearlo nel portale di anteprima per poter creare l'istanza di SQL Data Warehouse in un nuovo server.

Questo articolo non illustra come eseguire correttamente la configurazione e la connessione con Visual Studio. Per una descrizione completa di queste operazioni, vedere la documentazione relativa a [connessione e query][]. Per iniziare, sarà necessario aprire Esplora oggetti di SQL Server in Visual Studio e connettersi al server che si userà per creare l'istanza di SQL Data Warehouse. A questo punto sarà possibile creare un'istanza di SQL Data Warehouse eseguendo il comando seguente con il database master:

        CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>);

È anche possibile creare un'istanza di SQL Data Warehouse aprendo la riga di comando ed eseguendo quanto segue:

        sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>)"

Quando si eseguono le istruzioni TSQL precedenti, si notino i parametri MAXSIZE e SERVICE\_OBJECTIVE, che indicheranno le dimensioni di archiviazione iniziali e calcoleranno lo spazio assegnato all'istanza di Data Warehouse. MAXSIZE accetterà le dimensioni seguenti, ma si consiglia di scegliere dimensioni elevate per avere spazio sufficiente a contenere la crescita:

+ 250 GB
+ 500 GB
+ 750 GB
+ 1024 GB
+ 5120 GB
+ 10240 GB
+ 20480 GB
+ 30720 GB
+ 40960 GB
+ 51200 GB

SERVICE\_OBJECTIVE indicherà il numero di DWU iniziali dell'istanza e accetterà i valori seguenti:

+ DW100
+ DW200
+ DW300
+ DW400
+ DW500
+ DW600
+ DW1000
+ DW1200
+ DW1500
+ DW2000

Per informazioni sull'impatto di questi parametri sulla fatturazione, vedere la [pagina dei prezzi][].

## Passaggi successivi
Al termine del provisioning di SQL Data Warehouse, è possibile [caricare dati di esempio][] o vedere come eseguire le attività di [sviluppo][], [caricamento][] o [migrazione][].

[connessione e query]: ./sql-data-warehouse-get-started-connect-query.md
[migrazione]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-overview-migrate/
[sviluppo]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-overview-develop/
[caricamento]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-overview-load/
[caricare dati di esempio]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[pagina dei prezzi]: https://azure.microsoft.com/it-IT/pricing/details/sql-data-warehouse/

<!---HONumber=Oct15_HO3-->