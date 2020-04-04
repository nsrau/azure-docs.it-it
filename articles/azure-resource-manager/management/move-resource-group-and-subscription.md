---
title: Spostare le risorse in una nuova sottoscrizione o gruppo di risorseMove resources to a new subscription or resource group
description: Usare Azure Resource Manager per spostare risorse a un nuovo gruppo di risorse o a una nuova sottoscrizione.
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: ffb5f8be81d3628084d127db404ab994d4d5b938
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631510"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione

Questo articolo illustra come spostare le risorse di Azure in un'altra sottoscrizione o in un altro gruppo di risorse all'interno della stessa sottoscrizione. Per spostare le risorse, è possibile usare il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.

Durante l'operazione di spostamento il gruppo di origine e quello di destinazione sono bloccati. Le operazioni di scrittura ed eliminazione sono bloccate nei gruppi di risorse fino al completamento dello spostamento. Questo blocco significa che non è possibile aggiungere, aggiornare o eliminare risorse nei gruppi di risorse. Non significa che le risorse siano congelate. Se ad esempio si sposta un Server SQL con il relativo database in un nuovo gruppo di risorse, nelle applicazioni che usano il database non si verificano tempi di inattività, poiché rimane possibile leggere e scrivere nel database. Il blocco può durare per un massimo di quattro ore, ma la maggior parte delle mosse completa in molto meno tempo.

Lo spostamento di una risorsa comporta solo il suo spostamento in un nuovo gruppo di risorse o una nuova sottoscrizione. Non modifica il percorso della risorsa.

## <a name="checklist-before-moving-resources"></a>Controllo prima di spostare le risorse

Prima di spostare una risorsa, è necessario eseguire alcuni passi importanti. La verifica di queste condizioni consente di evitare errori.

1. Le risorse che si desidera spostare devono supportare l'operazione di spostamento. Per un elenco delle risorse che supportano lo spostamento, vedere [Move operation support for resources](move-support-resources.md).

1. Alcuni servizi hanno limitazioni o requisiti specifici quando si spostano le risorse. Se si sposta uno dei seguenti servizi, controllare le indicazioni fornite prima di effettuare lo spostamento.

   * [Linee guida per lo spostamento dei servizi app](./move-limitations/app-service-move-limitations.md)
   * [Servizi DevOps di Azure spostare le linee guidaAzure DevOps Services move guidance](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Linee guida per](./move-limitations/classic-model-move-limitations.md) lo spostamento del modello di distribuzione classica - Calcolo classico, archiviazione classica, reti virtuali classiche e servizi cloud
   * [Linee guida per lo spostamento in rete](./move-limitations/networking-move-limitations.md)
   * [Indicazioni per lo spostamento dei servizi di ripristino](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Macchine virtuali spostare le linee guidaVirtual Machines move guidance](./move-limitations/virtual-machines-move-limitations.md)

1. Le sottoscrizioni di origine e di destinazione devono essere attive. In caso di problemi durante l'abilitazione di un account precedentemente disabilitato, [creare una richiesta di supporto tecnico di Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selezionare **Gestione delle sottoscrizioni** per il tipo di problema.

1. Le sottoscrizioni di origine e di destinazione devono trovarsi nello stesso [tenant di Azure Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md). Per verificare che entrambe le sottoscrizioni contengano lo stesso ID tenant, usare Azure PowerShell o l'interfaccia della riga di comando di Azure.

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

   * [Trasferimento della proprietà di una sottoscrizione di Azure a un altro account](../../billing/billing-subscription-transfer.md)
   * [Come associare o aggiungere una sottoscrizione di Azure ad Azure Active DirectoryHow to associate or add an Azure subscription to Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

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

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** nel gruppo di risorse di origine.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** nel gruppo di risorse di destinazione.

1. Prima di spostare le risorse, controllare le quote della sottoscrizione in cui si desidera spostare le risorse. Se lo spostamento di risorse causa il superamento dei limiti della sottoscrizione, è necessario verificare se è possibile richiedere un aumento della quota. Per un elenco dei limiti e su come richiedere un aumento, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md).

