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
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Creare un database di SQL Data Warehouse usando Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Portale di Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Questo articolo mostra come creare un database di SQL Data Warehouse con Transact-SQL (TSQL).

## Prima di iniziare

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Un server SQL logico versione 12. Per creare SQL Data Warehouse, sarà necessario un sistema SQL Server V12. Se non è presente un sistema SQL Server V12 logico, vedere **Configurare e creare un server** nell'articolo [Come creare un database di SQL Data Warehouse dal portale di Azure][].
- Visual Studio. Per una copia gratuita di Visual Studio, vedere la pagina [Download di Visual Studio][].


> [AZURE.NOTE] La creazione di un nuovo database di SQL Data Warehouse può avere come risultato un nuovo servizio fatturabile. Per informazioni dettagliate sui prezzi, vedere [Prezzi di SQL Data Warehouse][].

## Creare un database con Visual Studio

Se non si ha familiarità con Visual Studio, vedere l'articolo [Connessione a SQL Data Warehouse con Visual Studio][]. Per iniziare, aprire Esplora oggetti di SQL Server in Visual Studio e connettersi al server che ospiterà il database di SQL Data Warehouse. Dopo aver stabilito la connessione, è possibile creare un database di SQL Data Warehouse eseguendo il comando SQL seguente sul database **master**. Questo comando crea il database MySqlDwDb con un obiettivo di servizio DW400 e permette un aumento delle dimensioni del database fino al limite massimo di 10 TB.

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Creare un database con sqlcmd

In alternativa, è possibile usare lo stesso comando con sqlcmd eseguendo quanto segue al prompt dei comandi.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

I parametri **MAXSIZE** e **SERVICE\_OBJECTIVE** specificano lo spazio massimo su disco che il database può usare e le risorse di calcolo allocate all'istanza di Data Warehouse. L'obiettivo di servizio è essenzialmente un'allocazione di CPU e memoria in grado di ridimensionarsi in modo lineare in base alle dimensioni della DWU.

Il parametro MAXSIZE può essere compreso tra 250 GB e 60 TB. L'obiettivo di servizio può essere compreso tra DW100 e DW2000. Per un elenco completo di tutti i valori validi per MAXSIZE e SERVICE\_OBJECTIVE, vedere la documentazione di MSDN per [CREATE DATABASE][]. MAXSIZE e SERVICE\_OBJECTIVE possono entrambi essere modificati anche con un comando T-SQL [ALTER DATABASE][]. Prestare attenzione quando si modifica il parametro SERVICE\_OBJECTIVE, perché questa operazione provoca un riavvio dei servizi e il conseguente annullamento delle query in corso. La modifica di MAXSIZE non richiede questa cautela, perché si tratta di una semplice operazione sui metadati.

## Passaggi successivi
Al termine del provisioning di SQL Data Warehouse, è possibile [caricare dati di esempio][] o vedere come eseguire le attività di [sviluppo][], [caricamento][] o [migrazione][].

<!--Article references-->
[Come creare un database di SQL Data Warehouse dal portale di Azure]: sql-data-warehouse-get-started-provision.md
[Connessione a SQL Data Warehouse con Visual Studio]: sql-data-warehouse-get-started-connect.md
[migrazione]: sql-data-warehouse-overview-migrate.md
[sviluppo]: sql-data-warehouse-overview-develop.md
[caricamento]: sql-data-warehouse-overview-load.md
[caricare dati di esempio]: sql-data-warehouse-get-started-manually-load-samples.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Prezzi di SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Download di Visual Studio]: https://www.visualstudio.com/downloads/download-visual-studio-vs

<!---HONumber=AcomDC_0427_2016-->