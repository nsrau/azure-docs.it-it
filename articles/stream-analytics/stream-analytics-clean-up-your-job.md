---
title: Pulire un processo di Analisi di flusso di Azure
description: Questo articolo illustra metodi diversi per eliminare i processi di Analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 124d2cf4380153bd3ea68de9233f6a1b5eb6ba78
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130698"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Arrestare o eliminare il processo di analisi di flusso di Azure

I processi di analisi di flusso di Azure possono essere facilmente arrestati o eliminati tramite il portale di Azure, Azure PowerShell, Azure SDK per .NET o l'API REST. Un processo di analisi di flusso non può essere recuperato dopo che è stato eliminato.

>[!NOTE] 
>Quando si arresta un processo di Analisi di flusso di Azure, i dati vengono mantenuti solo nell'archiviazione di input e output come ad esempio Hub eventi o Database SQL di Azure. Se è necessario rimuovere dati da Azure, assicurarsi di seguire il processo di rimozione per le risorse di input e output del processo di Analisi di flusso.

## <a name="stop-a-job-in-azure-portal"></a>Arrestare un processo nel portale di Azure

Quando si arresta un processo, viene effettuato il deprovisioning delle risorse e l'elaborazione degli eventi viene arrestata. Vengono inoltre arrestati gli addebiti relativi a questo processo. Tuttavia, tutte le configurazioni vengono mantenute e il processo può essere riavviato in un secondo momento 

1. Accedere al [portale di Azure](https://portal.azure.com). 

2. Individuare il processo di Analisi di flusso in esecuzione e selezionarlo.

3. Nella pagina del processo di Analisi di flusso selezionare **Arresta** per arrestare il processo. 

   ![Arrestare un processo di Analisi di flusso di Azure](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Eliminare un processo nel portale di Azure

>[!WARNING] 
>Un processo di analisi di flusso non può essere recuperato dopo che è stato eliminato.

1. Accedere al portale di Azure. 

2. Individuare il processo di Analisi di flusso di Azure esistente e selezionarlo.

3. Nella pagina del processo di Analisi di flusso selezionare **Elimina** per eliminare il processo. 

   ![Eliminare un processo di Analisi di flusso di Azure](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Arrestare o eliminare un processo con PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per arrestare un processo usando PowerShell, usare il cmdlet [Stop-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) . Per eliminare un processo usando PowerShell, usare il cmdlet [Remove-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) .

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Arrestare o eliminare un processo con Azure SDK per .NET

Per arrestare un processo con Azure SDK per .NET, usare il metodo [StreamingJobsOperationsExtensions.BeginStop](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet). Per eliminare un processo con Azure SDK per .NET, usare il metodo [StreamingJobsOperationsExtensions.BeginDelete](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet).

## <a name="stop-or-delete-a-job-using-rest-api"></a>Arrestare o eliminare un processo con API REST

Per arrestare un processo con API REST, vedere il metodo [Stop](/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob#stop). Per eliminare un processo con API REST, vedere il metodo [Delete](/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob#delete).