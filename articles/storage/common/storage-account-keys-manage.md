---
title: Gestire le chiavi di accesso dell'account
titleSuffix: Azure Storage
description: Informazioni su come visualizzare, gestire e ruotare le chiavi di accesso dell'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.openlocfilehash: 79712c50a5ad46d7d435868606011f458fe48e2e
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87370752"
---
# <a name="manage-storage-account-access-keys"></a>Gestisci chiavi di accesso all'account di archiviazione

Quando si crea un account di archiviazione, Azure genera due chiavi di accesso dell'account di archiviazione a 512 bit. Queste chiavi possono essere usate per autorizzare l'accesso ai dati nell'account di archiviazione tramite l'autorizzazione della chiave condivisa.

Microsoft consiglia di utilizzare Azure Key Vault per gestire le chiavi di accesso e di ruotare e rigenerare periodicamente le chiavi. L'uso di Azure Key Vault rende più semplice ruotare le chiavi senza interruzioni delle applicazioni. È anche possibile ruotare manualmente le chiavi.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Visualizza chiavi di accesso all'account

È possibile visualizzare e copiare le chiavi di accesso dell'account con l'portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Il portale di Azure fornisce anche una stringa di connessione per l'account di archiviazione che è possibile copiare.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per visualizzare e copiare le chiavi di accesso o la stringa di connessione dell'account di archiviazione dal portale di Azure:

1. Passare all'account di archiviazione nel [portale di Azure](https://portal.azure.com).
1. In **Impostazioni** selezionare **Chiavi di accesso**. Verranno visualizzate le chiavi di accesso dell'account, con la stringa di connessione completa per ogni chiave.
1. Individuare il valore della **chiave** in **Key1**e fare clic sul pulsante **Copy (copia** ) per copiare la chiave dell'account.
1. In alternativa, è possibile copiare l'intera stringa di connessione. Trovare il valore **Stringa di connessione** in **key1** e fare clic sul pulsante **Copia** per copiare la stringa di connessione.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Screenshot che illustra come visualizzare le chiavi di accesso nel portale di Azure":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per recuperare le chiavi di accesso all'account con PowerShell, chiamare il comando [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey) .

