---
title: Errori di risorsa non trovati
description: Viene descritto come risolvere gli errori quando non è possibile trovare una risorsa durante la distribuzione con un modello di Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773253"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Risolvere gli errori relativi alle risorse di Azure non trovate

Questo articolo descrive gli errori che possono essere visualizzati quando è impossibile trovare una risorsa durante la distribuzione.

## <a name="symptom"></a>Sintomo

Quando il modello include il nome di una risorsa che non può essere risolta, l'utente riceve un errore simile al seguente:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Se si usa la funzione [reference](template-functions-resource.md#reference) o la funzione [listKeys](template-functions-resource.md#listkeys) con una risorsa che non può essere risolta, verrà visualizzato l'errore seguente:

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
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

È tuttavia opportuno evitare di impostare dipendenze non necessarie. La presenza di dipendenze non necessarie prolunga la durata della distribuzione impedendo di distribuire in parallelo risorse che non sono interdipendenti. Si potrebbero anche creare dipendenze circolari che bloccano la distribuzione. La funzione [reference](template-functions-resource.md#reference) e la funzione\ [list*](template-functions-resource.md#list) creano una dipendenza implicita dalla risorsa di riferimento, se tale risorsa viene distribuita nello stesso modello e vi si fa riferimento tramite il nome, non tramite l'ID risorsa. Si potrebbero quindi avere dipendenze aggiuntive rispetto a quelle specificate nella proprietà **dependsOn**. La funzione [resourceId](template-functions-resource.md#resourceid) non crea una dipendenza implicita né esegue la convalida dell'esistenza della risorsa. Le funzioni [reference](template-functions-resource.md#reference) e [list*](template-functions-resource.md#list) non creano una dipendenza implicita se si fa riferimento alla risorsa tramite l'ID risorsa. Per creare una dipendenza implicita, passare il nome della risorsa distribuita nello stesso modello.

In caso di problemi relativi alle dipendenze, è necessario esaminare in modo approfondito l'ordine di distribuzione delle risorse. Per visualizzare l'ordine delle operazioni di distribuzione:

1. Selezionare la cronologia delle distribuzioni per il gruppo di risorse.

   ![selezionare la cronologia delle distribuzioni](./media/error-not-found/select-deployment.png)

2. Selezionare una distribuzione dalla cronologia e fare clic su **Eventi**.

   ![selezionare gli eventi di distribuzione](./media/error-not-found/select-deployment-events.png)

3. Esaminare la sequenza degli eventi per ogni risorsa. Prestare attenzione allo stato di ciascuna operazione. Ad esempio, l'immagine seguente mostra tre account di archiviazione distribuiti in parallelo. Si noti che i tre account di archiviazione vengono avviati contemporaneamente.

   ![distribuzione parallela](./media/error-not-found/deployment-events-parallel.png)

   L'immagine successiva mostra tre account di archiviazione che non vengono distribuiti in parallelo. Il secondo account di archiviazione dipende dal primo e il terzo account di archiviazione dipende dal secondo. Il primo account di archiviazione viene avviato, accettato e completato prima che venga avviato il successivo.

   ![distribuzione sequenziale](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Soluzione 2: ottenere risorse da un gruppo di risorse diverso

Se la risorsa è inclusa in un gruppo di risorse differente da quello distribuito, usare la funzione [resourceId](template-functions-resource.md#resourceid) per ottenere il nome completo della risorsa.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Soluzione 3: controllare la funzione reference

Cercare un'espressione contenente la funzione [reference](template-functions-resource.md#reference). I valori forniti variano a seconda che la risorsa sia nello stesso modello, gruppo di risorse e sottoscrizione. Controllare che ai parametri vengano passati i valori necessari per lo scenario. Se la risorsa è in un altro gruppo di risorse, fornire l'ID risorsa completo. Ad esempio, per fare riferimento a un account di archiviazione in un altro gruppo di risorse, usare:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Soluzione 4: ottenere l'identità gestita dalla risorsa

Se si distribuisce una risorsa che crea in modo implicito un' [identità gestita](../../active-directory/managed-identities-azure-resources/overview.md), è necessario attendere che la risorsa venga distribuita prima di recuperare i valori nell'identità gestita. Se si passa il nome dell'identità gestita alla funzione [Reference](template-functions-resource.md#reference) , gestione risorse tenta di risolvere il riferimento prima della distribuzione della risorsa e dell'identità. Al contrario, passare il nome della risorsa a cui viene applicata l'identità. Questo approccio assicura che la risorsa e l'identità gestita vengano distribuite prima che Gestione risorse risolve la funzione di riferimento.

Nella funzione Reference usare `Full` per ottenere tutte le proprietà, inclusa l'identità gestita.

Ad esempio, per ottenere l'ID tenant per un'identità gestita applicata a un set di scalabilità di macchine virtuali, usare:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```