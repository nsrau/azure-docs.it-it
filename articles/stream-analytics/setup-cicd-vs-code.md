---
title: L'integrazione continua e sviluppare con pacchetto npm di Azure Stream Analitica integrazione continua/recapito Continuo
description: Questo articolo descrive come usare pacchetto npm di Azure Stream Analitica integrazione continua/recapito Continuo per configurare l'integrazione continua e processo di distribuzione.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: fa5a57afa379c6bbe027be80f400fc176800d289
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158514"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-cicd-npm-package"></a>L'integrazione continua e sviluppare con pacchetto npm Stream Analitica integrazione continua/recapito Continuo
Questo articolo descrive come usare il pacchetto npm di Azure Stream Analitica integrazione continua/recapito Continuo per configurare l'integrazione continua e processo di distribuzione.

## <a name="build-the-vs-code-project"></a>Compilare il progetto di Visual Studio Code

È possibile abilitare l'integrazione continua e distribuzione per i processi di Analitica Stream di Azure con il **asa-streamanalytics-continue** pacchetto npm. Il pacchetto npm offre gli strumenti per generare modelli di Azure Resource Manager della [i progetti di Visual Studio Code di Stream Analitica](quick-create-vs-code.md). Può essere utilizzato in Windows, macOS e Linux senza installare Visual Studio Code.

Dopo aver [scaricato il pacchetto](https://www.npmjs.com/package/azure-streamanalytics-cicd), usare il comando seguente per restituire i modelli di Azure Resource Manager. Il **scriptPath** argomento è il percorso assoluto per il **asaql** file nel progetto. Assicurarsi che i file di JobConfig.json e asaproj.json siano nella stessa cartella con il file di script. Se il **outputPath** non viene specificato, i modelli di disponibilità verranno inseriti nel **Distribuisci** cartella del progetto **bin** cartella.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Esempio (in macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Quando viene compilato un progetto di Visual Studio Code di Stream Analitica, genera i seguenti due file di modello Azure Resource Manager con il **bin / [Debug/Retail] /deploy** cartella: 

*  File del modello di Resource Manager

       [ProjectName].JobTemplate.json 

*  File dei parametri di Resource Manager

       [ProjectName].JobTemplate.parameters.json   

I parametri predefiniti nel file Parameters. JSON sono dalle impostazioni del progetto di Visual Studio Code. Se si vuole distribuire in un altro ambiente, sostituire i parametri di conseguenza.

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

* [Avvio rapido: Creare un processo di cloud di Azure Stream Analitica in Visual Studio Code (anteprima)](quick-create-vs-code.md)
* [Testare le query Analitica di Stream in locale con Visual Studio Code (anteprima)](vscode-local-run.md)
* [Esplorare Azure Stream Analitica con Visual Studio Code (anteprima)](vscode-explore-jobs.md)
