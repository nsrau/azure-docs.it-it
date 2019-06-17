---
title: Errori relativi alle risorse padre in Azure| Microsoft Docs
description: Questo articolo descrive come risolvere gli errori che possono verificarsi quando si lavora con una risorsa padre.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.author: tomfitz
ms.openlocfilehash: 19642138105f3de0e2bda3f0f80e12cf74a35709
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61059991"
---
# <a name="resolve-errors-for-parent-resources"></a>Risolvere gli errori delle risorse padre

Questo articolo descrive gli errori che possono verificarsi durante la distribuzione di una risorsa che dipende da una risorsa padre.

## <a name="symptom"></a>Sintomo

Quando si distribuisce una risorsa che è un elemento figlio di un'altra risorsa, potrebbe essere visualizzato l'errore seguente:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Causa

Quando una risorsa è l'elemento figlio di un'altra risorsa, la risorsa padre deve già esistere prima di creare la risorsa figlio. Il nome della risorsa figlio definisce la connessione con la risorsa padre. Il nome della risorsa figlio è nel formato `<parent-resource-name>/<child-resource-name>`. Ad esempio, un database SQL può essere definito come segue:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Se si distribuisce sia il server sia il database nello stesso modello, ma non si specifica una dipendenza nel server, la distribuzione del database potrebbe essere avviata prima che il server abbia completato la distribuzione. 

Se la risorsa padre esiste già e non è stata distribuita nello stesso modello, questo errore si verifica quando Resource Manager non è in grado di associare la risorsa figlio alla risorsa padre. Questo errore potrebbe verificarsi quando la risorsa figlio non è nel formato corretto o la risorsa figlio viene distribuita in un gruppo di risorse diverso rispetto al gruppo di risorse per la risorsa padre.

## <a name="solution"></a>Soluzione

Per risolvere questo errore quando vengono distribuite risorse padre e figlio nello stesso modello, includere una dipendenza.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Per risolvere questo errore quando la risorsa padre è stata distribuita in precedenza in un altro modello, non si imposta una dipendenza. Distribuire invece la risorsa figlio nello stesso gruppo di risorse e specificare il nome della risorsa padre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlServerName": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2014-04-01",
            "type": "Microsoft.Sql/servers/databases",
            "location": "[resourceGroup().location]",
            "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
            "properties": {
                "collation": "SQL_Latin1_General_CP1_CI_AS",
                "edition": "Basic"
            }
        }
    ],
    "outputs": {}
}
```

Per altre informazioni, leggere [Definire l'ordine per la distribuzione delle risorse nei modelli di Azure Resource Manager](resource-group-define-dependencies.md).