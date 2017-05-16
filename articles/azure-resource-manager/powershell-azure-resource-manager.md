---
title: Gestire le soluzioni di Azure con PowerShell | Documentazione Microsoft
description: Usare Azure PowerShell e Resource Manager per gestire le risorse.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 674404b135d2e512840505ee0927db98824aa8b1
ms.lasthandoff: 04/27/2017


---
# <a name="manage-resources-with-azure-powershell-and-resource-manager"></a>Gestire le risorse con Azure PowerShell e Resource Manager
> [!div class="op_single_selector"]
> * [Portale](resource-group-portal.md)
> * [Interfaccia della riga di comando di Azure](xplat-cli-azure-resource-manager.md)
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [API REST](resource-manager-rest-api.md)
>
>

Questo articolo illustra come gestire le soluzioni con Azure PowerShell e Azure Resource Manager. Se non si ha familiarità con Resource Manager, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md). Questo argomento è incentrato sulle attività di gestione. Si apprenderà come:

1. Creare un gruppo di risorse
2. Aggiungere una risorsa al gruppo di risorse
3. Aggiungere un tag alla risorsa
4. Eseguire query sulle risorse in base ai nomi o ai valori dei tag
5. Applicare e rimuovere un blocco sulla risorsa
6. Eliminare un gruppo di risorse

Questo articolo non illustra come distribuire un modello di Resource Manager nella sottoscrizione. Per informazioni in merito, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](resource-group-template-deploy.md).

## <a name="get-started-with-azure-powershell"></a>guida introduttiva ad Azure PowerShell

Se Azure PowerShell non è installato, vedere l'articolo su [come installare e configurare Azure PowerShell](/powershell/azure/overview).

Se Azure PowerShell è stato installato in passato ma non è stato aggiornato di recente, è consigliabile installare l'ultima versione. È possibile aggiornare la versione con lo stesso metodo usato per l'installazione. Se è stata usata l'Installazione guidata piattaforma Web, ad esempio, avviarla di nuovo e cercare un aggiornamento.

Per controllare la versione del modulo Resources di Azure, usare il cmdlet seguente:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Questo argomento è stato aggiornato per la versione 3.3.0. Se si ha una versione precedente, l'esperienza utente potrebbe non corrispondere ai passaggi illustrati in questo argomento. Per la documentazione sui cmdlet di questa versione, vedere [AzureRM.Resources Module](/powershell/module/azurerm.resources) (Modulo AzureRM.Resources).

## <a name="log-in-to-your-azure-account"></a>Accedere all'account Azure
Prima di usare la soluzione, è necessario accedere al proprio account.

Per accedere al proprio account Azure, usare il cmdlet **Login-AzureRmAccount**.

```powershell
Login-AzureRmAccount
```

Il cmdlet richiede le credenziali di accesso per l'account di Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell.

Il cmdlet restituisce informazioni sull'account e la sottoscrizione da usare per le attività.

```powershell
Environment           : AzureCloud
Account               : example@contoso.com
TenantId              : {guid}
SubscriptionId        : {guid}
SubscriptionName      : Example Subscription One
CurrentStorageAccount :

```

Se si hanno più sottoscrizioni, è possibile passare a un'altra sottoscrizione. Per prima cosa, visualizzare tutte le sottoscrizioni dell'account.

```powershell
Get-AzureRmSubscription
```

Vengono restituite le sottoscrizioni abilitate e disabilitate.

```powershell
SubscriptionName : Example Subscription One
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Two
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Three
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Disabled
```

Per passare a un'altra sottoscrizione, specificarne il nome con il cmdlet **Set-AzureRmContext**.

```powershell
Set-AzureRmContext -SubscriptionName "Example Subscription Two"
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Prima di distribuire risorse nella sottoscrizione, è necessario creare un gruppo di risorse che conterrà le risorse.

Per creare un gruppo di risorse, usare il cmdlet **New-AzureRmResourceGroup** . Il comando usa il parametro **Name** per specificare un nome per il gruppo di risorse e il parametro **Location** per specificarne la posizione.

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location "South Central US"
```

