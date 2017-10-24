---
title: Errore Risorsa di Azure non trovata | Microsoft Docs
description: "Informazioni su come risolvere gli errori che segnalano l'impossibilità di trovare una risorsa."
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
ms.openlocfilehash: c76c965c43ca8217faa9488c01975ce09a21daaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Risolvere gli errori relativi alle risorse di Azure non trovate

Questo articolo descrive gli errori che possono verificarsi quando non viene trovata una risorsa durante la distribuzione. 

## <a name="symptom"></a>Sintomo

Quando il modello include il nome di una risorsa che non può essere risolta, verrà visualizzato un errore simile al seguente:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Se si prova a usare la funzione [reference](resource-group-template-functions-resource.md#reference) o la funzione [listKeys](resource-group-template-functions-resource.md#listkeys) con una risorsa che non può essere risolta, verrà visualizzato l'errore seguente:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Causa

Azure Resource Manager deve recuperare le proprietà di una risorsa, ma non è possibile identificare la risorsa nella sottoscrizione.

## <a name="solution"></a>Soluzione

### <a name="solution-1"></a>Soluzione 1

Se si sta provando a distribuire la risorsa mancante nel modello, verificare se sia necessario aggiungere una dipendenza. Azure Resource Manager consente di ottimizzare la distribuzione creando risorse in parallelo, quando ciò è possibile. Se una risorsa deve essere distribuita dopo un'altra, è necessario usare l'elemento **dependsOn** nel modello per creare una dipendenza dall'altra risorsa. Ad esempio, quando si distribuisce un'app Web deve esistere il piano di servizio app. Se non è stato specificato che l'app Web dipende dal piano di servizio app, Azure Resource Manager crea entrambe le risorse contemporaneamente. Verrà visualizzato un messaggio di errore che informa che la risorsa del piano di servizio app non è stata trovata, perché non esiste ancora al momento del tentativo di impostare una proprietà nell'app Web. È possibile prevenire questo errore impostando la dipendenza nell'app Web.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Per suggerimenti sulla risoluzione degli errori relativi alle dipendenze, vedere [Controllare la sequenza di distribuzione](resource-manager-troubleshoot-tips.md#check-deployment-sequence).

### <a name="solution-2"></a>Soluzione 2

Se la risorsa è inclusa in un gruppo di risorse differente da quello distribuito, usare la funzione [resourceId](resource-group-template-functions-resource.md#resourceid) per ottenere il nome completo della risorsa.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

### <a name="solution-3"></a>Soluzione 3

Cercare un'espressione contenente la funzione [reference](resource-group-template-functions-resource.md#reference). I valori forniti variano a seconda che la risorsa sia nello stesso modello, gruppo di risorse e sottoscrizione. Controllare che vengano forniti i valori dei parametri necessari per lo scenario. Se la risorsa è in un altro gruppo di risorse, fornire l'ID risorsa completo. Ad esempio, per fare riferimento a un account di archiviazione in un altro gruppo di risorse, usare:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```