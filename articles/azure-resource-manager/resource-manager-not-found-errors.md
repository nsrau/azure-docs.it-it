---
title: Errore Risorsa di Azure non trovata | Microsoft Docs
description: Informazioni su come risolvere gli errori che segnalano l'impossibilità di trovare una risorsa.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: 6844c1c2938873b0a74fe66e846dc733a4bd6ff7
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Risolvere gli errori relativi alle risorse di Azure non trovate

Questo articolo descrive gli errori che possono verificarsi quando è impossibile trovare una risorsa durante la distribuzione.

## <a name="symptom"></a>Sintomo

Quando il modello include il nome di una risorsa che non può essere risolta, l'utente riceve un errore simile al seguente:

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

Resource Manager deve recuperare le proprietà di una risorsa, ma non riesce a identificare la risorsa nella sottoscrizione.

## <a name="solution-1---set-dependencies"></a>Soluzione 1: impostare le dipendenze

Se si sta provando a distribuire la risorsa mancante nel modello, verificare se è necessario aggiungere una dipendenza. Azure Resource Manager consente di ottimizzare la distribuzione creando risorse in parallelo, quando ciò è possibile. Se una risorsa deve essere distribuita dopo un'altra, nel modello è necessario usare l'elemento **dependsOn**. Ad esempio, quando si distribuisce un'app Web deve esistere il piano di servizio app. Se non è stato specificato che l'app Web dipende dal piano di servizio app, Resource Manager crea entrambe le risorse contemporaneamente. Viene visualizzato un errore che segnala che la risorsa del piano di servizio app non è stata trovata perché non esiste ancora quando si prova a impostare una proprietà nell'app Web. È possibile prevenire questo errore impostando la dipendenza nell'app Web.

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

È tuttavia opportuno evitare di impostare dipendenze non necessarie. La presenza di dipendenze non necessarie prolunga la durata della distribuzione impedendo di distribuire in parallelo risorse che non sono interdipendenti. Si potrebbero anche creare dipendenze circolari che bloccano la distribuzione. La funzione [reference](resource-group-template-functions-resource.md#reference) crea una dipendenza implicita nella risorsa specificata, se tale risorsa viene distribuita nello stesso modello. Si potrebbero quindi avere dipendenze aggiuntive rispetto a quelle specificate nella proprietà **dependsOn**. La funzione [resourceId](resource-group-template-functions-resource.md#resourceid) non crea una dipendenza implicita né esegue la convalida dell'esistenza della risorsa.

In caso di problemi relativi alle dipendenze, è necessario approfondire l'ordine di distribuzione delle risorse. Per visualizzare l'ordine delle operazioni di distribuzione:

1. Selezionare la cronologia delle distribuzioni per il gruppo di risorse.

   ![selezionare la cronologia delle distribuzioni](./media/resource-manager-not-found-errors/select-deployment.png)

2. Selezionare una distribuzione dalla cronologia e fare clic su **Eventi**.

   ![selezionare gli eventi di distribuzione](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. Esaminare la sequenza degli eventi per ogni risorsa. Prestare attenzione allo stato di ciascuna operazione. Ad esempio, l'immagine seguente mostra tre account di archiviazione distribuiti in parallelo. Si noti che i tre account di archiviazione vengono avviati contemporaneamente.

   ![distribuzione parallela](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   L'immagine successiva mostra tre account di archiviazione che non vengono distribuiti in parallelo. Il secondo account di archiviazione dipende dal primo e il terzo account di archiviazione dipende dal secondo. Il primo account di archiviazione viene avviato, accettato e completato prima che venga avviato il successivo.

   ![distribuzione sequenziale](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Soluzione 2: ottenere risorse da un gruppo di risorse diverso

Se la risorsa è inclusa in un gruppo di risorse differente da quello distribuito, usare la funzione [resourceId](resource-group-template-functions-resource.md#resourceid) per ottenere il nome completo della risorsa.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Soluzione 3: controllare la funzione reference

Cercare un'espressione contenente la funzione [reference](resource-group-template-functions-resource.md#reference). I valori forniti variano a seconda che la risorsa sia nello stesso modello, gruppo di risorse e sottoscrizione. Controllare che vengano forniti i valori dei parametri necessari per lo scenario. Se la risorsa è in un altro gruppo di risorse, fornire l'ID risorsa completo. Ad esempio, per fare riferimento a un account di archiviazione in un altro gruppo di risorse, usare:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```