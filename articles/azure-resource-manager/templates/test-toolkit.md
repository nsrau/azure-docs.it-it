---
title: Toolkit di test del modello ARM
description: Viene descritto come eseguire ARM template test Toolkit sul modello. Il Toolkit consente di verificare se sono state implementate le procedure consigliate.
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7b88096dfdd1c7fb3e2671a369132e75a8885b8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255770"
---
# <a name="use-arm-template-test-toolkit"></a>Usare ARM template test Toolkit

[ARM template test Toolkit](https://aka.ms/arm-ttk) controlla se il modello usa le procedure consigliate. Quando il modello non è conforme alle procedure consigliate, restituisce un elenco di avvisi con le modifiche suggerite. Utilizzando il Toolkit di test, è possibile apprendere come evitare problemi comuni nello sviluppo di modelli.

Il Toolkit di test fornisce un [set di test predefiniti](test-cases.md). Questi test sono raccomandazioni ma non requisiti. È possibile decidere quali test sono rilevanti per gli obiettivi e personalizzare i test eseguiti.

Questo articolo descrive come eseguire il Toolkit di test e come aggiungere o rimuovere i test. Per le descrizioni dei test predefiniti, vedere [test case del Toolkit](test-cases.md).

Il Toolkit è un set di script di PowerShell che può essere eseguito da un comando in PowerShell o CLI.

## <a name="download-test-toolkit"></a>Scarica Test Toolkit

Per utilizzare il Toolkit di test, è possibile creare un fork e clonare il [repository](https://aka.ms/arm-ttk) contenente gli script oppure [scaricare il file con estensione zip più recente](https://aka.ms/arm-ttk-latest).

A seconda dei criteri di esecuzione del computer in cui viene eseguito lo script, è possibile che venga ricevuto un errore relativo all'esecuzione di script da Internet. È necessario modificare i criteri di [esecuzione](/powershell/module/microsoft.powershell.core/about/about_execution_policies) o [sbloccare i file di script](/powershell/module/microsoft.powershell.utility/unblock-file).

## <a name="run-on-powershell"></a>Esegui in PowerShell

Prima di eseguire i test, importare il modulo.

```powershell
Import-Module .\arm-ttk.psd1 # from the same directory as .\arm-ttk.psd1
```

Per eseguire i test in **PowerShell**, usare il comando seguente:

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

## <a name="run-on-linux"></a>Esegui in Linux

Prima di eseguire i test, installare [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux).

Per eseguire i test in **Linux** in bash, usare il comando seguente:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

È anche possibile eseguire il test su pwsh.exe.

## <a name="run-on-macos"></a>Esegui in macOS

Prima di eseguire i test, installare [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-macos). 

Installare `coreutils`:

```bash
brew install coreutils
```

Per eseguire i test in **MacOS**, usare il comando seguente:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

## <a name="result-format"></a>Formato del risultato

I test superati vengono visualizzati in **verde** e preceduti da **[+]**.

I test che hanno esito negativo vengono visualizzati in **rosso** e preceduti da **[-]**.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="visualizzare i risultati dei test":::

I risultati del testo sono:

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>Parametri di test

Quando si specifica il parametro **-TemplatePath** , il Toolkit Cerca in tale cartella un modello denominato azuredeploy.json o maintemplate.json. Questo modello viene testato per primo e quindi vengono testati tutti gli altri modelli nella cartella e nelle relative sottocartelle. Gli altri modelli sono testati come modelli collegati. Se il percorso include un file denominato [CreateUiDefinition.json](../managed-applications/create-uidefinition-overview.md), esegue i test rilevanti per la definizione dell'interfaccia utente.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

Per testare un file in tale cartella, aggiungere il parametro **-file** . Tuttavia, la cartella deve ancora avere un modello principale denominato azuredeploy.json o maintemplate.json.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

Per impostazione predefinita, vengono eseguiti tutti i test. Per specificare i singoli test da eseguire, usare il parametro **-test** . Consente di specificare il nome del test. Per i nomi, vedere [test case per Toolkit](test-cases.md).

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Personalizzare i test

Per i modelli ARM, il Toolkit esegue tutti i test nella cartella **\arm-ttk\testcases\deploymentTemplate**. Se si desidera rimuovere definitivamente un test, eliminare il file dalla cartella.

Per i file [CreateUiDefinition](../managed-applications/create-uidefinition-overview.md) , esegue tutti i test nella cartella **\arm-ttk\testcases\CreateUiDefinition**.

Per aggiungere un test personalizzato, creare un file con la convenzione di denominazione: **Your-Custom-Test-Name.test.ps1**.

Il test può ottenere il modello come parametro di oggetto o un parametro di stringa. In genere, si usa una o l'altra, ma è possibile usare entrambe.

Usare il parametro Object quando è necessario ottenere una sezione del modello e scorrere le relative proprietà. Un test che usa il parametro dell'oggetto ha il formato seguente:

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

L'oggetto modello presenta le proprietà seguenti:

* $schema
* contentVersion
* parametri
* variables
* resources
* outputs

Ad esempio, è possibile ottenere la raccolta di parametri con `$TemplateObject.parameters` .

Usare il parametro String quando è necessario eseguire un'operazione di stringa sull'intero modello. Un test che usa il parametro di stringa ha il formato seguente:

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

È ad esempio possibile eseguire un'espressione regolare del parametro di stringa per verificare se viene utilizzata una sintassi specifica.

Per ulteriori informazioni sull'implementazione del test, esaminare gli altri test in tale cartella.

## <a name="integrate-with-azure-pipelines"></a>Integrazione con Azure Pipelines

È possibile aggiungere il Toolkit di test alla pipeline di Azure. Con una pipeline, è possibile eseguire il test ogni volta che il modello viene aggiornato oppure eseguirlo come parte del processo di distribuzione.

Il modo più semplice per aggiungere il Toolkit di test alla pipeline è con estensioni di terze parti. Sono disponibili le due estensioni seguenti:

* [Esegui test ARM TTK](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [Tester modello ARM](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

In alternativa, è possibile implementare le proprie attività. Nell'esempio seguente viene illustrato come scaricare il Toolkit di test.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

Nell'esempio seguente viene illustrato come eseguire i test.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui test predefiniti, vedere [test case per Toolkit](test-cases.md).
