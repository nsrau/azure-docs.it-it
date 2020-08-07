---
title: Parametri globali
description: Impostare parametri globali per ogni ambiente di Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.date: 08/05/2020
ms.openlocfilehash: 052f502ed27db9ade0fd2916f91d6922c52a5a98
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854271"
---
# <a name="global-parameters-in-azure-data-factory"></a>Parametri globali in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I parametri globali sono costanti in un data factory che possono essere utilizzati da una pipeline in qualsiasi espressione. Sono utili quando si hanno più pipeline con nomi e valori di parametro identici. Quando si promuove una data factory usando l'integrazione continua e il processo di distribuzione (CI/CD), è possibile eseguire l'override di questi parametri in ogni ambiente. 

## <a name="creating-global-parameters"></a>Creazione di parametri globali

Per creare un parametro globale, passare alla scheda *parametri globali* nella sezione *Gestisci* . Selezionare **nuovo** per aprire il lato creazione-NAV.

![Crea parametri globali](media/author-global-parameters/create-global-parameter-1.png)

Nel NAV laterale, immettere un nome, selezionare un tipo di dati e specificare il valore del parametro.

![Crea parametri globali](media/author-global-parameters/create-global-parameter-2.png)

Dopo aver creato un parametro globale, è possibile modificarlo facendo clic sul nome del parametro. Per modificare contemporaneamente più parametri, selezionare **modifica tutto**.

![Crea parametri globali](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>Uso di parametri globali in una pipeline

I parametri globali possono essere usati in qualsiasi [espressione di pipeline](control-flow-expression-language-functions.md). Se una pipeline fa riferimento a un'altra risorsa, ad esempio un set di dati o un flusso di dati, è possibile passare il valore del parametro globale tramite i parametri di tale risorsa. Ai parametri globali viene fatto riferimento come `pipeline().globalParameters.<parameterName>` .

![Uso dei parametri globali](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a>Parametri globali in CI/CD

I parametri globali hanno un processo di integrazione continua/recapito continuo univoco rispetto ad altre entità in Azure Data Factory. Quando si pubblica una factory o si esporta un modello ARM con parametri globali, viene creata una cartella denominata *globalParameters* con un file denominato *your-factory-name_GlobalParameters.json*. Questo file è un oggetto JSON che contiene ogni tipo di parametro globale e valore nella Factory pubblicata.

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