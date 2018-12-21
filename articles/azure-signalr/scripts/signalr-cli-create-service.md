---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un servizio SignalR
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un servizio SignalR
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 364a8b6574b06aa2403ea028fecd0676ba0342a7
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256214"
---
# <a name="create-a-signalr-service"></a>Servizio Azure SignalR 

Questo script di esempio crea una nuova risorsa di servizio SignalR Azure in un nuovo gruppo di risorse con un nome casuale.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio

Questo script usa l'estensione *signalr* per l'interfaccia della riga di comando di Azure. Eseguire il comando seguente per installare l'estensione *signalr* per l'interfaccia della riga di comando di Azure prima di usare questo script di esempio:

```azurecli-interactive
az extension add -n signalr
```

Questo script crea una nuova risorsa servizio SignalR e un nuovo gruppo di risorse. 

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-service-and-group/create-signalr-service-and-group.sh "Creates a new Azure SignalR Service resource and resource group")]

Annotare il nome effettivo generato per il nuovo gruppo di risorse. Il nome del gruppo di risorse viene usato quando si vuole eliminare tutte le risorse di gruppo.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Ogni comando della tabella include collegamenti alla documentazione specifica del comando. Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az signalr create](/cli/azure/group#az-group-create) | Crea una risorsa servizio Azure SignalR. |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | Elencare le chiavi che saranno usate dall'applicazione per il push in tempo reale degli aggiornamenti del contenuto con SignalR. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando del servizio Azure SignalR sono disponibili nella [documentazione del servizio Azure SignalR](../signalr-cli-samples.md).