L'output presenta il formato seguente:

```powershell
ResourceGroupName : TestRG1
Location          : southcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1
```

Se è necessario recuperare il gruppo di risorse in un secondo momento, usare il cmdlet seguente:

```powershell
Get-AzureRmResourceGroup -ResourceGroupName TestRG1
```

Per ottenere tutti i gruppi di risorse della sottoscrizione, non specificare un nome:

```powershell
Get-AzureRmResourceGroup
```

## <a name="add-resources-to-a-resource-group"></a>Aggiungere risorse a un gruppo di risorse
Per aggiungere una risorsa al gruppo di risorse, è possibile usare il cmdlet **New-AzureRmResource** oppure un cmdlet specifico del tipo di risorsa che viene creato (ad esempio, **New-AzureRmStorageAccount**). Usare un cmdlet specifico di un tipo di risorsa può risultare più semplice perché il cmdlet include i parametri per le proprietà necessarie per la nuova risorsa. Per usare **New-AzureRmResource**, è necessario conoscere tutte le proprietà da impostare senza che vengano richieste.

Aggiungere una risorsa tramite i cmdlet, tuttavia, potrebbe causare confusione in futuro perché la nuova risorsa non è inclusa in un modello di Resource Manager. È consigliabile definire l'infrastruttura per la soluzione di Azure in un modello di Resource Manager. I modelli consentono di distribuire la soluzione in modo affidabile e ripetutamente. Per questo argomento viene creato un account di archiviazione con un cmdlet di PowerShell, ma successivamente viene generato un modello dal gruppo di risorse.

Il cmdlet seguente crea un account di archiviazione. Anziché usare il nome indicato nell'esempio, specificare un nome univoco per l'account di archiviazione. Il nome deve essere di lunghezza compresa tra 3 e 24 caratteri e contenere solo numeri e lettere minuscole. Se si usa il nome indicato nell'esempio, verrà visualizzato un errore perché tale nome è già in uso.

```powershell
New-AzureRmStorageAccount -ResourceGroupName TestRG1 -AccountName mystoragename -Type "Standard_LRS" -Location "South Central US"
```

Se è necessario recuperare la risorsa in un secondo momento, usare il cmdlet seguente:

```powershell
Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1
```

## <a name="add-a-tag"></a>Aggiungere un tag

I tag consentono di organizzare le risorse in base a diverse proprietà. È ad esempio possibile che diverse risorse in diversi gruppi di risorse appartengano allo stesso reparto. È possibile applicare un tag e un valore di reparto a tali risorse per contrassegnarle come appartenenti alla stessa categoria oppure indicare se una risorsa viene usata in un ambiente di produzione o di testing. In questo argomento si applicano tag a una sola risorsa, ma nell'ambiente in uso è probabilmente opportuno applicare tag a tutte le risorse.

