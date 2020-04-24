---
title: Distribuire ambienti modello annidati in Azure DevTest Labs
description: Informazioni su come distribuire modelli di Azure Resource Manager annidati per fornire ambienti con Azure DevTest Labs.
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
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Distribuire modelli di Azure Resource Manager annidati per ambienti di testing
Una distribuzione annidata consente di eseguire altri modelli di Azure Resource Manager dall'interno di un modello Gestione risorse principale. Consente di scomporre la distribuzione in un set di modelli mirati e specifici dello scopo. Fornisce vantaggi in termini di test, riutilizzo e leggibilità. L'articolo [utilizzo di modelli collegati quando si distribuiscono risorse di Azure](../azure-resource-manager/templates/linked-templates.md) fornisce una panoramica di questa soluzione con diversi esempi di codice. Questo articolo fornisce un esempio specifico per Azure DevTest Labs. 

## <a name="key-parameters"></a>Parametri chiave
Sebbene sia possibile creare un modello di Gestione risorse personalizzato da zero, è consigliabile usare il [progetto gruppo di risorse di Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) in Visual Studio, che semplifica lo sviluppo e il debug di modelli. Quando si aggiunge una risorsa di distribuzione annidata a file azuredeploy. JSON, Visual Studio aggiunge diversi elementi per rendere il modello più flessibile. Questi elementi includono la sottocartella con il modello e il file dei parametri secondari, i nomi delle variabili all'interno del file di modello principale e due parametri per il percorso di archiviazione per i nuovi file. I **_artifactsLocation** e **_artifactsLocationSasToken** sono i parametri chiave usati da DevTest Labs. 

Se non si ha familiarità con il funzionamento di DevTest Labs con gli ambienti, vedere [creare ambienti con più macchine virtuali e risorse PaaS con Azure Resource Manager modelli](devtest-lab-create-environment-from-arm.md). I modelli vengono archiviati nel repository collegato al Lab in DevTest Labs. Quando si crea un nuovo ambiente con questi modelli, i file vengono spostati in un contenitore di archiviazione di Azure nel Lab. Per poter identificare e copiare i file annidati, DevTest Labs identifica i parametri _artifactsLocation e _artifactsLocationSasToken e copia le sottocartelle fino al contenitore di archiviazione. Quindi inserisce automaticamente il percorso e il token di firma di accesso condiviso (SaS) nei parametri. 

## <a name="nested-deployment-example"></a>Esempio di distribuzione annidata
Di seguito è riportato un esempio semplice di una distribuzione annidata:

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

La cartella nel repository contenente questo modello include una sottocartella `nestedtemplates` con i file **NestOne. JSON** e **NestOne. Parameters. JSON**. In **file azuredeploy. JSON**, l'URI del modello viene compilato usando il percorso degli elementi, la cartella dei modelli annidati, il nome del file modello annidato. Analogamente, l'URI per i parametri viene compilato utilizzando il percorso degli elementi, la cartella dei modelli annidati e il file di parametri per il modello annidato. 

Di seguito è illustrata l'immagine della stessa struttura di progetto in Visual Studio: 

![Struttura del progetto in Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

È possibile aggiungere altre cartelle nella cartella primaria, ma non più di un singolo livello. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sugli ambienti, vedere gli articoli seguenti: 

- [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurare e usare ambienti pubblici in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Connettere un ambiente alla rete virtuale del Lab in Azure DevTest Labs](connect-environment-lab-virtual-network.md)