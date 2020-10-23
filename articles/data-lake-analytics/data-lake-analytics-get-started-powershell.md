---
title: Creare & Azure Data Lake Analytics di query-PowerShell
description: Usare Azure PowerShell per creare un account Azure Data Lake Analytics e inviare un processo U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/04/2017
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 93a05231bc971737a08d74ad04150e5449dfc792
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220942"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Introduzione ad Azure Data Lake Analytics con Azure PowerShell

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Questo articolo illustra come usare Azure PowerShell per creare account Azure Data Lake Analytics e quindi inviare ed eseguire processi U-SQL. Per altre informazioni su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prima di iniziare questa esercitazione sono necessari le informazioni seguenti:

* Un **account di Azure Data Lake Analytics**. Vedere [Introduzione a Data Lake Analytics](./data-lake-analytics-get-started-portal.md).
* **Una workstation con Azure PowerShell**. Vedere [Come installare e configurare Azure PowerShell](/powershell/azure/).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Questa esercitazione presuppone che si abbia già familiarità con l'uso di Azure PowerShell. In particolare, è necessario sapere come accedere ad Azure. Per istruzioni, vedere [Get started with Azure PowerShell](/powershell/azure/get-started-azureps) (Introduzione ad Azure PowerShell).

Per accedere con un nome di sottoscrizione:

```powershell
Connect-AzAccount -SubscriptionName "ContosoSubscription"
```

Invece del nome della sottoscrizione, è anche possibile usare un ID sottoscrizione per l'accesso:

```powershell
Connect-AzAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

In caso di esito positivo, l'output di questo comando è simile al testo seguente:

```text
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Preparazione dell'esercitazione

I frammenti di codice di PowerShell in questa esercitazione usano le variabili seguenti per archiviare queste informazioni:

```powershell
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Ottenere informazioni su un account Data Lake Analytics account

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Inviare un processo U-SQL

Creare una variabile di PowerShell per contenere lo script U-SQL.

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();

"@
```

Inviare il testo dello text con il cmdlet `Submit-AdlJob` e il parametro `-Script`.

```powershell
$job = Submit-AdlJob -Account $adla -Name "My Job" -Script $script
```

In alternativa, è possibile inviare un file di script usando il parametro `-ScriptPath`:

```powershell
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" -ScriptPath $filename
```

Ottenere lo stato di un processo con `Get-AdlJob`. 

```powershell
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

Invece di continuare a chiamare Get-AdlJob fino al termine di un processo, usare il cmdlet `Wait-AdlJob`.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Scaricare il file di output usando `Export-AdlStoreItem`.

```powershell
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>Vedere anche

* Per visualizzare la stessa esercitazione usando altri strumenti, scegliere i selettori di scheda nella parte superiore della pagina.
* Per informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Per le attività di gestione, vedere [manage Azure Data Lake Analytics using portale di Azure](data-lake-analytics-manage-use-portal.md).