Il cmdlet seguente applica due tag all'account di archiviazione:

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
 ```

I tag vengono aggiornati come un singolo oggetto. Per aggiungere un tag a una risorsa che già include tag, per prima cosa recuperare i tag esistenti. Aggiungere il nuovo tag all'oggetto contenente i tag esistenti e riapplicare tutti i tag alla risorsa.

```powershell
$tags = (Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
```

## <a name="search-for-resources"></a>Cercare le risorse

Per recuperare le risorse in base a diverse condizioni di ricerca, usare il cmdlet **Find-AzureRmResource**.

* Per ottenere una risorsa in base al nome, specificare il parametro **ResourceNameContains**:

  ```powershell
  Find-AzureRmResource -ResourceNameContains mystoragename
  ```

* Per ottenere tutte le risorse in un gruppo di risorse, specificare il parametro **ResourceGroupNameContains**:

  ```powershell
  Find-AzureRmResource -ResourceGroupNameContains TestRG1
  ```

* Per ottenere tutte le risorse con un nome e un valore di tag, specificare i parametri **TagName** e **TagValue**:

  ```powershell
  Find-AzureRmResource -TagName Dept -TagValue IT
  ```

* Per ottenere tutte le risorse con un determinato tipo di risorsa, specificare il parametro **ResourceType**:

  ```powershell
  Find-AzureRmResource -ResourceType Microsoft.Storage/storageAccounts
  ```

## <a name="lock-a-resource"></a>Bloccare una risorsa

Quando è necessario assicurarsi che una risorsa strategica non venga eliminata o modificata accidentalmente, applicare un blocco alla risorsa. È possibile specificare **CanNotDelete** o **ReadOnly**.

Per creare o eliminare i blocchi di gestione, è necessario avere accesso alle azioni `Microsoft.Authorization/*` o `Microsoft.Authorization/locks/*`. Fra i ruoli predefiniti, solo a Proprietario e Amministratore Accesso utenti sono concesse tali azioni.

Per applicare un blocco, usare il cmdlet seguente:

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

La risorsa bloccata nell'esempio precedente non potrà essere eliminata finché non verrà rimosso il blocco. Per rimuovere un blocco, usare:

```powershell
Remove-AzureRmResourceLock -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

Per altre informazioni sull'impostazione di blocchi, vedere l'articolo su come [bloccare le risorse con Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Rimuovere risorse o un gruppo di risorse
È possibile rimuovere una risorsa o un gruppo di risorse. Quando si rimuove un gruppo di risorse, si rimuovono anche tutte le risorse in esso contenute.

* Per eliminare una risorsa dal gruppo di risorse, usare il cmdlet **Remove-AzureRmResource** . Questo cmdlet elimina la risorsa, ma non il gruppo di risorse.

  ```powershell
  Remove-AzureRmResource -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
  ```

* Per eliminare un gruppo di risorse e tutte le relative risorse, usare il cmdlet **Remove-AzureRmResourceGroup**.

  ```powershell
  Remove-AzureRmResourceGroup -Name TestRG1
  ```

Per entrambi i cmdlet viene richiesto di confermare che si vuole rimuovere la risorsa o il gruppo di risorse. Se l'eliminazione della risorsa o del gruppo di risorse viene completata, l'operazione restituisce **True**.

## <a name="run-resource-manager-scripts-with-azure-automation"></a>Eseguire script di Resource Manager con Automazione di Azure

Questo argomento illustra come eseguire operazioni di base sulle risorse con Azure PowerShell. Per scenari di gestione più avanzati, è in genere opportuno creare uno script e riusare tale script in base alle esigenze o a una pianificazione. [Automazione di Azure](../automation/automation-intro.md) consente di automatizzare gli script di uso frequente che gestiscono le soluzioni di Azure.

Gli argomenti seguenti illustrano come usare Automazione di Azure, Resource Manager e PowerShell per eseguire attività di gestione in modo efficace:

- Per informazioni sulla creazione di un runbook, vedere [Il primo runbook PowerShell](../automation/automation-first-runbook-textual-powershell.md).
- Per informazioni sull'uso di raccolte di script, vedere [Raccolte di runbook e moduli per Automazione di Azure](../automation/automation-runbook-gallery.md).
- Per informazioni sui runbook per l'avvio e l'arresto di macchine virtuali, vedere [Scenario di Automazione di Azure: uso di tag in formato JSON per creare una pianificazione per l'avvio e l'arresto di una macchina virtuale di Azure](../automation/automation-scenario-start-stop-vm-wjson-tags.md).
- Per informazioni sui runbook per l'avvio e l'arresto di macchine virtuali durante gli orari di minore attività, vedere [Soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività in Automazione di Azure](../automation/automation-solution-vm-management.md).

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla creazione dei modelli, vedere [Authoring Azure Resource Manager templates](resource-group-authoring-templates.md) (Creazione di modelli di Azure Resource Manager).
* Per altre informazioni sulla distribuzione di modelli, vedere  [Deploy an application with Azure Resource Manager template](resource-group-template-deploy.md) (Distribuire un'applicazione con un modello di Azure Resource Manager).
* È possibile spostare le risorse esistenti in un nuovo gruppo di risorse. Per esempi, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).


