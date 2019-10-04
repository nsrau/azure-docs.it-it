---
title: Usare il pacchetto NPM di analisi di flusso CI/CD
description: Questo articolo descrive come usare il pacchetto NPM di analisi di flusso CI/CD per configurare un processo di integrazione e distribuzione continua.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: df9afaaeeb7e41c111fe6bd053047095a9cb9349
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173337"
---
# <a name="use-the-stream-analytics-cicd-npm-package"></a>Usare il pacchetto NPM di analisi di flusso CI/CD
Questo articolo descrive come usare il pacchetto NPM di analisi di flusso CI/CD per configurare un processo di integrazione e distribuzione continua.

## <a name="build-the-vs-code-project"></a>Compilare il progetto VS Code

È possibile abilitare l'integrazione e la distribuzione continue per i processi di analisi di flusso di Azure usando il pacchetto NPM **ASA-streamanalytics-CICD** . Il pacchetto NPM fornisce gli strumenti per generare Azure Resource Manager modelli di [progetti di analisi di flusso Visual Studio Code](quick-create-vs-code.md). Può essere usato in Windows, macOS e Linux senza installare Visual Studio Code.

Dopo aver [scaricato il pacchetto](https://www.npmjs.com/package/azure-streamanalytics-cicd), usare il comando seguente per generare l'output dei modelli di Azure Resource Manager. L'argomento **ScriptPath** è il percorso assoluto del file **asaql** nel progetto. Assicurarsi che i file asaproj. JSON e JobConfig. JSON si trovino nella stessa cartella con il file script. Se **OutputPath** non è specificato, i modelli verranno inseriti nella cartella **deploy** nella cartella **bin** del progetto.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Esempio (in macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Quando un progetto di analisi di flusso Visual Studio Code viene compilato correttamente, genera i seguenti due Azure Resource Manager file modello nella cartella **bin/[debug/Retail]/deploy** : 

*  File del modello di Resource Manager

       [ProjectName].JobTemplate.json 

*  File dei parametri di Resource Manager

       [ProjectName].JobTemplate.parameters.json   

I parametri predefiniti nel file Parameters. JSON sono delle impostazioni nel progetto Visual Studio Code. Se si vuole distribuire in un altro ambiente, sostituire i parametri di conseguenza.

> [!NOTE]
> Per tutte le credenziali, i valori predefiniti sono impostati su Null. È **obbligatorio** impostare i valori prima di eseguire la distribuzione nel cloud.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Altre informazioni su come [distribuire con un file di modello di Resource Manager e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Altre informazioni su come [usare un oggetto come parametro in un modello di Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Per usare le identità gestite per Azure Data Lake Store Gen1 come sink di output, è necessario fornire l'accesso all'entità servizio con PowerShell prima della distribuzione in Azure. Altre informazioni su come [distribuire ADLS Gen1 con identità gestite usando un modello di Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Creare un processo cloud di analisi di flusso di Azure in Visual Studio Code (anteprima)](quick-create-vs-code.md)
* [Testare le query di analisi di flusso localmente con Visual Studio Code (anteprima)](vscode-local-run.md)
* [Esplora analisi di flusso di Azure con Visual Studio Code (anteprima)](vscode-explore-jobs.md)
