---
title: Esportare un modello di Azure Stream Analytics per il processo di Azure Resource ManagerExport an Azure Stream Analytics job Azure Resource Manager template
description: Questo articolo descrive come esportare un modello di Azure Resource Manager per il processo di Analisi di flusso di Azure.This article describes how to export an Azure Resource Manager template for your Azure Stream Analytics job.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968904"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Esportare un modello di Azure Stream Analytics per il processo di Azure Resource ManagerExport an Azure Stream Analytics job Azure Resource Manager template

I modelli di [Azure Resource Manager](../azure-resource-manager/templates/overview.md) consentono di implementare l'infrastruttura come codice. Il modello è un file JSON (JavaScript Object Notation) che definisce l'infrastruttura e la configurazione per le risorse. Specificare le risorse da distribuire e le proprietà per tali risorse.

È possibile ridistribuire un processo di Analisi di flusso di Azure esportando il modello di Azure Resource Manager.You can redeploy an Azure Stream Analytics job by exporting the Azure Resource Manager template.

## <a name="open-a-job-in-vs-code"></a>Aprire un processo in codice VSOpen a job in VS Code

Prima di esportare un modello, è necessario aprire un processo di Analisi di flusso esistente in Visual Studio Code. 

Per esportare un processo in un progetto locale, individuare il processo che si vuole esportare in Esplora analisi di flusso nel portale di Azure.To export a job to a local project, locate the job you wish export in the **Stream Analytics Explorer** in the Azure portal. Nella pagina **Query** selezionare **Apri in Visual Studio**. Selezionare quindi **Visual Studio Code**.

![Aprire il processo di Analisi di flusso nel codice di Visual StudioOpen Stream Analytics job in Visual Studio Code](./media/resource-manager-export/open-job-vs-code.png)

Per ulteriori informazioni sull'utilizzo del codice di Visual Studio per gestire i processi di Analisi di flusso, vedere la [guida introduttiva al codice](quick-create-vs-code.md)di Visual Studio .

## <a name="compile-the-script"></a>Compilare lo script 

Il passaggio successivo consiste nel compilare lo script del processo in un modello di Azure Resource Manager.The next step is to compile the job script to an Azure Resource Manager template. Prima di compilare lo script, verificare che il processo abbia almeno un input e un output configurato. Se non è configurato alcun input o output, è necessario configurare prima l'input e l'output.

1. In Visual Studio Code passare al file *Transformation.asaql* del processo.

   ![File Transformation.asaql nel codice di Visual Studio](./media/resource-manager-export/transformation-asaql.png)

1. Fare clic con il pulsante destro del mouse sul file *Transformation.asaql* e selezionare **ASA: Compile Script (Compila script)** dal menu.

1. Si noti che viene visualizzata una cartella **Deploy** nell'area di lavoro di Analisi di flusso.

1. Esplorare il file *JobTemplate.json,* ovvero il modello di Gestione risorse di Azure usato per la distribuzione.

## <a name="complete-the-parameters-file"></a>Completare il file dei parametri

Completare quindi il file dei parametri del modello di Gestione risorse di Azure.Next, complete the Azure Resource Management template parameters file.

1. Aprire il file *JobTemplate.parameters.json* che si trova nella cartella **Deploy** dell'area di lavoro di flusso di flusso in Visual Studio Code.

1. Si noti che le chiavi di input e output sono null. Sostituire i valori null con i tasti di scelta effettivi per le risorse di input e di output.

1. Salvare il file dei parametri.

## <a name="deploy-using-templates"></a>Distribuire utilizzando i modelli

È possibile distribuire il processo di Analisi di flusso di Azure usando i modelli di Azure Resource Manager generati nella sezione precedente.

In una finestra di PowerShell eseguire il comando seguente. Assicurarsi di reaplce il *ResourceGroupName*, *TemplateFile*, e *TemplateParameterFile* con il nome del gruppo di risorse effettivo e i percorsi di file completi per i file *JobTemplate.json* e *JobTemplate.parameters.json* nella **cartella Deploy** dell'area di lavoro processo.

Se Azure PowerShell non è configurato, seguire i passaggi descritti in Installare il [modulo di Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-Az-ps)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Passaggi successivi

* [Testare i processi di Analisi di flusso di Azure in locale con input dinamico usando il codice di Visual StudioTest Azure Stream Analytics jobs locally with live input using Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Esplorare i processi di Analisi di flusso di Azure con il codice di Visual Studio (anteprima)Explore Azure Stream Analytics jobs with Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)