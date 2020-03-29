---
title: Distribuire ambienti di modelli annidati in Azure DevTest LabsDeploy nested template environments in Azure DevTest Labs
description: Informazioni su come distribuire modelli di Azure Resource Manager annidati per fornire ambienti con Azure DevTest Labs.Learn how to deploy nested Azure Resource Manager templates to provide environments with Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e83bc4e77a44f20d55fa3b56bc81aefd1d25bb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168829"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Distribuire modelli di Azure Resource Manager annidati per gli ambienti di testDeploy nested Azure Resource Manager templates for testing environments
Una distribuzione annidata consente di eseguire altri modelli di Azure Resource Manager dall'interno di un modello di Resource Manager principale. Consente di scomporre la distribuzione in un set di modelli specifici e di destinazione. Fornisce vantaggi in termini di test, riutilizzo e leggibilità. L'articolo [Utilizzo di modelli collegati durante la distribuzione di risorse di Azure](../azure-resource-manager/templates/linked-templates.md) offre una buona panoramica di questa soluzione con diversi esempi di codice. Questo articolo fornisce un esempio specifico di Azure DevTest Labs.This article provides an example that's specific to Azure DevTest Labs. 

## <a name="key-parameters"></a>Parametri chiave
Sebbene sia possibile creare il proprio modello di Resource Manager da zero, è consigliabile usare il [progetto Gruppo](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) di risorse di Azure in Visual Studio, che semplifica lo sviluppo e il debug dei modelli. Quando si aggiunge una risorsa di distribuzione annidata a azuredeploy.json, Visual Studio aggiunge diversi elementi per rendere il modello più flessibile. Questi elementi includono la sottocartella con il modello secondario e il file dei parametri, i nomi delle variabili all'interno del file modello principale e due parametri per il percorso di archiviazione per i nuovi file. Il **_artifactsLocation** e **_artifactsLocationSasToken** sono i parametri chiave utilizzati da DevTest Labs. 

Se non si ha familiarità con il funzionamento di DevTest Labs con gli ambienti, vedere [Creare ambienti multi-VM e risorse PaaS con i modelli](devtest-lab-create-environment-from-arm.md)di Azure Resource Manager. I modelli vengono archiviati nel repository collegato al lab in DevTest Labs. Quando si crea un nuovo ambiente con tali modelli, i file vengono spostati in un contenitore di Archiviazione di Azure nel lab. Per poter identificare e copiare i file nidificati, DevTest Labs identifica i parametri _artifactsLocation e _artifactsLocationSasToken e copia le sottocartelle fino al contenitore di archiviazione. Quindi, inserisce automaticamente il percorso e il token di firma di accesso condiviso (SaS) nei parametri. 

## <a name="nested-deployment-example"></a>Esempio di distribuzione annidataNested deployment example
Di seguito è riportato un semplice esempio di distribuzione annidata:Here is a simple example of a nested deployment:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

La cartella nel repository contenente questo `nestedtemplates` modello contiene una sottocartella con i file **NestOne.json** e **NestOne.parameters.json**. In **azuredeploy.json**, l'URI per il modello viene compilato utilizzando il percorso degli elementi, la cartella del modello annidato, il nome del file del modello annidato. Analogamente, l'URI per i parametri viene compilato utilizzando il percorso degli elementi, la cartella del modello annidato e il file dei parametri per il modello nidificato. 

Ecco l'immagine della stessa struttura di progetto in Visual Studio:Here is the image of the same project structure in Visual Studio: 

![Struttura del progetto in Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

È possibile aggiungere altre cartelle nella cartella principale, ma non più in profondità di un singolo livello. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sugli ambienti, vedere gli articoli seguenti:See the following articles for details about environments: 

- [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurare e usare ambienti pubblici in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Connettere un ambiente alla rete virtuale del lab nei laboratori DevTest di AzureConnect an environment to your lab's virtual network in Azure DevTest Labs](connect-environment-lab-virtual-network.md)