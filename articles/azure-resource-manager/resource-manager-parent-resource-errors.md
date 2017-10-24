---
title: Errori relativi alle risorse padre in Azure| Microsoft Docs
description: Questo articolo descrive come risolvere gli errori che possono verificarsi quando si lavora con una risorsa padre.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: e59147c4ac18f730f27b9d4aa9c008f219881065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
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

Quando una risorsa è l'elemento figlio di un'altra risorsa, la risorsa padre deve già esistere prima di creare la risorsa figlio. Il nome della risorsa figlio include il nome della risorsa padre. Ad esempio, un database SQL può essere definito come segue:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Se non si specifica una dipendenza sul server, la distribuzione del database potrebbe essere avviata prima che il server abbia completato la distribuzione.

## <a name="solution"></a>Soluzione

Per risolvere questo errore, includere una dipendenza.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Per altre informazioni, leggere [Definire l'ordine per la distribuzione delle risorse nei modelli di Azure Resource Manager](resource-group-define-dependencies.md).