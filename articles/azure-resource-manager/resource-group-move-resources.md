---
title: Spostare le risorse di Azure in una nuova sottoscrizione o in un nuovo gruppo di risorse | Microsoft Docs
description: Usare Azure Resource Manager per spostare risorse a un nuovo gruppo di risorse o a una nuova sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 114e0d8e935aa8e6ac3f70a34a8050b19758fb42
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624549"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Spostare le risorse in un nuovo gruppo di risorse o una nuova sottoscrizione

Questo articolo illustra come spostare le risorse di Azure in un'altra sottoscrizione o in un altro gruppo di risorse all'interno della stessa sottoscrizione. Per spostare le risorse, è possibile usare il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.

Durante l'operazione di spostamento il gruppo di origine e quello di destinazione sono bloccati. Le operazioni di scrittura ed eliminazione sono bloccate nei gruppi di risorse fino al completamento dello spostamento. Questo blocco indica che non è possibile aggiungere, aggiornare o eliminare le risorse dei gruppi di risorse, ma non che le risorse sono bloccate. Se ad esempio si sposta un Server SQL con il relativo database in un nuovo gruppo di risorse, nelle applicazioni che usano il database non si verificano tempi di inattività, poiché rimane possibile leggere e scrivere nel database.

Lo spostamento di una risorsa lo sposta solo in un nuovo gruppo di risorse o una nuova sottoscrizione. Non modifica la posizione della risorsa.

## <a name="checklist-before-moving-resources"></a>Controllo prima di spostare le risorse

Prima di spostare una risorsa, è necessario eseguire alcuni passi importanti. La verifica di queste condizioni consente di evitare errori.

1. Le risorse che si desidera spostare devono supportare l'operazione di spostamento. Per un elenco delle risorse che supportano lo spostamento, vedere [spostare il supporto delle operazioni per le risorse](move-support-resources.md).

1. Alcuni servizi presentano limitazioni o requisiti specifici quando si trasferiscono le risorse. Se è stato spostato uno dei servizi seguenti, controllare le linee guida prima di procedere.

   * [Linee guida per lo spostamento dei servizi app](./move-limitations/app-service-move-limitations.md)
   * [Linee guida per lo spostamento Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Linee guida per lo spostamento di modelli di distribuzione classica](./move-limitations/classic-model-move-limitations.md) -calcolo classico, archiviazione classica, reti virtuali classiche e servizi cloud
   * [Linee guida per lo spostamento in rete](./move-limitations/networking-move-limitations.md)
   * [Linee guida per lo spostamento dei servizi di ripristino](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Linee guida per lo spostamento delle macchine virtuali](./move-limitations/virtual-machines-move-limitations.md)

   Se il gruppo di risorse di destinazione contiene una rete virtuale, lo stato delle risorse dipendenti può bloccare lo spostamento anche quando tali risorse non sono incluse nello spostamento. Per altre informazioni, vedere [linee guida](./move-limitations/virtual-network-move-limitations.md)per lo spostamento di rete.

1. Le sottoscrizioni di origine e di destinazione devono essere attive. In caso di problemi durante l'abilitazione di un account precedentemente disabilitato, [creare una richiesta di supporto tecnico di Azure](../azure-supportability/how-to-create-azure-support-request.md). Selezionare **Gestione delle sottoscrizioni** per il tipo di problema.

