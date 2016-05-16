<properties
   pageTitle="Gestire le operazioni di scalabilità per Azure SQL Data Warehouse (REST) | Microsoft Azure"
   description="Attività delle API REST per scalare orizzontalmente le prestazioni per Azure SQL Data Warehouse. Modificare le risorse di calcolo cambiando il numero di DWU. In alternativa, sospendere e riprendere le risorse di calcolo per ridurre i costi."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Gestire le operazioni di scalabilità per Azure SQL Data Warehouse (REST)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-overview-scalability.md)
- [Portale](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)

La scalatura orizzontale elastica delle risorse di calcolo e di memoria consente di soddisfare le variazioni di domanda del carico di lavoro e di contenere i costi, riducendo le risorse durante i periodi non di picco.

Questo gruppo di attività usa le API REST per:

- Scalare le prestazioni modificando le DWU
- Sospendere l'attività delle risorse di calcolo
- Ripristinare l'attività delle risorse di calcolo

Per altre informazioni, vedere [Panoramica sulla scalabilità delle prestazioni][].

<a name="scale-performance-bk"></a>

## Scalare le prestazioni

[AZURE.INCLUDE [Descrizione della scalatura di DWU di SQL Data Warehouse](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Per modificare le DWU usare l'API REST [Create or Update Database][] (Creare o aggiornare il database). L'esempio seguente imposta l'obiettivo del livello di servizio su DW1000 per il database MySQLDW ospitato nel server MyServer. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## Sospendere il calcolo

[AZURE.INCLUDE [Descrizione della sospensione di SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Per sospendere un database, usare l'API REST [Pause Database][] (Sospendere il database). L'esempio seguente sospende il database Database02 ospitato sul server Server01. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## Riprendere il calcolo

[AZURE.INCLUDE [Descrizione della riattivazione di SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]

Per attivare un database, usare l'API REST [Resume Database][] (Riprendere il database). L'esempio seguente avvia il database Database02 ospitato sul server Server01. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## Passaggi successivi

Per altre attività di gestione, vedere [Panoramica della gestione][].

<!--Image references-->

<!--Article references-->
[Panoramica della gestione]: ./sql-data-warehouse-overview-manage.md
[Panoramica sulla scalabilità delle prestazioni]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->
[Pause Database]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[Resume Database]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[Create or Update Database]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->