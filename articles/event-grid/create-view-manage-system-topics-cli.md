---
title: Creare, visualizzare e gestire gli argomenti del sistema di griglia di eventi di Azure usando l'interfaccia della riga
description: Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare, visualizzare ed eliminare gli argomenti di sistema.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 354afb89b145e288f525e40ad700e8f8a67c6dad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86115044"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Creare, visualizzare e gestire argomenti di sistema di griglia di eventi usando l'interfaccia della riga di comando di Azure
Questo articolo illustra come creare e gestire gli argomenti di sistema usando l'interfaccia della riga di comando di Azure. Per una panoramica degli argomenti di sistema, vedere [argomenti di sistema](system-topics.md).

## <a name="install-extension-for-azure-cli"></a>Installare l'estensione per l'interfaccia della riga di comando di Azure
Per l'interfaccia della riga di comando di Azure, è necessaria l'[estensione Griglia di eventi](/cli/azure/azure-cli-extensions-list).

In Cloud Shell:

- Se l'estensione è stata installata in precedenza, aggiornarla: `az extension update -n eventgrid`
- Se l'estensione non è stata installata in precedenza, installarla:  `az extension add -n eventgrid`

Per un'installazione locale:

1. [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) Assicurarsi di avere la versione più recente installata controllando con `az --version`.
2. Disinstallare le versioni precedenti dell'estensione: `az extension remove -n eventgrid`
3. Installare l'estensione eventgrid con `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Creare un argomento di sistema

- Per creare prima un argomento di sistema in un'origine di Azure e quindi creare una sottoscrizione di eventi per tale argomento, vedere gli argomenti di riferimento seguenti:
    - [AZ eventgrid System-topic create](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-create)

        ```azurecli-interactive
        # Get the ID of the Azure source (for example: Azure Storage account)
        storageid=$(az storage account show \
                --name <AZURE STORAGE ACCOUNT NAME> \
                --resource-group <AZURE RESOURCE GROUP NAME> \
                    --query id --output tsv)
    
        # Create the system topic on the Azure source (example: Azure Storage account)
        az eventgrid system-topic create \
            -g <AZURE RESOURCE GROUP NAME> \
            --name <SPECIFY SYSTEM TOPIC NAME> \
            --location <LOCATION> \
            --topic-type microsoft.storage.storageaccounts \
            --source $storageid
        ```           

        Per un elenco di `topic-type` valori che è possibile usare per creare un argomento di sistema, eseguire il comando seguente. I valori dei tipi di argomento rappresentano le origini eventi che supportano la creazione di argomenti di sistema. Ignorare `Microsoft.EventGrid.Topics` e `Microsoft.EventGrid.Domains` dall'elenco. 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [AZ eventgrid System-topic Event-Subscription create](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Per creare un argomento di sistema (in modo implicito) quando si crea una sottoscrizione di eventi per un'origine di Azure, usare il metodo [AZ eventgrid Event-Subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-event-subscription-create) . Ecco un esempio:
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    Per un'esercitazione con istruzioni dettagliate, vedere [sottoscrivere un account di archiviazione](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account).

## <a name="view-all-system-topics"></a>Visualizza tutti gli argomenti di sistema
Per visualizzare tutti gli argomenti di sistema e i dettagli di un argomento di sistema selezionato, usare i comandi seguenti:

- [AZ eventgrid System-elenco di argomenti](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [AZ eventgrid System-topic Show](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>Eliminare un argomento di sistema
Per eliminare un argomento di sistema, usare il comando seguente: 

- [AZ eventgrid System-topic Delete](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>Passaggi successivi
Vedere la sezione [argomenti di sistema in griglia di eventi di Azure](system-topics.md) per altre informazioni sugli argomenti di sistema e i tipi di argomento supportati da griglia di eventi di Azure. 