Nell'esempio seguente viene recuperata la prima chiave. Per recuperare la seconda chiave, usare `Value[1]` anziché `Value[0]` . Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per elencare le chiavi di accesso dell'account con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ storage account keys List](/cli/azure/storage/account/keys#az-storage-account-keys-list) , come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

È possibile usare una delle due chiavi per accedere ad archiviazione di Azure, ma in generale è consigliabile usare la prima chiave e riservare l'uso della seconda chiave per la rotazione delle chiavi.

Per visualizzare o leggere le chiavi di accesso di un account, l'utente deve essere un amministratore del servizio oppure deve essere assegnato un ruolo RBAC che include **Microsoft. storage/storageAccounts/listkeys/Action**. Alcuni ruoli predefiniti di Azure che includono questa azione sono i ruoli del **ruolo del servizio operatore** **proprietario**, **collaboratore**e chiave account di archiviazione. Per altre informazioni sul ruolo di amministratore del servizio, vedere [ruoli di amministratore della sottoscrizione classica, ruoli RBAC di Azure e ruoli Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md). Per informazioni dettagliate sui ruoli predefiniti per archiviazione di Azure, vedere la sezione **archiviazione** in [ruoli predefiniti](../../role-based-access-control/built-in-roles.md#storage)di Azure per il controllo degli accessi in base al ruolo di Azure.

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Usare Azure Key Vault per gestire le chiavi di accesso

Microsoft consiglia di utilizzare Azure Key Vault per gestire e ruotare le chiavi di accesso. L'applicazione può accedere in modo sicuro alle chiavi in Key Vault, in modo che sia possibile evitare di archiviarle con il codice dell'applicazione. Per ulteriori informazioni sull'utilizzo di Key Vault per la gestione delle chiavi, vedere gli articoli seguenti:

- [Gestire le chiavi dell'account di archiviazione con Azure Key Vault e PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Gestire chiavi dell'account di archiviazione con Azure Key Vault e l'interfaccia della riga di comando di Azure](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Ruotare manualmente le chiavi di accesso

Microsoft consiglia di ruotare periodicamente le chiavi di accesso per proteggere l'account di archiviazione. Se possibile, usare Azure Key Vault per gestire le chiavi di accesso. Se non si usa Key Vault, sarà necessario ruotare manualmente le chiavi.

Vengono assegnate due chiavi di accesso che è quindi possibile ruotare. La presenza di due chiavi garantisce che l'applicazione mantenga l'accesso ad archiviazione di Azure durante tutto il processo.

> [!WARNING]
> La rigenerazione delle chiavi di accesso può influire sulle applicazioni o sui servizi di Azure che dipendono dalla chiave dell'account di archiviazione. I client che usano la chiave dell'account per accedere all'account di archiviazione devono essere aggiornati per usare la nuova chiave, inclusi servizi multimediali, applicazioni cloud, desktop e per dispositivi mobili e applicazioni di interfaccia utente grafica per Archiviazione di Azure, ad esempio [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per ruotare le chiavi di accesso dell'account di archiviazione nel portale di Azure:

1. Aggiornare le stringhe di connessione nel codice dell'applicazione per fare riferimento alla chiave di accesso secondaria per l'account di archiviazione.
1. Passare all'account di archiviazione nel [portale di Azure](https://portal.azure.com).
1. In **Impostazioni** selezionare **Chiavi di accesso**.
1. Per rigenerare la chiave di accesso primaria per l'account di archiviazione, selezionare il pulsante **Rigenera** accanto alla chiave di accesso primaria.
1. Aggiornare le stringhe di connessione nel codice in modo che facciano riferimento alla nuova chiave di accesso primaria.
1. Rigenerare la chiave di accesso secondaria nello stesso modo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per ruotare le chiavi di accesso dell'account di archiviazione con PowerShell:

1. Aggiornare le stringhe di connessione nel codice dell'applicazione per fare riferimento alla chiave di accesso secondaria per l'account di archiviazione.
1. Chiamare il comando [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) per rigenerare la chiave di accesso primaria, come illustrato nell'esempio seguente:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Aggiornare le stringhe di connessione nel codice in modo che facciano riferimento alla nuova chiave di accesso primaria.
1. Rigenerare la chiave di accesso secondaria nello stesso modo. Per rigenerare la chiave secondaria, utilizzare `key2` come nome della chiave anziché `key1` .

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per ruotare le chiavi di accesso dell'account di archiviazione con la CLI di Azure:

1. Aggiornare le stringhe di connessione nel codice dell'applicazione per fare riferimento alla chiave di accesso secondaria per l'account di archiviazione.
1. Chiamare il comando [AZ storage account Keys Renew](/cli/azure/storage/account/keys#az-storage-account-keys-renew) per rigenerare la chiave di accesso primaria, come illustrato nell'esempio seguente:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Aggiornare le stringhe di connessione nel codice in modo che facciano riferimento alla nuova chiave di accesso primaria.
1. Rigenerare la chiave di accesso secondaria nello stesso modo. Per rigenerare la chiave secondaria, utilizzare `key2` come nome della chiave anziché `key1` .

---

> [!NOTE]
> Microsoft consiglia di usare solo una delle chiavi in tutte le applicazioni contemporaneamente. Se si usa la Chiave 1 in alcune posizioni e la Chiave 2 in altre, non si potranno ruotare le chiavi senza quale applicazione perda l'accesso.

Per ruotare le chiavi di accesso di un account, l'utente deve essere un amministratore del servizio oppure deve essere assegnato un ruolo RBAC che include **Microsoft. storage/storageAccounts/RegenerateKey/Action**. Alcuni ruoli predefiniti di Azure che includono questa azione sono i ruoli del **ruolo del servizio operatore** **proprietario**, **collaboratore**e chiave account di archiviazione. Per altre informazioni sul ruolo di amministratore del servizio, vedere [ruoli di amministratore della sottoscrizione classica, ruoli RBAC di Azure e ruoli Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md). Per informazioni dettagliate sui ruoli predefiniti di Azure per archiviazione di Azure, vedere la sezione **archiviazione** nei [ruoli predefiniti di Azure per](../../role-based-access-control/built-in-roles.md#storage)il controllo degli accessi in base al ruolo di Azure.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md)
- [Creare un account di archiviazione](storage-account-create.md)
