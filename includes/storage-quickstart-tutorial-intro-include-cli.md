---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 4ad977dc8cbaa85360092dbfd391a3c3b88f67bb
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747942"
---
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con il comando [az group create](/cli/azure/group). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli-interactive
az group create \
    --name <resource-group-name> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Creare un account di archiviazione per utilizzo generico con il comando [az storage account create](/cli/azure/storage/account). L'account di archiviazione per utilizzo generico può essere usato per tutti e quattro i servizi: BLOB, file, tabelle e code.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Specificare le credenziali dell'account di archiviazione

L'interfaccia della riga di comando di Azure richiede le credenziali dell'account di archiviazione per la maggior parte dei comandi usati in questa esercitazione. Anche se esistono diverse opzioni per fornirle, uno dei modi più semplici consiste nell'impostare le variabili di ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_KEY`.

> [!NOTE]
> Questo articolo illustra come impostare le variabili di ambiente usando Bash. Altri ambienti possono richiedere modifiche di sintassi.

Usare prima il comando [az storage account keys list](/cli/azure/storage/account/keys) per visualizzare le chiavi dell'account di archiviazione. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli-interactive
az storage account keys list \
    --account-name <account-name> \
    --resource-group <resource-group-name> \
    --output table
```

Impostare ora le variabili di ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_KEY`. Questa operazione può essere eseguita nella shell Bash con il comando `export`. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```bash
export AZURE_STORAGE_ACCOUNT="<account-name>"
export AZURE_STORAGE_KEY="<account-key>"
```

Per altre informazioni su come recuperare le chiavi di accesso all'account usando il portale di Azure, vedere **Chiavi di accesso** in [Gestire le impostazioni dell'account di archiviazione nel portale di Azure](../articles/storage/common/storage-account-manage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#access-keys).