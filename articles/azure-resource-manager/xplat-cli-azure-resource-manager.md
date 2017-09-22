---
title: Gestire le risorse con l'interfaccia della riga di comando di Azure | Documentazione Microsoft
description: Usare l'interfaccia della riga di comando di Azure per gestire le risorse e i gruppi di Azure
editor: 
manager: timlt
documentationcenter: 
author: tfitzmac
services: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 2e3fdf06316bbf68abefe06024f63668bdf07b05
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Usare l'interfaccia della riga di comando di Azure per gestire risorse e gruppi di risorse

Questo articolo illustra come gestire le soluzioni con l'interfaccia della riga di comando di Azure e Azure Resource Manager. Se non si ha familiarità con Resource Manager, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md). Questo argomento è incentrato sulle attività di gestione. Si apprenderà come:

1. Creare un gruppo di risorse
2. Aggiungere una risorsa al gruppo di risorse
3. Aggiungere un tag alla risorsa
4. Eseguire query sulle risorse in base ai nomi o ai valori dei tag
5. Applicare e rimuovere un blocco sulla risorsa
6. Eliminare un gruppo di risorse

Questo articolo non illustra come distribuire un modello di Resource Manager nella sottoscrizione. Per informazioni specifiche, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](resource-group-template-deploy-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per installare e usare l'interfaccia della riga di comando in locale, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

## <a name="set-subscription"></a>Impostare la sottoscrizione

Se si hanno più sottoscrizioni, è possibile passare a un'altra sottoscrizione. Per prima cosa, visualizzare tutte le sottoscrizioni dell'account.

```azurecli-interactive
az account list
```

Viene restituito un elenco di tutte le sottoscrizioni abilitate e disabilitate.

```json
[
  {
    "cloudName": "AzureCloud",
    "id": "<guid>",
    "isDefault": true,
    "name": "Example Subscription One",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<guid>",
    "user": {
      "name": "example@contoso.org",
      "type": "user"
    }
  },
  ...
]
```

Si noti che una sottoscrizione è contrassegnata come predefinita. Questa sottoscrizione è il contesto corrente per le operazioni. Per passare a un'altra sottoscrizione, specificarne il nome con il cmdlet **az account set**.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

Per visualizzare il contesto della sottoscrizione corrente, usare **az account show** senza alcun parametro:

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Prima di distribuire risorse nella sottoscrizione, è necessario creare un gruppo di risorse che conterrà le risorse.

Per creare un gruppo di risorse, usare il comando **az group create**. Il comando usa il parametro **name** per specificare un nome per il gruppo di risorse e il parametro **location** per specificarne la posizione.

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

L'output presenta il formato seguente:

```json
{
  "id": "/subscriptions/<subscription-id>/resourceGroups/TestRG1",
  "location": "southcentralus",
  "managedBy": null,
  "name": "TestRG1",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Se è necessario recuperare il gruppo di risorse in un secondo momento, usare il comando seguente:

```azurecli-interactive
az group show --name TestRG1
```

Per ottenere tutti i gruppi di risorse nella sottoscrizione, usare:

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>Aggiungere risorse a un gruppo di risorse
Per aggiungere una risorsa al gruppo di risorse, è possibile usare il comando **az resource create** oppure un comando specifico del tipo di risorsa che viene creato, ad esempio **az storage account create**. Usare un comando specifico di un tipo di risorsa può risultare più semplice perché può includere i parametri per le proprietà necessarie per la nuova risorsa. Per usare **az resource create**, è necessario conoscere tutte le proprietà da impostare senza che vengano richieste.

Aggiungere una risorsa tramite gli script, tuttavia, potrebbe causare confusione in futuro perché la nuova risorsa non è inclusa in un modello di Azure Resource Manager. I modelli consentono di distribuire la soluzione in modo affidabile e ripetutamente.

Il comando seguente consente di creare un account di archiviazione. Anziché usare il nome indicato nell'esempio, specificare un nome univoco per l'account di archiviazione. Il nome deve essere di lunghezza compresa tra 3 e 24 caratteri e contenere solo numeri e lettere minuscole. Se si usa il nome indicato nell'esempio, verrà visualizzato un errore perché tale nome è già in uso.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

Se è necessario recuperare la risorsa in un secondo momento, usare il comando seguente:

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>Aggiungere un tag

I tag consentono di organizzare le risorse in base a diverse proprietà. È ad esempio possibile che diverse risorse in diversi gruppi di risorse appartengano allo stesso reparto. È possibile applicare un tag e un valore di reparto a tali risorse per contrassegnarle come appartenenti alla stessa categoria oppure indicare se una risorsa viene usata in un ambiente di produzione o di testing. In questo argomento si applicano tag a una sola risorsa, ma nell'ambiente in uso è probabilmente opportuno applicare tag a tutte le risorse.

Il comando seguente applica due tag all'account di archiviazione:

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

I tag vengono aggiornati come un singolo oggetto. Per aggiungere un tag a una risorsa che già include tag, per prima cosa recuperare i tag esistenti. Aggiungere il nuovo tag all'oggetto contenente i tag esistenti e riapplicare tutti i tag alla risorsa.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>Cercare le risorse

Per recuperare le risorse in base a diverse condizioni di ricerca, usare il comando **az resource list**.

* Per ottenere una risorsa in base al nome, specificare il parametro **name**:

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* Per ottenere tutte le risorse in un gruppo di risorse, specificare il parametro **resource-group**:

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* Per ottenere tutte le risorse con un nome e un valore di tag, specificare il parametro **tag**:

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* Per ottenere tutte le risorse con un determinato tipo di risorsa, specificare il parametro **resource-type**:

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="lock-a-resource"></a>Bloccare una risorsa

Quando è necessario assicurarsi che una risorsa strategica non venga eliminata o modificata accidentalmente, applicare un blocco alla risorsa. È possibile specificare **CanNotDelete** o **ReadOnly**.

Per creare o eliminare i blocchi di gestione, è necessario avere accesso alle azioni `Microsoft.Authorization/*` o `Microsoft.Authorization/locks/*`. Fra i ruoli predefiniti, solo a Proprietario e Amministratore Accesso utenti sono concesse tali azioni.

Per applicare un blocco, usare il comando seguente:

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

La risorsa bloccata nell'esempio precedente non potrà essere eliminata finché non verrà rimosso il blocco. Per rimuovere un blocco, usare:

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

Per altre informazioni sull'impostazione di blocchi, vedere l'articolo su come [bloccare le risorse con Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Rimuovere risorse o un gruppo di risorse
È possibile rimuovere una risorsa o un gruppo di risorse. Quando si rimuove un gruppo di risorse, si rimuovono anche tutte le risorse in esso contenute.

* Per eliminare una risorsa del gruppo di risorse, usare il comando delete per il tipo di risorsa che si sta eliminando. Questo comando elimina la risorsa, ma non il gruppo di risorse.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* Per eliminare un gruppo di risorse e tutte le relative risorse, usare il comando **az group delete**.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

Per entrambi i comandi viene richiesto di confermare che si vuole rimuovere la risorsa o il gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla creazione dei modelli, vedere [Authoring Azure Resource Manager templates](resource-group-authoring-templates.md) (Creazione di modelli di Azure Resource Manager).
* Per altre informazioni sulla distribuzione di modelli, vedere  [Deploy an application with Azure Resource Manager template](resource-group-template-deploy-cli.md) (Distribuire un'applicazione con un modello di Azure Resource Manager).
* È possibile spostare le risorse esistenti in un nuovo gruppo di risorse. Per esempi, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).