1. **Per uno spostamento tra sottoscrizioni, la risorsa e le relative risorse dipendenti devono trovarsi nello stesso gruppo di risorse e devono essere spostate insieme.** Ad esempio, una macchina virtuale con dischi gestiti richiederebbe lo spostamento della macchina virtuale e dei dischi gestiti insieme ad altre risorse dipendenti.

   Se si sposta una risorsa in una nuova sottoscrizione, verificare se la risorsa dispone di risorse dipendenti e se si trovano nello stesso gruppo di risorse. Se le risorse non fanno parte dello stesso gruppo di risorse, verificare se le risorse possono essere consolidate nello stesso gruppo di risorse. In tal caso, inserire tutte queste risorse nello stesso gruppo di risorse usando un'operazione di spostamento tra gruppi di risorse.

   Per ulteriori informazioni, vedere [Scenario per lo spostamento tra sottoscrizioni](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Scenario per lo spostamento da una sottoscrizione all'altra

Lo spostamento delle risorse da una sottoscrizione a un'altra è un processo in tre passaggi:Moving resources from one subscription to another is a three-step process:

![scenario di spostamento tra sottoscrizioni](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

A scopo illustrativo, abbiamo una sola risorsa dipendente.

* Passaggio 1: se le risorse dipendenti vengono distribuite tra gruppi di risorse diversi, spostarle innanzitutto in un unico gruppo di risorse.
* Passaggio 2: Spostare insieme la risorsa e le risorse dipendenti dalla sottoscrizione di origine alla sottoscrizione di destinazione.
* Passaggio 3: Facoltativamente, ridistribuire le risorse dipendenti in gruppi di risorse diversi all'interno della sottoscrizione di destinazione.

## <a name="validate-move"></a>Convalidare lo spostamento

L'operazione di [convalida dello spostamento](/rest/api/resources/resources/validatemoveresources) consente di testare lo scenario di spostamento senza spostare effettivamente le risorse. Utilizzare questa operazione per verificare se lo spostamento avrà esito positivo. La convalida viene chiamata automaticamente quando si invia una richiesta di spostamento. Utilizzare questa operazione solo quando è necessario predeterminare i risultati. Per eseguire questa operazione, è necessario:

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

![Spostare le risorse](./media/move-resource-group-and-subscription/select-move.png)

Selezionare se si desidera spostare le risorse in un nuovo gruppo di risorse o in una nuova sottoscrizione.

Selezionare le risorse da spostare e il gruppo di risorse di destinazione. Confermare di dover aggiornare gli script per queste risorse e selezionare **OK**. Se si seleziona l'icona del comando Modifica sottoscrizione nel passaggio precedente, è necessario anche selezionare la sottoscrizione di destinazione.

![Selezione della destinazione](./media/move-resource-group-and-subscription/select-destination.png)

In **Notifiche**si nota che è in corso l'operazione di spostamento.

![Visualizzare lo stato dello spostamento](./media/move-resource-group-and-subscription/show-status.png)

Al completamento dell'operazione si riceverà la notifica del risultato.

![Visualizzare il risultato dello spostamento](./media/move-resource-group-and-subscription/show-result.png)

Se viene visualizzato un errore, vedere Risolvere i problemi di spostamento delle risorse di Azure in [un nuovo gruppo di risorse o sottoscrizione.](troubleshoot-move.md)

## <a name="use-azure-powershell"></a>Usare Azure PowerShell

Per spostare le risorse esistenti in un gruppo di risorse o in una sottoscrizione diversa, usare il comando [Move-AzResource](/powershell/module/az.resources/move-azresource). L'esempio seguente illustra come spostare diverse risorse in un nuovo gruppo di risorse.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Per eseguire lo spostamento in una nuova sottoscrizione, includere un valore per il parametro `DestinationSubscriptionId`.

Se viene visualizzato un errore, vedere Risolvere i problemi di spostamento delle risorse di Azure in [un nuovo gruppo di risorse o sottoscrizione.](troubleshoot-move.md)

## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

Per spostare risorse esistenti in un altro gruppo di risorse o un'altra sottoscrizione, usare il comando [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move). Fornire gli ID risorsa delle risorse da spostare. L'esempio seguente illustra come spostare diverse risorse in un nuovo gruppo di risorse. Nel parametro `--ids` inserire un elenco delimitato da spazi di ID di risorse da spostare.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Per spostare in una nuova sottoscrizione, inserire il parametro `--destination-subscription-id`.

Se viene visualizzato un errore, vedere Risolvere i problemi di spostamento delle risorse di Azure in [un nuovo gruppo di risorse o sottoscrizione.](troubleshoot-move.md)

## <a name="use-rest-api"></a>Usare l'API REST

Per spostare le risorse esistenti in un altro gruppo di risorse o sottoscrizione, usare l'operazione [Sposta risorse.](/rest/api/resources/Resources/MoveResources)

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

Se viene visualizzato un errore, vedere Risolvere i problemi di spostamento delle risorse di Azure in [un nuovo gruppo di risorse o sottoscrizione.](troubleshoot-move.md)

## <a name="frequently-asked-questions"></a>Domande frequenti

**Domanda: L'operazione di spostamento delle risorse, che in genere richiede alcuni minuti, è in esecuzione da quasi un'ora. C'è qualcosa che non va?**

Lo spostamento di una risorsa è un'operazione complessa con fasi diverse. Può coinvolgere più di un semplice provider di risorse della risorsa che si sta tentando di spostare. A causa delle dipendenze tra i provider di risorse, Azure Resource Manager consente 4 ore per il completamento dell'operazione. Questo periodo di tempo offre ai fornitori di risorse la possibilità di risolvere problemi temporanei. Se la richiesta di spostamento rientra nel periodo di 4 ore, l'operazione continua a tentare di completare e potrebbe comunque riuscire. I gruppi di risorse di origine e di destinazione vengono bloccati durante questo periodo per evitare problemi di coerenza.

**Domanda: Perché il gruppo di risorse è bloccato per 4 ore durante lo spostamento delle risorse?**

La finestra di 4 ore è il tempo massimo consentito per lo spostamento delle risorse. Per impedire modifiche alle risorse da spostare, i gruppi di risorse di origine e di destinazione vengono bloccati per la durata dello spostamento delle risorse.

Ci sono due fasi in una richiesta di spostamento. Nella prima fase, la risorsa viene spostata. Nella seconda fase, le notifiche vengono inviate ad altri provider di risorse che dipendono dalla risorsa da spostare. Un gruppo di risorse può essere bloccato per l'intera finestra di 4 ore quando un provider di risorse non riesce in entrambe le fasi. Durante il periodo di tempo consentito, Resource Manager ritenta il passaggio non riuscito.

Se una risorsa non può essere spostata all'interno della finestra di 4 ore, Resource Manager sblocca entrambi i gruppi di risorse. Le risorse che sono state spostate correttamente si trovano nel gruppo di risorse di destinazione. Le risorse che non è stato possibile spostare vengono lasciate il gruppo di risorse di origine.

**Domanda: Quali sono le implicazioni dei gruppi di risorse di origine e di destinazione bloccati durante lo spostamento delle risorse?**

Il blocco impedisce di eliminare uno dei gruppi di risorse, creare una nuova risorsa in un gruppo di risorse o eliminare una delle risorse coinvolte nello spostamento.

L'immagine seguente mostra un messaggio di errore dal portale di Azure quando un utente tenta di eliminare un gruppo di risorse che fa parte di uno spostamento in corso.

![Spostare il messaggio di errore durante il tentativo di eliminazione](./media/move-resource-group-and-subscription/move-error-delete.png)

**Domanda: Cosa significa il codice di errore "MissingMoveDependentResources"?**

Quando si sposta una risorsa, le relative risorse dipendenti devono esistere nel gruppo di risorse o nella sottoscrizione di destinazione oppure essere incluse nella richiesta di spostamento. Il codice di errore MissingMoveDependentResources viene visualizzato quando una risorsa dipendente non soddisfa questo requisito. Il messaggio di errore contiene dettagli sulla risorsa dipendente che deve essere inclusa nella richiesta di spostamento.

Ad esempio, lo spostamento di una macchina virtuale potrebbe richiedere lo spostamento di sette tipi di risorse con tre provider di risorse diversi. Tali provider di risorse e tipi sono:Those resource providers and types are:

* Microsoft.Compute

  * virtualMachines
  * disks
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

Un altro esempio comune prevede lo spostamento di una rete virtuale. Potrebbe essere necessario spostare diverse altre risorse associate alla rete virtuale. La richiesta di spostamento potrebbe richiedere lo spostamento di indirizzi IP pubblici, tabelle di route, gateway di rete virtuale, gruppi di sicurezza di rete e altri.

**Domanda: Perché non è possibile spostare alcune risorse in Azure?**

Attualmente, non tutte le risorse in Azure supportano lo spostamento. Per un elenco delle risorse che supportano lo spostamento, vedere [Move operation support for resources](move-support-resources.md).

## <a name="next-steps"></a>Passaggi successivi

Per un elenco delle risorse che supportano lo spostamento, vedere [Move operation support for resources](move-support-resources.md).
