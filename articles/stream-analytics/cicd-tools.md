---
title: Automatizzare le compilazioni, i test e le distribuzioni di un processo di analisi di flusso di Azure usando gli strumenti CI/CD
description: Questo articolo descrive come usare gli strumenti di integrazione continua/distribuzione continua di analisi di flusso di Azure per compilare, testare e distribuire automaticamente un progetto di analisi di flusso di Azure.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: aa75a553ffc131f4827aa045849f1317d894ddc5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123151"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>Automatizzare le compilazioni, i test e le distribuzioni di un processo di analisi di flusso di Azure usando gli strumenti CI/CD

È possibile usare il pacchetto NPM di analisi di flusso CI/CD per compilare, testare e distribuire automaticamente i progetti di analisi di flusso di Azure Visual Studio Code o Visual Studio. I progetti possono essere creati usando gli strumenti di sviluppo oppure possono essere esportati da processi di analisi di flusso esistenti. Questo articolo descrive come usare il pacchetto NPM con qualsiasi sistema CI/CD. Per la distribuzione con Azure Pipelines, vedere [usare Azure DevOps per creare una pipeline ci/CD per un processo di analisi di flusso](set-up-cicd-pipeline.md).

## <a name="installation"></a>Installazione

È possibile [scaricare il pacchetto](https://www.npmjs.com/package/azure-streamanalytics-cicd) direttamente oppure installarlo a [livello globale](https://docs.npmjs.com/downloading-and-installing-packages-globally) tramite il `npm install -g azure-streamanalytics-cicd` comando. Si consiglia di usare il comando, che può essere usato anche in un'attività script di PowerShell o dell'interfaccia della riga di comando di Azure di una pipeline di compilazione in **Azure Pipelines** .

## <a name="build-the-project"></a>Compilare il progetto

Il pacchetto NPM **ASA-streamanalytics-CICD** fornisce gli strumenti per generare Azure Resource Manager modelli di progetti di analisi di flusso [Visual Studio Code](./quick-create-visual-studio-code.md) o [progetti di Visual Studio](stream-analytics-quick-create-vs.md). È anche possibile usare il pacchetto NPM in Windows, macOS e Linux senza installare Visual Studio Code o Visual Studio.

Dopo aver installato il pacchetto, usare il comando seguente per compilare i progetti di analisi di flusso.

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

Il comando di *compilazione* esegue una verifica della sintassi delle parole chiave e restituisce il modello di Azure Resource Manager.

| Parametro | Descrizione |
|---|---|
| `-project` | Il percorso assoluto del **asaproj.jsnel** file per il progetto Visual Studio Code o **[nome del progetto]. asaproj** per il progetto di Visual Studio. |
| `-outputPath` | Percorso della cartella di output per i modelli di Azure Resource Manager. Se non viene specificato, i modelli verranno inseriti nella directory corrente. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

Quando un progetto di analisi di flusso viene compilato correttamente, genera i due file seguenti nella cartella di output:

* Azure Resource Manager file di modello

   `[ProjectName].JobTemplate.json`

* Azure Resource Manager file di parametri

   `[ProjectName].JobTemplate.parameters.json`

I parametri predefiniti presenti nel parameters.jsfile sono inclusi nelle impostazioni del Visual Studio Code o del progetto di Visual Studio. Se si vuole distribuire in un altro ambiente, sostituire i parametri di conseguenza.

I valori predefiniti per tutte le credenziali sono **null** . È necessario impostare i valori prima di eseguire la distribuzione in Azure.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

Per usare l'identità gestita per Azure Data Lake Store Gen1 come sink di output, è necessario fornire l'accesso all'entità servizio usando PowerShell prima di distribuire in Azure. Altre informazioni su come [distribuire ADLS Gen1 con identità gestite usando un modello di Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).

## <a name="local-run"></a>Esecuzione locale

Se il progetto ha specificato file di input locali, è possibile eseguire uno script di analisi di flusso localmente usando il `localrun` comando.

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parametro | Descrizione |
|---|---|
| `-project` | Il percorso del **asaproj.jsnel** file per il progetto Visual Studio Code o **[nome del progetto]. asaproj** per il progetto di Visual Studio. |
| `-outputPath` | Percorso della cartella di output. Se non viene specificato, i file dei risultati di output verranno inseriti nella directory corrente. |
| `-customCodeZipFilePath` | Percorso del file zip per il codice personalizzato C#, ad esempio una funzione definita dall'utente o un deserializzatore, se vengono utilizzati. Assemblare le dll in un file zip e specificare il percorso. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> La funzione definita dall'utente JavaScript funziona solo in Windows.

## <a name="automated-test"></a>Test automatizzato

È possibile usare il pacchetto NPM CI/CD per configurare ed eseguire test automatizzati per lo script di analisi di flusso.

### <a name="add-a-test-case"></a>Aggiungere un test case

I test case sono descritti in un file di configurazione di test. Per iniziare, usare il `addtestcase` comando per aggiungere un modello di test case al file di configurazione di test. Se il file di configurazione di test non esiste, ne viene creato uno per impostazione predefinita.

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| Parametro | Descrizione |
|---|---|
| `-project` | Il percorso del **asaproj.jsnel** file per il progetto Visual Studio Code o **[nome del progetto]. asaproj** per il progetto di Visual Studio. |
| `-testConfigPath` | Percorso del file di configurazione di test. Se non viene specificato, il file verrà cercato in **\test** sotto la directory corrente del file **asaproj.json** , con il nome file predefinito **testConfig.json** . Se non esiste, verrà creato un nuovo file. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

Se il file di configurazione del test è vuoto, il contenuto seguente viene scritto nel file. In caso contrario, viene aggiunto un test case nella matrice di **testCases** . Le configurazioni di input necessarie vengono compilate automaticamente in base ai file di configurazione di input, se esistenti. In caso contrario, vengono configurati i valori predefiniti. Prima di eseguire il test, è necessario specificare **filePath** di ogni input e output previsto. È possibile modificare la configurazione manualmente.

Se si desidera che la convalida dei test ignori un determinato output, impostare il campo **obbligatorio** dell'output previsto su **false** .

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": "Required",
          "Required": true
        }
      ]
    }
  ]
}
```

### <a name="run-a-unit-test"></a>Eseguire un unit test

È possibile usare il comando seguente per eseguire più test case per il progetto. Nella cartella di output viene generato un riepilogo dei risultati del test. Il processo viene chiuso con il codice **0** per tutti i test superati; **-1** per l'eccezione. **-2** per i test non riusciti.

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parametro | Descrizione |
|---|---|
| `-project` | Il percorso del **asaproj.jsnel** file per il progetto Visual Studio Code o **[nome del progetto]. asaproj** per il progetto di Visual Studio. |
| `-testConfigPath` | Percorso del file di configurazione di test. Se non viene specificato, il file verrà cercato in **\test** sotto la directory corrente del file **asaproj.json** , con il nome file predefinito **testConfig.json** .
| `-outputPath` | Percorso della cartella di output dei risultati del test. Se non viene specificato, i file dei risultati di output verranno inseriti nella directory corrente. |
| `-customCodeZipFilePath` | Percorso del file zip per codice personalizzato, ad esempio una funzione definita dall'utente o un deserializzatore, se vengono utilizzati. |

Al termine di tutti i test, nella cartella di output viene generato un riepilogo dei risultati del test in formato JSON. Il file di riepilogo è denominato **testResultSummary.json** .

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>Distribuisci in Azure

È possibile usare il modello di Azure Resource Manager e i file di parametri generati da Build per [distribuire il processo in Azure](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template).

## <a name="next-steps"></a>Passaggi successivi

* [Integrazione continua e distribuzione continua per analisi di flusso di Azure](cicd-overview.md)
* [Configurare la pipeline CI/CD per il processo di analisi di flusso usando Azure Pipelines](set-up-cicd-pipeline.md)