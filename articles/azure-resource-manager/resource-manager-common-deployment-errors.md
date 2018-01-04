---
title: Risolvere errori comuni durante la distribuzione di risorse in Azure | Microsoft Docs
description: Descrive come risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager.
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: errore di distribuzione, distribuzione di azure, distribuire in azure
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: tomfitz
ms.openlocfilehash: ca7e3cb541948e6cc0b8d077616f3611e3ab2477
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager

Questo argomento descrive alcuni errori comuni che possono verificarsi durante la distribuzione di risorse in Azure e fornisce indicazioni sulla relativa risoluzione. Se non si trova il codice di errore per l'errore di distribuzione specifico, vedere [Trovare il codice di errore](#find-error-code).

## <a name="error-codes"></a>Codici di errore

| Codice di errore | Mitigazione | Altre informazioni |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Seguire le limitazioni relative all'assegnazione dei nomi per gli account di archiviazione. | [Risolvere gli errori relativi ai nomi degli account di archiviazione](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Controllare le proprietà dell'account di archiviazione disponibili. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Il cluster o l'area non ha risorse disponibili o non può supportare le dimensioni della VM richieste. Ripetere la richiesta in un secondo momento oppure richiedere una dimensione di macchina virtuale diversa. | [Problemi di provisioning e allocazione per Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md) e [Problemi di provisioning e allocazione per Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) |
| AnotherOperationInProgress | Attendere il completamento dell'operazione simultanea. | |
| AuthorizationFailed | L'account o l'entità servizio non dispone dell'accesso sufficiente per completare la distribuzione. Selezionare il ruolo a cui appartiene l'account e il relativo accesso per l'ambito della distribuzione. | [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md) |
| RichiestaNonValida | I valori della distribuzione inviati non corrispondono ai valori previsti da Gestione risorse. Per informazioni sulla risoluzione dei problemi, controllare il messaggio di stato interno. | [Informazioni di riferimento sul modello](/azure/templates/) e [Località supportate](resource-manager-templates-resources.md#location) |
| Conflitto | Si sta richiedendo un'operazione non consentita nello stato corrente della risorsa. Il ridimensionamento del disco, ad esempio, è consentito solo quando viene creata o deallocata una macchina virtuale. | |
| DeploymentActive | Attendere il completamento della distribuzione simultanea al gruppo di risorse. | |
| DnsRecordInUse | Il nome del record DNS deve essere univoco. Specificare un nome diverso o modificare il record esistente. | |
| ImageNotFound | Controllare le impostazioni dell'immagine della macchina virtuale. |  |
| InUseSubnetCannotBeDeleted | Questo errore può verificarsi quando si tenta di aggiornare una risorsa, ma la richiesta viene elaborata eliminando e creando la risorsa. Assicurarsi di specificare tutti i valori invariati. | [Aggiornare una risorsa](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Ottenere il token di accesso per il tenant appropriato. È possibile ottenere solo il token dal tenant a cui appartiene l'account. | |
| InvalidContentLink | È probabile che si sia tentato il collegamento a un modello annidato non disponibile. Ricontrollare l'URI specificato per il modello annidato. Se il modello si trova in un account di archiviazione, verificare che l'URI sia accessibile. Potrebbe essere necessario passare un token di firma di accesso condiviso. | [Modelli collegati](resource-group-linked-templates.md) |
| InvalidParameter | Uno dei valori forniti per una risorsa non corrisponde al valore previsto. Questo errore può dipendere da molte condizioni diverse. Ad esempio, è possibile che una password non sia sufficiente o che un nome di BLOB non sia corretto. Controllare il messaggio di errore per determinare se è necessario correggere il valore. | |
| InvalidRequestContent | I valori della distribuzione contengono valori non previsti o mancano valori obbligatori. Confermare i valori per il tipo di risorsa. | [Informazioni di riferimento sul modello](/azure/templates/) |
| InvalidRequestFormat | Abilitare la registrazione del debug quando si esegue la distribuzione e verificare il contenuto della richiesta. | [Registrazione del debug](resource-manager-troubleshoot-tips.md#enable-debug-logging) |
| InvalidResourceNamespace | Controllare lo spazio dei nomi della risorsa specificato nella proprietà **type**. | [Informazioni di riferimento sul modello](/azure/templates/) |
| InvalidResourceReference | La risorsa non esiste ancora o viene referenziata in modo non corretto. Controllare se è necessario aggiungere una dipendenza. Verificare che l'utilizzo della funzione **reference** includa i parametri necessari per lo scenario in uso. | [Risolvere gli errori relativi alle risorse di Azure non trovate](resource-manager-not-found-errors.md) |
| InvalidResourceType | Controllare il tipo di risorsa specificato nella proprietà **type**. | [Informazioni di riferimento sul modello](/azure/templates/) |
| InvalidTemplate | Ricercare eventuali errori nella sintassi del modello. | [Risolvere errori dovuti a modelli non validi](resource-manager-invalid-template-errors.md) |
| LinkedAuthorizationFailed | Controllare se l'account appartiene allo stesso tenant del gruppo di risorse in fase di distribuzione. | |
| LinkedInvalidPropertyId | La risoluzione dell'ID risorsa per una risorsa non è stata eseguita correttamente. Verificare di avere fornito tutti i valori necessari per l'ID risorsa, incluso l'ID sottoscrizione, il nome del gruppo di risorse, il tipo di risorsa, il nome della risorsa padre (se necessario) e il nome della risorsa. | |
| LocationRequired | Fornire una posizione per la risorsa. | [Impostare la posizione](resource-manager-templates-resources.md#location) |
| MissingRegistrationForLocation | Controllare lo stato della registrazione del provider di risorse e le posizioni supportate. | [Risoluzione degli errori di registrazione del provider di risorse](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Registrare la sottoscrizione con il provider di risorse. | [Risoluzione degli errori di registrazione del provider di risorse](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Controllare lo stato della registrazione del provider di risorse. | [Risoluzione degli errori di registrazione del provider di risorse](resource-manager-register-provider-errors.md) |
| NotFound | È possibile che si stia tentando di distribuire una risorsa dipendente in parallelo con una risorsa padre. Controllare se è necessario aggiungere una dipendenza. | [Risolvere gli errori relativi alle risorse di Azure non trovate](resource-manager-not-found-errors.md) |
| OperationNotAllowed | La distribuzione sta tentando di eseguire un'operazione che supera la quota per la sottoscrizione, il gruppo di risorse o l'area. Se possibile, modificare la distribuzione in modo da non superare le quote. In alternativa è possibile richiedere una modifica delle quote. | [Risolvere gli errori di quota delle risorse](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Assicurarsi che esista una risorsa padre prima di creare le risorse figlio. | [Risolvere gli errori delle risorse padre](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | L'indirizzo IP specificato include un intervallo di indirizzi richiesto da Azure. Modificare l'indirizzo IP in modo da evitare l'intervallo riservato. | [Indirizzi IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | L'indirizzo IP specificato non è compreso nell'intervallo di subnet. Modificare l'indirizzo IP in modo che sia compreso nell'intervallo di subnet. | [Indirizzi IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Alcune proprietà non possono essere modificate per una risorsa distribuita. Quando si aggiorna una risorsa, è possibile limitare le modifiche alle proprietà consentite. | [Aggiornare una risorsa](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | La sottoscrizione include un criterio di risorsa che impedisce di eseguire un'azione desiderata in fase distribuzione. Individuare il criterio che blocca l'azione. Se possibile, modificare la distribuzione in modo da soddisfare le limitazioni del criterio. | [Errore RequestDisallowedByPolicy con i criteri delle risorse di Azure](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Specificare un nome di risorsa che non includa un nome riservato. | [Nomi di risorse riservati](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Attendere il completamento dell'eliminazione. | |
| ResourceGroupNotFound | Controllare il nome del gruppo della risorse di destinazione per la distribuzione. Deve esistere già nella sottoscrizione. Controllare il contesto della sottoscrizione. | [Interfaccia della riga di comando di Azure](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | La distribuzione referenzia una risorsa di cui non è possibile eseguire la risoluzione. Verificare che l'utilizzo della funzione **reference** includa i parametri necessari per lo scenario in uso. | [Risolvere gli errori relativi ai riferimenti](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | La distribuzione sta tentando di creare risorse che superano la quota per la sottoscrizione, il gruppo di risorse o l'area. Se possibile, modificare l'infrastruttura in modo da non superare le quote. In alternativa è possibile richiedere una modifica delle quote. | [Risolvere gli errori di quota delle risorse](resource-manager-quota-errors.md) |
| SkuNotAvailable | Selezionare lo SKU, ad esempio le dimensioni delle macchine virtuali, disponibile per la posizione selezionata. | [Risolvere gli errori dovuti all'indisponibilità di SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Fornire un nome univoco per l'account di archiviazione. | [Risolvere gli errori relativi ai nomi degli account di archiviazione](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Fornire un nome univoco per l'account di archiviazione. | [Risolvere gli errori relativi ai nomi degli account di archiviazione](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Controllare la sottoscrizione, il gruppo di risorse e il nome dell'account di archiviazione che si sta tentando di usare. | |
| SubnetsNotInSameVnet | Una macchina virtuale può avere solo una rete virtuale. Quando si distribuiscono più schede di interfaccia di rete, assicurarsi che appartengano alla stessa rete virtuale. | [Più schede di interfaccia di rete](../virtual-machines/windows/multiple-nics.md) |

## <a name="find-error-code"></a>Trovare il codice di errore

Quando si verifica un errore in fase di distribuzione, Gestione risorse restituisce un codice di errore. È possibile esaminare questo messaggio di errore tramite il portale, PowerShell o l'interfaccia della riga di comando di Azure. Il messaggio di errore esterno potrebbe essere troppo generale per la risoluzione dei problemi. Cercare il messaggio interno che contiene informazioni dettagliate sull'errore. Per altre informazioni, vedere [Determinare il codice di errore](resource-manager-troubleshoot-tips.md#determine-error-code).

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).
* Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](resource-manager-deployment-operations.md).
