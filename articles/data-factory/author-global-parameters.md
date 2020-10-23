---
title: Parametri globali
description: Impostare parametri globali per ogni ambiente di Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.date: 08/31/2020
ms.openlocfilehash: c20bb77711833a1a17082e25e31d4a2e14db5bf9
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328188"
---
# <a name="global-parameters-in-azure-data-factory"></a>Parametri globali in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I parametri globali sono costanti in un data factory che possono essere utilizzati da una pipeline in qualsiasi espressione. Sono utili quando si hanno più pipeline con nomi e valori di parametro identici. Quando si promuove una data factory usando l'integrazione continua e il processo di distribuzione (CI/CD), è possibile eseguire l'override di questi parametri in ogni ambiente. 

## <a name="creating-global-parameters"></a>Creazione di parametri globali

Per creare un parametro globale, passare alla scheda *parametri globali* nella sezione *Gestisci* . Selezionare **nuovo** per aprire il lato creazione-NAV.

![Schermata che evidenzia il pulsante nuovo selezionato per creare parametri globali.](media/author-global-parameters/create-global-parameter-1.png)

Nel NAV laterale, immettere un nome, selezionare un tipo di dati e specificare il valore del parametro.

![Screenshot che mostra la posizione in cui si aggiungono il nome, il tipo di dati e il valore per il nuovo parametro globale.](media/author-global-parameters/create-global-parameter-2.png)

Dopo aver creato un parametro globale, è possibile modificarlo facendo clic sul nome del parametro. Per modificare contemporaneamente più parametri, selezionare **modifica tutto**.

![Crea parametri globali](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>Uso di parametri globali in una pipeline

I parametri globali possono essere usati in qualsiasi [espressione di pipeline](control-flow-expression-language-functions.md). Se una pipeline fa riferimento a un'altra risorsa, ad esempio un set di dati o un flusso di dati, è possibile passare il valore del parametro globale tramite i parametri di tale risorsa. Ai parametri globali viene fatto riferimento come `pipeline().globalParameters.<parameterName>` .

![Uso dei parametri globali](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> Parametri globali in CI/CD

Esistono due modi per integrare i parametri globali nella soluzione di distribuzione e integrazione continua:

* Includi parametri globali nel modello ARM
* Distribuire parametri globali tramite uno script di PowerShell

Per la maggior parte dei casi d'uso, è consigliabile includere i parametri globali nel modello ARM. Questa operazione si integrerà in modo nativo con la soluzione descritta nel [documento ci/CD](continuous-integration-deployment.md). Per impostazione predefinita, i parametri globali verranno aggiunti come parametro di modello ARM perché cambiano spesso da Environment a Environment. È possibile abilitare l'inclusione dei parametri globali nel modello ARM dall'hub di gestione.

![Includi nel modello ARM](media/author-global-parameters/include-arm-template.png)

Se si aggiungono parametri globali al modello ARM, viene aggiunta un'impostazione a livello di factory che sostituisce altre impostazioni a livello di Factory, ad esempio una chiave gestita dal cliente o una configurazione git in altri ambienti. Se queste impostazioni sono abilitate in un ambiente con privilegi elevati, ad esempio UAT o PROD, è preferibile distribuire i parametri globali tramite uno script di PowerShell nei passaggi indicati di seguito.

### <a name="deploying-using-powershell"></a>Distribuzione tramite PowerShell

I passaggi seguenti illustrano come distribuire parametri globali tramite PowerShell. Questa operazione è utile quando la factory di destinazione ha un'impostazione a livello di Factory, ad esempio una chiave gestita dal cliente.

Quando si pubblica una factory o si esporta un modello ARM con parametri globali, viene creata una cartella denominata *globalParameters* con un file denominato *your-factory-name_GlobalParameters.json*. Questo file è un oggetto JSON che contiene ogni tipo di parametro globale e valore nella Factory pubblicata.

![Pubblicazione di parametri globali](media/author-global-parameters/global-parameters-adf-publish.png)

Se si esegue la distribuzione in un nuovo ambiente, ad esempio TEST o PROD, è consigliabile creare una copia di questo file di parametri globali e sovrascrivere i valori specifici dell'ambiente appropriati. Quando si ripubblicano i parametri globali originali, il file verrà sovrascritto, ma la copia per l'altro ambiente non verrà toccata.

Ad esempio, se si dispone di una factory denominata ' ADF-DEV ' e di un parametro globale di tipo stringa denominata ' Environment ' con un valore ' dev ', quando si pubblica un file denominato *ADF-DEV_GlobalParameters.json* viene generato. Se si esegue la distribuzione in una factory di test denominata ' ADF_TEST ', creare una copia del file JSON, ad esempio denominato ADF-TEST_GlobalParameters.jssu, e sostituire i valori dei parametri con i valori specifici dell'ambiente. Il parametro ' Environment ' potrebbe avere un valore ' test ' adesso. 

![Distribuzione di parametri globali](media/author-global-parameters/powershell-task.png)

Usare lo script di PowerShell seguente per innalzare di livello i parametri globali ad altri ambienti. Aggiungere un'attività Azure PowerShell DevOps prima dell'Distribuzione modelli ARM. Nell'attività DevOps è necessario specificare il percorso del nuovo file dei parametri, il gruppo di risorse di destinazione e la data factory di destinazione.

> [!NOTE]
> Per distribuire parametri globali usando PowerShell, è necessario usare almeno la versione 4.4.0 del modulo AZ.

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul processo di [distribuzione e integrazione continua](continuous-integration-deployment.md) Azure Data Factory
* Informazioni su come usare il [linguaggio delle espressioni del flusso di controllo](control-flow-expression-language-functions.md)
