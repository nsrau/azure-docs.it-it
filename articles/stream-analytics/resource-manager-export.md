---
title: Esportare un processo di analisi di flusso di Azure Azure Resource Manager modello
description: Questo articolo descrive come esportare un modello di Azure Resource Manager per il processo di analisi di flusso di Azure.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78968904"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Esportare un processo di analisi di flusso di Azure Azure Resource Manager modello

[Azure Resource Manager modelli](../azure-resource-manager/templates/overview.md) consentono di implementare l'infrastruttura come codice. Il modello è un file JavaScript Object Notation (JSON) che definisce l'infrastruttura e la configurazione per le risorse. Si specificano le risorse da distribuire e le proprietà di tali risorse.

È possibile ridistribuire un processo di analisi di flusso di Azure esportando il modello di Azure Resource Manager.

## <a name="open-a-job-in-vs-code"></a>Aprire un processo in VS Code

Prima di poter esportare un modello, è innanzitutto necessario aprire un processo di analisi di flusso esistente in Visual Studio Code. 

Per esportare un processo in un progetto locale, individuare il processo che si vuole esportare in **Esplora analisi di flusso** nel portale di Azure. Dalla pagina **query** selezionare **Apri in Visual Studio**. Quindi selezionare **Visual Studio Code**.

![Aprire il processo di analisi di flusso in Visual Studio Code](./media/resource-manager-export/open-job-vs-code.png)

Per altre informazioni sull'uso di Visual Studio Code per gestire i processi di analisi di flusso, vedere la [Guida introduttiva di Visual Studio Code](quick-create-vs-code.md).

## <a name="compile-the-script"></a>Compilare lo script 

Il passaggio successivo consiste nel compilare lo script del processo in un modello di Azure Resource Manager. Prima di compilare lo script, verificare che il processo disponga di almeno un input e un output configurato. Se non è configurato alcun input o output, è necessario configurare prima di tutto l'input e l'output.

1. In Visual Studio Code passare al file *Transformation. asaql* del processo.

   ![File Transformation. asaql in Visual Studio Code](./media/resource-manager-export/transformation-asaql.png)

1. Fare clic con il pulsante destro del mouse sul file *Transformation. asaql* e selezionare **ASA: compila script** dal menu.

1. Si noti che nell'area di lavoro del processo di analisi di flusso viene visualizzata una cartella di **distribuzione** .

1. Esplorare il file *JobTemplate. JSON* , che è il modello di Azure Resource Manager usato per la distribuzione.

## <a name="complete-the-parameters-file"></a>Completare il file dei parametri

Completare quindi il file dei parametri del modello di gestione risorse di Azure.

1. Aprire il file *JobTemplate. Parameters. JSON* che si trova nella cartella **deploy** dell'area di lavoro del processo di analisi di flusso in Visual Studio Code.

1. Si noti che le chiavi di input e di output sono null. Sostituire i valori null con le chiavi di accesso effettive per le risorse di input e di output.

1. Salvare il file dei parametri.

## <a name="deploy-using-templates"></a>Eseguire la distribuzione usando i modelli

Si è pronti per distribuire il processo di analisi di flusso di Azure usando i modelli di Azure Resource Manager generati nella sezione precedente.

In una finestra di PowerShell eseguire il comando seguente. Assicurarsi di reaplce *ResourceGroupName*, *TemplateFile*e *TemplateParameterFile* con il nome del gruppo di risorse effettivo e i percorsi dei file completi per i file *JobTemplate. JSON* e *JobTemplate. Parameters. JSON* nella **cartella Deploy** dell'area di lavoro del processo.

Se Azure PowerShell non è configurato, seguire la procedura descritta in [Install Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-Az-ps).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Passaggi successivi

* [Testare i processi di analisi di flusso di Azure localmente con input Live con Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Esplorare i processi di analisi di flusso di Azure con Visual Studio Code (anteprima)](visual-studio-code-explore-jobs.md)