1. Le sottoscrizioni di origine e di destinazione devono trovarsi nello stesso [tenant di Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md). Per verificare che entrambe le sottoscrizioni contengano lo stesso ID tenant, usare Azure PowerShell o l'interfaccia della riga di comando di Azure.

   Per Azure PowerShell usare:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Per l'interfaccia della riga di comando di Azure usare:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Se gli ID tenant per le sottoscrizioni di origine e di destinazione non sono uguali, usare i metodi descritti di seguito per risolvere le differenze degli ID tenant:

   * [Trasferimento della proprietà di una sottoscrizione di Azure a un altro account](../billing/billing-subscription-transfer.md)
   * [Come associare o aggiungere una sottoscrizione di Azure ad Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Il provider di risorse della risorsa da spostare deve essere registrato nella sottoscrizione di destinazione, altrimenti un errore indica che la **sottoscrizione non è registrata per un tipo di risorsa**. Questo errore può verificarsi se si sposta una risorsa in una nuova sottoscrizione, ma la sottoscrizione non è mai stata usata con tale tipo di risorsa.

   In PowerShell, per ottenere lo stato della registrazione usare i comandi seguenti:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Per registrare un provider di risorse, usare:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Nell'interfaccia della riga di comando di Azure, per ottenere lo stato della registrazione usare i comandi seguenti:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Per registrare un provider di risorse, usare:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. L'account che sposta le risorse deve avere almeno le autorizzazioni seguenti:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** sul gruppo di risorse di origine.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** sul gruppo di risorse di destinazione.

1. Prima di spostare le risorse, controllare le quote della sottoscrizione in cui si desidera spostare le risorse. Se lo spostamento di risorse causa il superamento dei limiti della sottoscrizione, è necessario verificare se è possibile richiedere un aumento della quota. Per un elenco dei limiti e su come richiedere un aumento, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

1. **Per lo spostamento tra le sottoscrizioni, la risorsa e le relative risorse dipendenti devono trovarsi nello stesso gruppo di risorse e devono essere spostate insieme.** Ad esempio, una macchina virtuale con dischi gestiti richiede lo spostamento della macchina virtuale e dei dischi gestiti, insieme ad altre risorse dipendenti.

   Se si sta migrando una risorsa in una nuova sottoscrizione, verificare se la risorsa ha risorse dipendenti e se si trovano nello stesso gruppo di risorse. Se le risorse non si trovano nello stesso gruppo di risorse, controllare per verificare se le risorse possono essere consolidate nello stesso gruppo di risorse. In tal caso, portare tutte queste risorse nello stesso gruppo di risorse usando un'operazione di spostamento tra i gruppi di risorse.

   Per ulteriori informazioni, vedere [scenario per lo spostamento tra sottoscrizioni](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Scenario per lo spostamento tra sottoscrizioni

Lo trasferimento di risorse da una sottoscrizione a un'altra è un processo in tre passaggi:

![scenario di spostamento tra sottoscrizioni](./media/resource-group-move-resources/cross-subscription-move-scenario.png)

A scopo illustrativo, è presente una sola risorsa dipendente.

* Passaggio 1: Se le risorse dipendenti vengono distribuite in gruppi di risorse diversi, spostarle prima in un gruppo di risorse.
* Passaggio 2: Spostare la risorsa e le risorse dipendenti insieme dalla sottoscrizione di origine alla sottoscrizione di destinazione.
* Passaggio 3: Facoltativamente, ridistribuire le risorse dipendenti a diversi gruppi di risorse all'interno della sottoscrizione di destinazione. 

## <a name="validate-move"></a>Convalidare lo spostamento

L'[operazione di convalida dello spostamento](/rest/api/resources/resources/validatemoveresources) consente di testare lo scenario di spostamento senza realmente spostare le risorse. Utilizzare questa operazione per verificare se lo spostamento avrà esito positivo. La convalida viene chiamata automaticamente quando si invia una richiesta di spostamento. Utilizzare questa operazione solo quando è necessario predeterminare i risultati. Per eseguire questa operazione, è necessario:

* il nome del gruppo di risorse di origine
* l'ID risorsa della risorsa del gruppo di risorse di destinazione
* l'ID risorsa di ogni risorsa da spostare
* il [token di accesso](/rest/api/azure/#acquire-an-access-token) per l'account

Inviare la richiesta seguente:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Con un corpo della richiesta:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Se la richiesta viene formattata correttamente, l'operazione restituisce:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Il codice di stato 202 indica che la richiesta di convalida è stata accettata, ma non è stato ancora determinato se l'operazione di spostamento avrà esito positivo. Il valore `location` contiene un URL che si usa per controllare lo stato dell'operazione a esecuzione prolungata.  

Per controllare lo stato, inviare la richiesta seguente:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Mentre l'operazione è ancora in esecuzione, si continua a ricevere il codice di stato 202. Attendere il numero di secondi indicato nel valore `retry-after` prima di riprovare. Se l'operazione di spostamento ha esito positivo, viene visualizzato il codice di stato 204. Se la convalida dello spostamento ha esito negativo, viene visualizzato un messaggio di errore, ad esempio:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Usare il portale

Per spostare le risorse, selezionare il gruppo con tali risorse, quindi usare il pulsante **Sposta**.

![Spostare le risorse](./media/resource-group-move-resources/select-move.png)

Selezionare se si desidera spostare le risorse in un nuovo gruppo di risorse o in una nuova sottoscrizione.

Selezionare le risorse da spostare e il gruppo di risorse di destinazione. Confermare di dover aggiornare gli script per queste risorse e selezionare **OK**. Se si seleziona l'icona del comando Modifica sottoscrizione nel passaggio precedente, è necessario anche selezionare la sottoscrizione di destinazione.

![Selezione della destinazione](./media/resource-group-move-resources/select-destination.png)

In **Notifiche**si nota che è in corso l'operazione di spostamento.

![Visualizzare lo stato dello spostamento](./media/resource-group-move-resources/show-status.png)

Al completamento dell'operazione si riceverà la notifica del risultato.

![Visualizzare il risultato dello spostamento](./media/resource-group-move-resources/show-result.png)

Se viene visualizzato un errore, vedere [risolvere i problemi di trasferimento delle risorse di Azure in un nuovo gruppo di risorse o sottoscrizione](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Usare Azure PowerShell

Per spostare le risorse esistenti in un gruppo di risorse o in una sottoscrizione diversa, usare il comando [Move-AzResource](/powershell/module/az.resources/move-azresource). L'esempio seguente illustra come spostare diverse risorse in un nuovo gruppo di risorse.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Per eseguire lo spostamento in una nuova sottoscrizione, includere un valore per il parametro `DestinationSubscriptionId`.

Se viene visualizzato un errore, vedere [risolvere i problemi di trasferimento delle risorse di Azure in un nuovo gruppo di risorse o sottoscrizione](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

Per spostare risorse esistenti in un altro gruppo di risorse o un'altra sottoscrizione, usare il comando [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move). Fornire gli ID risorsa delle risorse da spostare. L'esempio seguente illustra come spostare diverse risorse in un nuovo gruppo di risorse. Nel parametro `--ids` inserire un elenco delimitato da spazi di ID di risorse da spostare.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Per spostare in una nuova sottoscrizione, inserire il parametro `--destination-subscription-id`.

Se viene visualizzato un errore, vedere [risolvere i problemi di trasferimento delle risorse di Azure in un nuovo gruppo di risorse o sottoscrizione](troubleshoot-move.md).

## <a name="use-rest-api"></a>Usare l'API REST

Per spostare le risorse esistenti in un altro gruppo di risorse o una sottoscrizione, usare l'operazione di [spostamento delle risorse](/rest/api/resources/Resources/MoveResources) .

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

Nel corpo della richiesta specificare il gruppo di risorse di destinazione e le risorse da spostare.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Se viene visualizzato un errore, vedere [risolvere i problemi di trasferimento delle risorse di Azure in un nuovo gruppo di risorse o sottoscrizione](troubleshoot-move.md).

## <a name="next-steps"></a>Passaggi successivi

Per un elenco delle risorse che supportano lo spostamento, vedere [spostare il supporto delle operazioni per le risorse](move-support-resources.md).
