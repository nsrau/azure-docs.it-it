---
title: Risolvere gli errori di distribuzione comuni
description: Descrive come risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: bc1568c53cdb5518f694d77a2f28f3cf77296ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460382"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager

Questo argomento descrive alcuni errori comuni che possono verificarsi durante la distribuzione di risorse in Azure e fornisce indicazioni sulla relativa risoluzione. Se non si trova il codice di errore per l'errore di distribuzione specifico, vedere [Trovare il codice di errore](#find-error-code).

Se stai cercando informazioni su un codice di errore e tali informazioni non vengono fornite in questo articolo, comunicacelo. In fondo a questa pagina, puoi lasciare un feedback. Il feedback viene monitorato con GitHub Issues.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Codici di errore

| Codice di errore | Strategia di riduzione del rischio | Ulteriori informazioni |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Seguire le limitazioni relative all'assegnazione dei nomi per gli account di archiviazione. | [Risolvere gli errori relativi ai nomi degli account di archiviazione](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Controllare le proprietà dell'account di archiviazione disponibili. | [storageAccount](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Il cluster o l'area non ha risorse disponibili o non può supportare le dimensioni di macchina virtuale richieste. Ripetere la richiesta in un secondo momento oppure richiedere una dimensione di macchina virtuale diversa. | [Problemi di provisioning e allocazione per Linux](../../virtual-machines/linux/troubleshoot-deployment-new-vm.md), [Problemi di provisioning e allocazione per Windows](../../virtual-machines/windows/troubleshoot-deployment-new-vm.md) e [Risolvere i problemi relativi agli errori di allocazione](../../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | Attendere il completamento dell'operazione simultanea. | |
| AuthorizationFailed | L'account o l'entità servizio non dispone dell'accesso sufficiente per completare la distribuzione. Selezionare il ruolo a cui appartiene l'account e il relativo accesso per l'ambito della distribuzione.<br><br>Questo errore potrebbe essere visualizzato quando un provider di risorse richiesto non è registrato. | [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/role-assignments-portal.md)<br><br>[Risoluzione degli errori di registrazione del provider di risorse](error-register-resource-provider.md) |
| RichiestaNonValida | I valori della distribuzione inviati non corrispondono ai valori previsti da Resource Manager. Per informazioni sulla risoluzione dei problemi, controllare il messaggio di stato interno. | [Informazioni di riferimento sul modello](/azure/templates/) e [Località supportate](resource-location.md) |
| Conflitto | Si sta richiedendo un'operazione non consentita nello stato corrente della risorsa. Il ridimensionamento del disco, ad esempio, è consentito solo quando viene creata o deallocata una macchina virtuale. | |
| DeploymentActiveAndUnedatable | Attendere il completamento della distribuzione simultanea al gruppo di risorse. | |
| DistribuzioneFailedCleanUp | Quando si distribuisce in modalità completa, tutte le risorse non presenti nel modello vengono eliminate. Questo errore viene visualizzato quando non si dispone delle autorizzazioni appropriate per eliminare tutte le risorse non nel modello. Per evitare l'errore, modificare la modalità di distribuzione in incrementale. | [Modelli di distribuzione Azure Resource Manager](deployment-modes.md) |
| DeploymentNameInvalidCharacters | Il nome della distribuzione può contenere solo lettere, cifre, '-', '.' o '_'. | |
| DeploymentNameLengthLimitExceeded | I nomi delle distribuzioni sono limitati a 64 caratteri.  | |
| DeploymentFailed | DeploymentFailed è un errore generale che non fornisce i dettagli necessari per risolvere l'errore. Nei dettagli cercare un codice di errore che fornisca maggiori informazioni. | [Trovare il codice di errore](#find-error-code) |
| DeploymentQuotaExceeded | Se si raggiunge il limite di 800 distribuzioni per gruppo di risorse, eliminare dalla cronologia le distribuzioni che non sono più necessarie. | [Risolvere l'errore quando il numero di distribuzione supera 800](deployment-quota-exceeded.md) |
| DnsRecordInUse | Il nome del record DNS deve essere univoco. Immettere un nome diverso. | |
| ImageNotFound | Controllare le impostazioni dell'immagine della macchina virtuale. |  |
| InUseSubnetCannotBeDeleted | È possibile che venga visualizzato questo errore quando si tenta di aggiornare una risorsa e la richiesta viene elaborata eliminando e creando la risorsa. Assicurarsi di specificare tutti i valori invariati. | [Aggiornare una risorsa](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Ottenere il token di accesso per il tenant appropriato. È possibile ottenere solo il token dal tenant a cui appartiene l'account. | |
| InvalidContentLink | Molto probabilmente hai tentato di collegarti a un modello nidificato che non è disponibile. Ricontrollare l'URI specificato per il modello annidato. Se il modello si trova in un account di archiviazione, verificare che l'URI sia accessibile. Potrebbe essere necessario passare un token di firma di accesso condiviso. Attualmente non è possibile creare un collegamento a un modello che si trova in un account di archiviazione dietro un firewall di Archiviazione di Azure.Currently, you can't link to a template that is in a storage account behind an [Azure Storage firewall](../../storage/common/storage-network-security.md). Valutare la possibilità di spostare il modello in un altro repository, ad esempio GitHub.Consider moving your template to another repository, like GitHub. | [Modelli collegati](linked-templates.md) |
| InvalidDeploymentLocation | Quando si esegue la distribuzione a livello di sottoscrizione, è stato fornito un percorso diverso per un nome di distribuzione usato in precedenza. | [Distribuzioni a livello di sottoscrizioneSubscription level deployments](deploy-to-subscription.md) |
| InvalidParameter | Uno dei valori forniti per una risorsa non corrisponde al valore previsto. Questo errore può dipendere da molte condizioni diverse. Ad esempio, è possibile che una password non sia sufficiente o che un nome di BLOB non sia corretto. Il messaggio di errore deve indicare quale valore deve essere corretto. | |
| InvalidRequestContent | I valori di distribuzione includono valori non riconosciuti o valori obbligatori mancanti. Confermare i valori per il tipo di risorsa. | [Informazioni di riferimento sul modello](/azure/templates/) |
| InvalidRequestFormat | Abilitare la registrazione di debug durante l'esecuzione della distribuzione e verificare il contenuto della richiesta. | [Registrazione del debug](#enable-debug-logging) |
| InvalidResourceNamespace | Controllare lo spazio dei nomi della risorsa specificato nella proprietà **type**. | [Informazioni di riferimento sul modello](/azure/templates/) |
| InvalidResourceReference | La risorsa non esiste ancora o viene referenziata in modo non corretto. Controllare se è necessario aggiungere una dipendenza. Verificare che l'utilizzo della funzione **reference** includa i parametri necessari per lo scenario in uso. | [Risolvere gli errori relativi alle risorse di Azure non trovate](error-not-found.md) |
| InvalidResourceType | Controllare il tipo di risorsa specificato nella proprietà **type**. | [Informazioni di riferimento sul modello](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registrare la sottoscrizione con il provider di risorse. | [Risoluzione degli errori di registrazione del provider di risorse](error-register-resource-provider.md) |
| InvalidTemplate | Ricercare eventuali errori nella sintassi del modello. | [Risolvere errori dovuti a modelli non validi](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Rimuovere le dipendenze non necessarie. | [Risolvere le dipendenze circolari](error-invalid-template.md#circular-dependency) |
| LinkedAuthorizationFailed | Verificare se l'account appartiene allo stesso tenant del gruppo di risorse in cui si esegue la distribuzione. | |
| LinkedInvalidPropertyId | La risoluzione dell'ID risorsa per una risorsa non è stato eseguito correttamente. Verificare di avere fornito tutti i valori necessari per l'ID risorsa, incluso l'ID sottoscrizione, il nome del gruppo di risorse, il tipo di risorsa, il nome della risorsa padre (se necessario) e il nome della risorsa. | |
| LocationRequired | Specificare un percorso per la risorsa. | [Impostare la posizione](resource-location.md) |
| MismatchingResourceSegments | Assicurarsi che la risorsa nidificata abbia il numero corretto di segmenti nel nome e nel tipo. | [Risolvere i segmenti di risorse](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Controllare lo stato di registrazione del provider di risorse e le posizioni supportate. | [Risoluzione degli errori di registrazione del provider di risorse](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Registrare la sottoscrizione con il provider di risorse. | [Risoluzione degli errori di registrazione del provider di risorse](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Controllare lo stato della registrazione del provider di risorse. | [Risoluzione degli errori di registrazione del provider di risorse](error-register-resource-provider.md) |
| NotFound | È possibile che si stia tentando di distribuire una risorsa dipendente in parallelo con una risorsa padre. Controllare se è necessario aggiungere una dipendenza. | [Risolvere gli errori relativi alle risorse di Azure non trovate](error-not-found.md) |
| OperationNotAllowed | La distribuzione sta tentando di eseguire un'operazione che supera la quota per la sottoscrizione, il gruppo di risorse o l'area. Se possibile, modificare la distribuzione in modo da non superare le quote. In alternativa è possibile richiedere una modifica delle quote. | [Risolvere gli errori di quota delle risorse](error-resource-quota.md) |
| ParentResourceNotFound | Assicurarsi che esista una risorsa padre prima di creare le risorse figlio. | [Risolvere gli errori delle risorse padre](error-parent-resource.md) |
| PasswordTooLong | È possibile che sia stata selezionata una password con troppi caratteri o che il valore della password sia stato convertito in una stringa sicura prima di passarlo come parametro. Se il modello include un parametro **secure string**, non è necessario convertire il valore in una stringa sicura. Specificare il valore della password come testo. |  |
| PrivateIPAddressInReservedRange | L'indirizzo IP specificato include un intervallo di indirizzi richiesto da Azure. Modificare l'indirizzo IP in modo da evitare l'intervallo riservato. | [Indirizzi IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | L'indirizzo IP specificato non è compreso nell'intervallo di subnet. Modificare l'indirizzo IP in modo che sia compreso nell'intervallo di subnet. | [Indirizzi IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Alcune proprietà non possono essere modificate in una risorsa distribuita. Quando si aggiorna una risorsa, è possibile limitare le modifiche alle proprietà consentite. | [Aggiornare una risorsa](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | La sottoscrizione include un criterio delle risorse che impedisce un'azione che si sta tentando di eseguire durante la distribuzione. Individuare il criterio che blocca l'azione. Se possibile, modificare la distribuzione per soddisfare le limitazioni del criterio. | [Errore RequestDisallowedByPolicy con i criteri delle risorse di Azure](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Specificare un nome di risorsa che non includa un nome riservato. | [Nomi di risorse riservati](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Attendere il completamento dell'eliminazione. | |
| ResourceGroupNotFound | Controllare il nome del gruppo della risorse di destinazione per la distribuzione. Il gruppo di risorse di destinazione deve essere già presente nella sottoscrizione. Controllare il contesto della sottoscrizione. | [Interfaccia della riga di comando di Azure](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | La distribuzione referenzia una risorsa di cui non è possibile eseguire la risoluzione. Verificare che l'utilizzo della funzione **reference** includa i parametri necessari per lo scenario in uso. | [Risolvere gli errori relativi ai riferimenti](error-not-found.md) |
| ResourceQuotaExceeded | La distribuzione sta tentando di creare risorse che superano la quota per la sottoscrizione, il gruppo di risorse o l'area. Se possibile, modificare l'infrastruttura in modo da non superare le quote. In alternativa è possibile richiedere una modifica delle quote. | [Risolvere gli errori di quota delle risorse](error-resource-quota.md) |
| SkuNotAvailable | Selezionare lo SKU, ad esempio le dimensioni delle macchine virtuali, disponibile per la posizione selezionata. | [Risolvere gli errori dovuti all'indisponibilità di SKU](error-sku-not-available.md) |
| StorageAccountAlreadyExists | Fornire un nome univoco per l'account di archiviazione. | [Risolvere gli errori relativi ai nomi degli account di archiviazione](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken | Fornire un nome univoco per l'account di archiviazione. | [Risolvere gli errori relativi ai nomi degli account di archiviazione](error-storage-account-name.md) |
| StorageAccountNotFound | Controllare la sottoscrizione, il gruppo di risorse e il nome dell'account di archiviazione che si sta tentando di usare. | |
| SubnetsNotInSameVnet | Una macchina virtuale può avere solo una rete virtuale. Quando si distribuiscono più schede di interfaccia di rete, assicurarsi che appartengano alla stessa rete virtuale. | [Più schede di interfaccia di rete](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNota Registro | Quando si distribuiscono risorse di rete, il provider di risorse Microsoft.Network viene registrato automaticamente nella sottoscrizione. A volte, la registrazione automatica non viene completata in tempo. Per evitare questo errore intermittente, registrare il provider di risorse Microsoft.Network prima della distribuzione. | [Risoluzione degli errori di registrazione del provider di risorse](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Rimuovere le dipendenze non necessarie. | [Risolvere le dipendenze circolari](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Ridurre il numero di gruppi di risorse per una distribuzione singola. | [Distribuzione in più gruppi di risorse](cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Trovare il codice di errore

I possibili errori sono di due tipi:

* errori di convalida
* errori di distribuzione

Gli errori di convalida sono causati da scenari già determinati prima della distribuzione. Tra di essi figurano gli errori di sintassi presenti in un modello oppure il tentativo di distribuire risorse superiori alle quote di sottoscrizione. Gli errori di distribuzione sorgono da condizioni che si verificano durante il processo di distribuzione. Tra essi configurano i tentativi di accedere a una risorsa che viene distribuita in parallelo.

Entrambi i tipi di errore restituiscono un codice necessario per risolvere i problemi della distribuzione. Entrambi i tipi di errori vengono visualizzati nel [log attività](../management/view-activity-logs.md). Gli errori di convalida non sono invece visualizzati nella cronologia della distribuzione, poiché quest'ultima non è mai stata avviata.

### <a name="validation-errors"></a>Errori di convalida

Durante la distribuzione nel portale, viene visualizzato un errore di convalida dopo l'invio dei valori.

![vista dell'errore di convalida nel portale](./media/common-deployment-errors/validation-error.png)

Selezionare il messaggio per avere altre informazioni. Nella figura seguente viene visualizzato un errore **InvalidTemplateDeployment** e un messaggio che indica che la distribuzione è stata bloccata da un criterio.

![visualizzare le informazioni di convalida](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Errori di distribuzione

Quando l'operazione supera la convalida, ma non esegue correttamente la distribuzione, viene visualizzato un errore di distribuzione.

Per visualizzare i codici di errore di distribuzione e i messaggi con PowerShell, usare:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Per visualizzare i codici di errore di distribuzione e i messaggi con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az deployment group operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

Dal portale, selezionare la notifica.

![Notifica di errore](./media/common-deployment-errors/notification.png)

Vengono visualizzati maggiori dettagli sulla distribuzione. Selezionare l'opzione per trovare altre informazioni sull'errore.

![distribuzione non riuscita](./media/common-deployment-errors/deployment-failed.png)

Viene visualizzato il messaggio di errore e i codici di errore. Si noti che sono presenti due codici di errore. Il primo codice di errore, **DeploymentFailed**, è un errore generale che non fornisce i dettagli necessari per risolvere l'errore. Il secondo codice di errore, **StorageAccountNotFound**, contiene i dettagli necessari.

![dettagli dell'errore](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Abilitazione della registrazione di debug

In alcuni casi sono necessarie maggiori informazioni sulla richiesta e sulla risposta per conoscere la causa dell'errore. Durante la distribuzione è possibile richiedere la registrazione di informazioni aggiuntive durante la distribuzione.

### <a name="powershell"></a>PowerShell

In PowerShell impostare il parametro **DeploymentDebugLogLevel** su All, ResponseContent o RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Esaminare il contenuto della richiesta con il cmdlet seguente:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

In alternativa, esaminare il contenuto della risposta con:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Queste informazioni consentono di stabilire se nel modello sia impostato un valore errato.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Attualmente, l'interfaccia della riga di comando di Azure non supporta l'attivazione della registrazione di debug, ma è possibile recuperare la registrazione di debug.

Esaminare le operazioni di distribuzione con il comando seguente:

```azurecli
az deployment group operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Esaminare il contenuto della richiesta con il comando seguente:

```azurecli
az deployment group operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Esaminare il contenuto della risposta con il comando seguente:

```azurecli
az deployment group operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Modello annidato

Per registrare le informazioni di debug relative a un modello nidificato, usare l'elemento **debugSetting**.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2016-09-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

## <a name="create-a-troubleshooting-template"></a>Creare un modello per la risoluzione dei problemi

Talvolta il modo più semplice per risolvere i problemi del modello è testarne alcune parti. A tale scopo, è possibile creare un modello semplificato che consente di concentrarsi sulla parte da cui si ritiene abbia origine l'errore. Si supponga, ad esempio, di ricevere un errore quando si fa riferimento a una risorsa. Anziché gestire un intero modello, crearne uno che restituisca la parte potenzialmente problematica. Ciò può aiutare a capire se i parametri trasmessi siano corretti, a usare correttamente le funzioni del modello e a recuperare le risorse desiderate.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "storageName": {
    "type": "string"
  },
  "storageResourceGroup": {
    "type": "string"
  }
  },
  "variables": {},
  "resources": [],
  "outputs": {
  "exampleOutput": {
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
    "type" : "object"
  }
  }
}
```

In alternativa, si supponga che si stanno ottenendo errori di distribuzione che si ritiene siano correlati a dipendenze impostate in modo non corretto. Testare il modello suddividendolo in modelli semplificati. Creare innanzitutto un modello che distribuisce una sola risorsa (ad esempio SQL Server). Quando si è certi di aver definito correttamente la risorsa, aggiungere una risorsa che dipende da essa (ad esempio un database SQL). Una volta definite correttamente queste due risorse, aggiungere altre risorse che dipendono da esse (ad esempio criteri di controllo). Tra una distribuzione di test e l'altra, eliminare il gruppo di risorse per assicurarsi di testare le dipendenze in modo adeguato.

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire un'esercitazione sulla risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi relativi alle distribuzioni](template-tutorial-troubleshoot.md) di modelli di Resource ManagerTo go through a troubleshooting tutorial, see Tutorial: Troubleshoot Resource Manager template deployments
* Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](../management/view-activity-logs.md).
* Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](deployment-history.md).
