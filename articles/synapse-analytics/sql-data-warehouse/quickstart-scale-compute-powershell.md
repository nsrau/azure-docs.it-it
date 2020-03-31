---
title: Ridimensionare le risorse di calcolo per il pool SQL di Synapse (Azure PowerShell)
description: È possibile ridimensionare le risorse di calcolo per il pool SQL di Synapse (data warehouse) con Azure PowerShell.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c7ec8db212a24f1f23f393e4cb0e7f4150605a56
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350785"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-azure-powershell"></a>Guida introduttiva: Ridimensionare le risorse di calcolo per il pool SQL di Synapse con Azure PowerShell

È possibile ridimensionare le risorse di calcolo per il pool SQL di Synapse (data warehouse) con Azure PowerShell. [aumentandone il numero](sql-data-warehouse-manage-compute-overview.md) per ottenere prestazioni migliori o riducendolo per diminuire i costi. 

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Questa guida di avvio rapido presuppone che l'utente abbia già un pool SQL che è possibile ridimensionare. Se è necessario crearne uno, fare riferimento a [Creare e connettere - portale](create-data-warehouse-portal.md) per creare un pool SQL denominato **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e seguire le indicazioni visualizzate.

```powershell
Connect-AzAccount
```

Per vedere quale sottoscrizione si sta usando, eseguire [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Se è necessario usare una sottoscrizione diversa da quella predefinita, eseguire [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Cercare informazioni sul data warehouse

Individuare il nome del database, il nome del server e il gruppo di risorse del data warehouse di cui si prevede di sospendere e riprendere l'esecuzione.

Seguire questa procedura per trovare le informazioni sulla posizione del data warehouse.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Azure Synapse Analytics (in precedenza SQL DW)** nella pagina di spostamento sinistra del portale di Azure.
3. Selezionare **mySampleDataWarehouse** nella pagina **Azure Synapse Analytics (in precedenza SQL DW)** per aprire il data warehouse.

    ![Nome del server e gruppo di risorse](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. Annotare il nome del data warehouse che verrà usato come nome del database. Tenere presente che un data warehouse è un tipo di database. Annotare anche il nome del server e il gruppo di risorse. Il nome del server e il nome del gruppo di risorse verranno usati nei comandi sospensione e ripresa.
5. Usare solo la prima parte del nome del server nei cmdlet di PowerShell. Nell'immagine precedente il nome completo del server è sqlpoolservername.database.windows.net. Come nome del server nel cmdlet PowerShell si usa **sqlpoolservername**.

## <a name="scale-compute"></a>Ridimensionare le risorse di calcolo

Nel pool SQL è possibile aumentare o ridurre le risorse di calcolo agendo sulle unità di data warehouse. Nella pagina [Creare e connettere - portale](create-data-warehouse-portal.md)**mySampleDataWarehouse** è stato creato e inizializzato con 400 unità Data Warehouse. La procedura seguente modifica le unità Data Warehouse per **mySampleDataWarehouse**.

Per modificare le unità Data Warehouse, usare il cmdlet PowerShell [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase). L'esempio seguente imposta le unità di data warehouse su DW300c per il database **mySampleDataWarehouse** che è ospitato nel gruppo di risorse **resourcegroupname** del server **sqlpoolservername**.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Controllare lo stato del data warehouse

Per visualizzare lo stato corrente del data warehouse, usare il cmdlet PowerShell [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase). Questo cmdlet mostra lo stato del database **mySampleDataWarehouse** nel gruppo di risorse **resourcegroupname** e nel server **sqlpoolservername.database.windows.net**.

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName resourcegroupname -ServerName sqlpoolservername -DatabaseName mySampleDataWarehouse
```

Verrà visualizzato un risultato simile al seguente:

```powershell
ResourceGroupName             : resourcegroupname
ServerName                    : sqlpoolservername
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/resourcegroupname/providers/Microsoft.Sql/servers/sqlpoolservername/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

È possibile visualizzare lo **stato** del database nell'output. Nel caso in questione, il database è online.  Quando si esegue questo comando, è possibile ricevere un valore di stato Online, In pausa, Ripresa, Ridimensionamento o Sospeso.

Per visualizzare solo lo stato, usare il comando seguente:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Passaggi successivi
Si è appreso come ridimensionare le risorse di calcolo per un pool SQL. Per altre informazioni sul pool SQL, continuare con l'esercitazione sul caricamento dei dati.

> [!div class="nextstepaction"]
>[Caricare i dati in un pool SQL](load-data-from-azure-blob-storage-using-polybase.md)
