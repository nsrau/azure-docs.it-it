---
title: "Avvio rapido: Creare una query condivisa con l'interfaccia della riga di comando di Azure"
description: Questa guida di avvio rapido illustra la procedura per abilitare l'estensione Resource Graph per l'interfaccia della riga di comando di Azure e creare una query condivisa.
ms.date: 06/29/2020
ms.topic: quickstart
ms.openlocfilehash: c5f7e6fbe1b462c1f9b6e8ad46c598398e1aca02
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050948"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>Avvio rapido: Creare una query condivisa di Resource Graph usando l'interfaccia della riga di comando di Azure

Il primo passaggio per usare Azure Resource Graph con l'[interfaccia della riga di comando di Azure](/cli/azure/) prevede di verificare che l'estensione sia installata. Questa guida introduttiva illustra il processo di aggiunta dell'estensione all'installazione dell'interfaccia della riga di comando di Azure. È possibile usare l'estensione con l'interfaccia della riga di comando di Azure installata in locale o tramite [Azure Cloud Shell](https://shell.azure.com).

Alla fine di questo processo l'estensione risulterà aggiunta all'installazione dell'interfaccia della riga di comando di Azure scelta e si creerà una query condivisa di Resource Graph.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Aggiungere l'estensione Resource Graph

Per consentire all'interfaccia della riga di comando di Azure di funzionare con Azure Resource Graph è necessario che l'estensione sia aggiunta. Questa estensione funziona ovunque sia possibile usare l'interfaccia della riga di comando di Azure, fra cui [bash in Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) sia autonomo che nel portale e l'[immagine Docker di Azure PowerShell](https://hub.docker.com/r/microsoft/azure-cli/), oppure installata in locale.

1. Controllare che sia installata l'interfaccia della riga di comando di Azure più recente o almeno la versione **2.8.0**. Se non è ancora installato, seguire [queste istruzioni](/cli/azure/install-azure-cli-windows).

1. Nell'ambiente dell'interfaccia della riga di comando di Azure preferito usare [az extension add](/cli/azure/extension#az-extension-add) per importare l'estensione Resource Graph con il comando seguente:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Verificare che l'estensione sia stata installata e che la versione sia quella prevista (almeno **1.1.0**) usando [az extension list](/cli/azure/extension#az-extension-list):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Creare una query condivisa di Resource Graph

Ora che l'estensione dell'interfaccia della riga di comando di Azure è stata aggiunta all'ambiente scelto, è possibile creare una query condivisa di Resource Graph. La query condivisa è un oggetto Azure Resource Manager a cui è possibile concedere autorizzazioni o che è possibile eseguire in Azure Resource Graph Explorer. Nella query viene riepilogato il conteggio di tutte le risorse raggruppate in base alla _località_.

1. Creare un gruppo di risorse con [az group create](/cli/azure/group#az-group-create) per archiviare la query condivisa di Azure Resource Graph. Questo gruppo di risorse è denominato `resource-graph-queries` e la località è `westus2`.

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. Creare la query condivisa di Azure Resource Graph usando l'estensione `graph` e il comando [az graph shared-query create](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-create):

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. Elencare le query condivise nel nuovo gruppo di risorse. Il comando [az graph shared-query list](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-list) restituisce una matrice di valori.

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. Per ottenere un singolo risultato per la query condivisa, usare il comando [az graph shared-query show](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-show).

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. Eseguire la query condivisa nell'interfaccia della riga di comando di Azure con la sintassi `{{shared-query-uri}}` in un comando [az graph query](/cli/azure/ext/resource-graph/graph#ext-resource-graph-az-graph-query).
   Prima di tutto, copiare il campo `id` dal risultato del comando `show` precedente. Sostituire il testo `shared-query-uri` nell'esempio con il valore del campo `id`, sempre racchiuso tra `{{` e `}}`.

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > La sintassi `{{shared-query-uri}}` è una funzionalità di **anteprima**.

Per trovare le query condivise di Resource Graph è anche possibile usare il portale di Azure. Nel portale usare la barra di ricerca per cercare "query di Resource Graph". Selezionare la query condivisa. Nella scheda **Query** della pagina **Panoramica** viene visualizzata la query salvata. Il pulsante **Modifica** consente di aprirla in [Resource Graph Explorer](./first-query-portal.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere la query condivisa di Resource Graph, il gruppo di risorse e l'estensione dall'ambiente dell'interfaccia della riga di comando di Azure, è possibile usare i comandi seguenti:

- [az graph shared-query delete](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-delete)
- [az group delete](/cli/azure/group#az-group-delete)
- [az extension remove](/cli/azure/extension#az-extension-remove)

```azurecli-interactive
# Delete the Azure Resource Graph shared query
az graph shared-query delete --resource-group 'resource-graph-queries' \
   --name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group without prompting for confirmation
az group delete --resource-group 'resource-graph-queries' --yes

# Remove the Azure Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata aggiunta l'estensione Resource Graph all'ambiente dell'interfaccia della riga di comando di Azure ed è stata creata una query condivisa. Per altre informazioni sul linguaggio di Resource Graph, passare alla pagina dei dettagli del linguaggio di query.

> [!div class="nextstepaction"]
> [Altre informazioni sul linguaggio di query](./concepts/query-language.md)