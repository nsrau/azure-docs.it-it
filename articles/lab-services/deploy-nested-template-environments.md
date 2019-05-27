---
title: Distribuire annidati gli ambienti di modello di Resource Manager in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come distribuire i modelli annidati di Azure Resource Manager per offrire ambienti con Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: spelluru
ms.openlocfilehash: eec0cde4a36449f85998bfb04d16f1d52c68bb19
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835286"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Distribuire modelli di Azure Resource Manager nidificati per ambienti di test
Una distribuzione nidificata consente di eseguire altri modelli di Azure Resource Manager all'interno di un modello di Resource Manager principale. Consente di scomporre la distribuzione in un set di modelli di destinazione e scopi specifici. Offre vantaggi in termini di testing, riuso e leggibilità. L'articolo [uso di modelli collegati durante la distribuzione di risorse di Azure](../azure-resource-manager/resource-group-linked-templates.md) fornisce una valida panoramica di questa soluzione con diversi esempi di codice. Questo articolo fornisce un esempio specifico in Azure DevTest Labs. 

## <a name="key-parameters"></a>Parametri delle chiavi
Sebbene sia possibile creare il proprio modello di Resource Manager da zero, è consigliabile usare la [progetto gruppo di risorse di Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) in Visual Studio, che rendono più facile sviluppare ed eseguire il debug di modelli. Quando si aggiunge una risorsa di distribuzione annidata di azuredeploy. JSON, Visual Studio aggiunge diversi elementi per rendere il modello più flessibile. Questi elementi includono la sottocartella con il file di modello e i parametri secondario, i nomi delle variabili all'interno del file di modello principale e due parametri per il percorso di archiviazione per i nuovi file. Il **artifactslocation** e **artifactslocationsastoken** sono parametri della chiave che usa DevTest Labs. 

Se non si ha familiarità con il funzionamento con gli ambienti DevTest Labs, vedere [creare ambienti con più macchine Virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md). I modelli vengono archiviati nel repository di cui è collegato al laboratorio in DevTest Labs. Quando si crea un nuovo ambiente con tali modelli, i file vengono spostati in un contenitore di archiviazione di Azure nell'ambiente di laboratorio. Per essere in grado di identificare e copiare i file annidati, DevTest Labs identifica i parametri artifactslocation e artifactslocationsastoken e copia le sottocartelle fino al contenitore di archiviazione. Quindi, inserisce automaticamente il percorso e un token di firma di accesso condiviso (SaS) in parametri. 

## <a name="nested-deployment-example"></a>Esempio di distribuzione annidata
Ecco un esempio semplice di una distribuzione nidificata:

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

La cartella del repository che contiene questo modello contiene una sottocartella `nestedtemplates` con i file **NestOne.json** e **NestOne.parameters.json**. Nel **azuredeploy. JSON**, URI per il modello viene compilato usando il percorso degli elementi, la cartella di modello annidato, nome del file modello annidato. Analogamente, URI per i parametri viene compilata usando il percorso degli elementi, cartella modelli annidati e file di parametri per il modello annidato. 

Ecco l'immagine della stessa struttura di progetto in Visual Studio: 

![Struttura del progetto in Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

È possibile aggiungere altre cartelle nella cartella principale, ma non tutti più profondo rispetto a un solo livello. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti per informazioni dettagliate sugli ambienti: 

- [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurare e usare gli ambienti pubblici in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Connettere un ambiente di rete virtuale del lab in Azure DevTest Labs](connect-environment-lab-virtual-network.md)