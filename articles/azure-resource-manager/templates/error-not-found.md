---
title: Errori di risorsa non trovati
description: Viene descritto come risolvere gli errori quando non è possibile trovare una risorsa. L'errore può verificarsi durante la distribuzione di un modello di Azure Resource Manager o durante l'esecuzione di azioni di gestione.
ms.topic: troubleshooting
ms.date: 06/10/2020
ms.openlocfilehash: 224af4ce0fe5053201f25d8207f4ca8cdc73e638
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84667948"
---
# <a name="resolve-resource-not-found-errors"></a>Risolvere gli errori di risorsa non trovata

Questo articolo descrive l'errore che si verifica quando non è possibile trovare una risorsa durante un'operazione. In genere, questo errore viene visualizzato durante la distribuzione delle risorse. Questo errore viene visualizzato anche quando si esegue attività di gestione e Azure Resource Manager non riesce a trovare la risorsa richiesta. Se, ad esempio, si tenta di aggiungere tag a una risorsa che non esiste, viene visualizzato questo errore.

## <a name="symptom"></a>Sintomo

Sono presenti due codici di errore che indicano che la risorsa non è stata trovata. L'errore **NotFound** restituisce un risultato simile al seguente:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

L'errore **ResourceNotFound** restituisce un risultato simile al seguente:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Causa

Gestione risorse necessario recuperare le proprietà di una risorsa, ma non è possibile trovare la risorsa nelle sottoscrizioni.

## <a name="solution-1---check-resource-properties"></a>Soluzione 1: controllare le proprietà delle risorse

Quando si riceve questo errore durante un'attività di gestione, controllare i valori forniti per la risorsa. I tre valori da controllare sono:

* Nome risorsa
* Nome del gruppo di risorse
* Subscription

Se si usa PowerShell o l'interfaccia della riga di comando di Azure, verificare se si sta eseguendo il comando nella sottoscrizione che contiene la risorsa. È possibile modificare la sottoscrizione con [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) o [AZ account set](/cli/azure/account#az-account-set). Molti comandi forniscono anche un parametro di sottoscrizione che consente di specificare una sottoscrizione diversa da quella del contesto corrente.

In caso di problemi durante la verifica delle proprietà, accedere al [portale](https://portal.azure.com). Trovare la risorsa che si sta provando a usare ed esaminare il nome della risorsa, il gruppo di risorse e la sottoscrizione.

## <a name="solution-2---set-dependencies"></a>Soluzione 2: impostare le dipendenze

Se viene ricevuto questo errore durante la distribuzione di un modello, potrebbe essere necessario aggiungere una dipendenza. Azure Resource Manager consente di ottimizzare la distribuzione creando risorse in parallelo, quando ciò è possibile. Se una risorsa deve essere distribuita dopo un'altra, nel modello è necessario usare l'elemento **dependsOn**. Ad esempio, quando si distribuisce un'app Web deve esistere il piano di servizio app. Se non è stato specificato che l'app Web dipende dal piano di servizio app, Resource Manager crea entrambe le risorse contemporaneamente. Viene visualizzato un errore che segnala che la risorsa del piano di servizio app non è stata trovata perché non esiste ancora quando si prova a impostare una proprietà nell'app Web. È possibile prevenire questo errore impostando la dipendenza nell'app Web.

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

## <a name="solution-3---get-external-resource"></a>Soluzione 3-ottenere una risorsa esterna

Quando si distribuisce un modello ed è necessario ottenere una risorsa esistente in una sottoscrizione o un gruppo di risorse diverso, usare la [funzione ResourceId](template-functions-resource.md#resourceid). Questa funzione restituisce per ottenere il nome completo della risorsa.

I parametri della sottoscrizione e del gruppo di risorse nella funzione resourceId sono facoltativi. Se non vengono forniti, per impostazione predefinita si tratta della sottoscrizione e del gruppo di risorse correnti. Quando si lavora con una risorsa in un gruppo di risorse o una sottoscrizione diversa, assicurarsi di specificare tali valori.

Nell'esempio seguente viene ottenuto l'ID risorsa per una risorsa esistente in un gruppo di risorse diverso.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Soluzione 4: ottenere l'identità gestita dalla risorsa

Se si distribuisce una risorsa che crea in modo implicito un' [identità gestita](../../active-directory/managed-identities-azure-resources/overview.md), è necessario attendere che la risorsa venga distribuita prima di recuperare i valori nell'identità gestita. Se si passa il nome dell'identità gestita alla funzione [Reference](template-functions-resource.md#reference) , gestione risorse tenta di risolvere il riferimento prima della distribuzione della risorsa e dell'identità. Al contrario, passare il nome della risorsa a cui viene applicata l'identità. Questo approccio assicura che la risorsa e l'identità gestita vengano distribuite prima che Gestione risorse risolve la funzione di riferimento.

Nella funzione Reference usare `Full` per ottenere tutte le proprietà, inclusa l'identità gestita.

Il modello è:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>, <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> Non usare il modello:
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> Il modello non riuscirà.

Ad esempio, per ottenere l'ID entità per un'identità gestita applicata a una macchina virtuale, usare:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

In alternativa, per ottenere l'ID tenant per un'identità gestita applicata a un set di scalabilità di macchine virtuali, usare:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>Soluzione 5-funzioni di controllo

Quando si distribuisce un modello, cercare le espressioni che usano le funzioni [Reference](template-functions-resource.md#reference) o [listKeys](template-functions-resource.md#listkeys) . I valori forniti variano a seconda che la risorsa sia nello stesso modello, gruppo di risorse e sottoscrizione. Verificare di fornire i valori dei parametri richiesti per lo scenario. Se la risorsa è in un altro gruppo di risorse, fornire l'ID risorsa completo. Ad esempio, per fare riferimento a un account di archiviazione in un altro gruppo di risorse, usare:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```