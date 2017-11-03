---
title: Usare gli strumenti di Analisi di flusso di Azure per Visual Studio per configurare l'integrazione continua e il processo di distribuzione | Microsoft Docs
description: Esercitazione sull'uso degli strumenti di Analisi di flusso di Azure per Visual Studio per configurare l'integrazione continua e il processo di distribuzione
keywords: visual studio, NuGet, DevOps, CI/CD
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/27/2017
ms.author: sujie
ms.openlocfilehash: 13fe5e37424704bd5b948d3a6629c28b320025c4
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2017
---
# <a name="use-stream-analytics-visual-studio-tools-to-set-up-a-continuous-integration-and-deployment-process"></a>Usare gli strumenti di Analisi di flusso di Azure per Visual Studio per configurare l'integrazione continua e il processo di distribuzione
In questa esercitazione si apprenderà come usare gli strumenti di Analisi di flusso di Azure per Visual Studio per configurare l'integrazione continua e il processo di distribuzione.

La versione più recente (2.3.0000.0 o successiva) di [strumenti di Analisi di flusso di Azure per Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio) aggiunge il supporto per MSBuild.

È disponibile anche un nuovo pacchetto NuGet [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Fornisce MSBuild, l'esecuzione locale e gli strumenti di distribuzione che supportano l'integrazione continua e il processo di distribuzione dei progetti di Analisi di flusso per Visual Studio. 
> [!NOTE] 
Il pacchetto NuGet può essere usato solo con la versione 2.3.0000.0 o successiva di strumenti di Analisi di flusso di Azure per Visual Studio. Se i progetti sono stati creati con le versioni precedenti degli strumenti di Visual Studio, aprirli con la versione 2.3.0000.0 o successiva e salvare. Le nuove funzionalità vengono quindi abilitate. 

Informazioni su come usare gli [strumenti di Analisi di flusso di Azure per Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Come l'esperienza standard di Visual Studio MSBuild, per compilare un progetto sono disponibili due opzioni. È possibile fare clic con il pulsante destro del mouse sul progetto e quindi scegliere **Compila**. È possibile anche usare **MSBuild** nel pacchetto NuGet dalla riga di comando.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Quando viene compilato un progetto di Analisi di flusso per Visual Studio, vengono generati i seguenti due file del modello di Azure Resource Manager nella cartella **bin/[Debug/Retail]/Deploy**: 

*  File del modello di Resource Manager

       [ProjectName].JobTemplate.json 

*  File dei parametri di Resource Manager

       [ProjectName].JobTemplate.parameters.json   

I parametri predefiniti nel file parameters.json corrispondono alle impostazioni del progetto di Visual Studio. Se si vuole distribuire in un altro ambiente, sostituire i parametri di conseguenza.

> [!NOTE] 
Per tutte le credenziali, i valori predefiniti sono impostati su Null. È *obbligatorio* impostare i valori prima di eseguire la distribuzione nel cloud.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Altre informazioni su come [distribuire con un file di modello di Resource Manager e Azure PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy). Altre informazioni su come [usare un oggetto come parametro in un modello di Resource Manager](https://docs.microsoft.com/en-us/azure/architecture/building-blocks/extending-templates/objects-as-parameters).


## <a name="command-line-tool"></a>Strumento da riga di comando

### <a name="build-the-project"></a>Compilare il progetto
Il pacchetto NuGet include uno strumento da riga di comando denominato **SA.exe**. Supporta la compilazione di progetti e i test locali in un computer arbitrario che è possibile usare nei processi di integrazione continua e recapito continuo. 

Per impostazione predefinita, i file di distribuzione vengono inseriti nella directory corrente. È possibile specificare il percorso di output usando il seguente parametro -OutputPath:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testare lo script in locale

Se il progetto ha file di input locali specificati in Visual Studio, è possibile eseguire un test automatico per lo script usando il comando *localrun*. Il risultato dell'output verrà inserito nella directory corrente.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generare un file di definizione del processo da usare con l'API PowerShell di Analisi di flusso di Azure

Il comando *arm* prende come input i file del modello del processo e del parametro del modello del processo generati nella build. Quindi li combina in un file JSON di definizione del processo che può essere usato con l'API PowerShell di Analisi di flusso di Azure.

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Esempio:
```
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


