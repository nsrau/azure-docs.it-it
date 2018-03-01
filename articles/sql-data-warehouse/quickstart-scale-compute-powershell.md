---
title: 'Guida introduttiva: aumentare il numero delle risorse di calcolo in Azure SQL Data Warehouse - PowerShell | Microsoft Docs'
description: "Attività di PowerShell per aumentare il numero delle risorse di calcolo modificando le unità Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/31/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: a3a435d6bdb0d35c96349540d5e9f9b5be61bd9b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>Guida introduttiva: ridimensionare le risorse di calcolo in Azure SQL Data Warehouse con PowerShell

È possibile ridimensionare le risorse di calcolo in Azure SQL Data Warehouse con PowerShell, [aumentandone il numero](sql-data-warehouse-manage-compute-overview.md) per ottenere prestazioni migliori o riducendolo per diminuire i costi. 

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Questa esercitazione richiede il modulo Azure PowerShell 5.1.1 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione attualmente in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps.md). 

## <a name="before-you-begin"></a>Prima di iniziare

Questa guida introduttiva presuppone che l'utente abbia già un data warehouse SQL che è possibile ridimensionare. Se è necessario crearne uno, fare riferimento a [Creare e connettere - portale](create-data-warehouse-portal.md) per creare un data warehouse denominato **mySampleDataWarehouse**. 

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) e seguire le istruzioni visualizzate.

```powershell
Add-AzureRmAccount
```

Per vedere quale sottoscrizione si sta usando, eseguire [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Per usare una sottoscrizione diversa da quella predefinita, eseguire [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription).

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Cercare informazioni sul data warehouse

Individuare il nome del database, il nome del server e il gruppo di risorse del data warehouse di cui si prevede di sospendere e riprendere l'esecuzione. 

Seguire questa procedura per trovare le informazioni sulla posizione del data warehouse.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nella pagina di sinistra del portale di Azure fare clic su **Database SQL**.
3. Selezionare **mySampleDataWarehouse** nella pagina **Database SQL**. Verrà aperto il data warehouse. 

    ![Nome del server e gruppo di risorse](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Annotare il nome del data warehouse che verrà usato come nome del database. Annotare anche il nome del server e il gruppo di risorse. Queste informazioni verranno usate nei comandi di sospensione e ripresa.
5. Se il server è foo.database.windows.net, usare solo la prima parte come nome del server nei cmdlet PowerShell. Nella figura precedente il nome completo del server è newserver-20171113.database.windows.net. Come nome del server nel cmdlet PowerShell viene usato **newserver-20171113**.

## <a name="scale-compute"></a>Ridimensionare le risorse di calcolo

In SQL Data Warehouse è possibile aumentare o ridurre le risorse di calcolo agendo sulle unità Data Warehouse. Nella pagina [Creare e connettere - portale](create-data-warehouse-portal.md) **mySampleDataWarehouse** è stato creato e inizializzato con 400 unità Data Warehouse. La procedura seguente modifica le unità Data Warehouse per **mySampleDataWarehouse**.

Per modificare le unità Data Warehouse, usare il cmdlet PowerShell [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase). L'esempio seguente imposta le unità Data Warehouse su DW300 per il database **mySampleDataWarehouse** che è ospitato nel gruppo di risorse **myResourceGroup** nel server **mynewserver-20171113**.

```Powershell
Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-database-state"></a>Controllare lo stato del database

Per visualizzare lo stato corrente del data warehouse, usare il cmdlet PowerShell [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase), che consente di ottenere lo stato del database **mySampleDataWarehouse** nel gruppo di risorse **myResourceGroup** e nel server **mynewserver-20171113.database.windows.net**.

```powershell
Get-AzureRmSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
```

Verrà visualizzato un risultato simile al seguente:

```powershell   
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
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
CurrentServiceObjectiveName   : DW300
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

È quindi possibile controllare lo **stato** del database. Nel caso in questione, il database è online.  Quando si esegue questo comando, è possibile ricevere un valore di stato Online, In pausa, Ripresa, Ridimensionamento o Sospeso.

## <a name="next-steps"></a>Passaggi successivi
Si è appreso come ridimensionare le risorse di calcolo per il data warehouse. Per altre informazioni su Azure SQL Data Warehouse, continuare con l'esercitazione per il caricamento dei dati.

> [!div class="nextstepaction"]
>[Caricare i dati in SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
