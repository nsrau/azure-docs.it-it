---
title: Toolkit di test del modello ARM
description: Viene descritto come eseguire ARM template test Toolkit sul modello. Il Toolkit consente di verificare se sono state implementate le procedure consigliate.
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 73f6db8cbd5e4d7a0670c394f6af338aae8e9e79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89439561"
---
# <a name="use-arm-template-test-toolkit"></a>Usare ARM template test Toolkit

Il [Toolkit di test del modello di Azure Resource Manager (ARM)](https://aka.ms/arm-ttk) verifica se il modello usa le procedure consigliate. Quando il modello non è conforme alle procedure consigliate, restituisce un elenco di avvisi con le modifiche suggerite. Utilizzando il Toolkit di test, è possibile apprendere come evitare problemi comuni nello sviluppo di modelli.

Il Toolkit di test fornisce un [set di test predefiniti](test-cases.md). Questi test sono raccomandazioni ma non requisiti. È possibile decidere quali test sono rilevanti per gli obiettivi e personalizzare i test eseguiti.

Questo articolo descrive come eseguire il Toolkit di test e come aggiungere o rimuovere i test. Per le descrizioni dei test predefiniti, vedere [test case del Toolkit](test-cases.md).

Il Toolkit è un set di script di PowerShell che può essere eseguito da un comando in PowerShell o CLI.

## <a name="install-on-windows"></a>Installare in Windows

1. Se non si dispone già di PowerShell, [installare PowerShell in Windows](/powershell/scripting/install/installing-powershell-core-on-windows).

1. [Scaricare il file zip più recente](https://aka.ms/arm-ttk-latest) per il Toolkit di test ed estrarlo.

1. Avviare PowerShell.

1. Passare alla cartella in cui è stato estratto il Toolkit di test. All'interno di tale cartella passare alla cartella **ARM-TTK** .

1. Se i [criteri di esecuzione](/powershell/module/microsoft.powershell.core/about/about_execution_policies) bloccano gli script da Internet, è necessario sbloccare i file script. Assicurarsi di trovarsi nella cartella **ARM-TTK** .

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importare il modulo.

   ```powershell
   Import-Module .\arm-ttk.psd1
   ```

1. Per eseguire i test, usare il comando seguente:

   ```powershell
   Test-AzTemplate -TemplatePath \path\to\template
   ```

## <a name="install-on-linux"></a>Installare in Linux

1. Se non si dispone già di PowerShell, [installare PowerShell in Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

1. [Scaricare il file zip più recente](https://aka.ms/arm-ttk-latest) per il Toolkit di test ed estrarlo.

1. Avviare PowerShell.

   ```bash
   pwsh
   ```

1. Passare alla cartella in cui è stato estratto il Toolkit di test. All'interno di tale cartella passare alla cartella **ARM-TTK** .

1. Se i [criteri di esecuzione](/powershell/module/microsoft.powershell.core/about/about_execution_policies) bloccano gli script da Internet, è necessario sbloccare i file script. Assicurarsi di trovarsi nella cartella **ARM-TTK** .

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importare il modulo.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. Per eseguire i test, usare il comando seguente:

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="install-on-macos"></a>Eseguire l'installazione in macOS

1. Se non si dispone già di PowerShell, [installare PowerShell in MacOS](/powershell/scripting/install/installing-powershell-core-on-macos).

1. Installare `coreutils`:

   ```bash
   brew install coreutils
   ```

1. [Scaricare il file zip più recente](https://aka.ms/arm-ttk-latest) per il Toolkit di test ed estrarlo.

1. Avviare PowerShell.

   ```bash
   pwsh
   ```

1. Passare alla cartella in cui è stato estratto il Toolkit di test. All'interno di tale cartella passare alla cartella **ARM-TTK** .

1. Se i [criteri di esecuzione](/powershell/module/microsoft.powershell.core/about/about_execution_policies) bloccano gli script da Internet, è necessario sbloccare i file script. Assicurarsi di trovarsi nella cartella **ARM-TTK** .

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importare il modulo.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. Per eseguire i test, usare il comando seguente:

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
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
