---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 894a89126d1ee3ed909134f3e0dd914166568654
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606598"
---
È possibile visualizzare la coda nel [portale di Azure](../articles/storage/queues/storage-quickstart-queues-portal.md) o in [Microsoft Azure Storage Explorer](https://storageexplorer.com/). È anche possibile visualizzare la coda nell'interfaccia della riga di comando di Azure, come descritto nei passaggi seguenti:

1. Aprire il file *local.setting.json* del progetto di funzione e copiare il valore della stringa di connessione. In un terminale o una finestra di comando eseguire il comando seguente per creare una variabile di ambiente denominata `AZURE_STORAGE_CONNECTION_STRING`, incollando la stringa di connessione specifica al posto di `<MY_CONNECTION_STRING>`. Questa variabile di ambiente implica che non è necessario fornire la stringa di connessione a ogni comando successivo usando l'argomento `--connection-string`.

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    export AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Facoltativo) Usare il comando [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) per visualizzare le code di archiviazione dell'account. L'output di questo comando dovrebbe includere una coda denominata `outqueue`, che è stata creata quando la funzione ha scritto il primo messaggio in tale coda.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Usare il comando [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) per leggere il messaggio di questa coda, che dovrebbe essere il primo nome usato per il test della funzione eseguito in precedenza. Il comando legge e rimuove il primo messaggio dalla coda. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Questo script usa certutil per decodificare la raccolta di messaggi con codifica base64 da un file temporaneo locale. Se non è presente alcun output, provare a rimuovere `> NUL` dallo script per interrompere l'eliminazione dell'output di certutil, nel caso in cui si verifichi un errore. 
    
    ---
    
    Poiché il corpo del messaggio viene archiviato [con codifica base64](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding), il messaggio deve essere decodificato prima di essere visualizzato. Dopo l'esecuzione di `az storage message get`, il messaggio viene rimosso dalla coda. Se è presente un solo messaggio in `outqueue`, quando si esegue questo comando una seconda volta non viene recuperato alcun messaggio e si riceve invece un errore.
