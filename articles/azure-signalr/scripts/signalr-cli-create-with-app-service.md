---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un servizio SignalR con un servizio app | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un servizio SignalR con un servizio app
services: signalr
documentationcenter: signalr
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 0532cb81c766c2d039b978ab4ba79c812915bbc0
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "41918244"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>Creare un servizio SignalR con un servizio app

Questo script di esempio crea una nuova risorsa servizio Azure SignalR che viene usata per eseguire il push in tempo reale degli aggiornamenti del contenuto ai client. Questo script aggiunge anche una nuova app Web e un nuovo piano di servizio app per ospitare l'app Web ASP.NET Core che usa il servizio SignalR. L'app Web è configurata con un'impostazione app denominata *AzureSignalRConnectionString* per la connessione alla nuova risorsa servizio SignalR.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio

Questo script usa l'estensione *signalr* per l'interfaccia della riga di comando di Azure. Eseguire il comando seguente per installare l'estensione *signalr* per l'interfaccia della riga di comando di Azure prima di usare questo script di esempio:

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service/create-signalr-with-app-service.sh "Create a new Azure SignalR Service and Web App")]

Annotare il nome effettivo generato per il nuovo gruppo di risorse. Sarà visualizzato nell'output. Il nome del gruppo di risorse viene usato quando si vuole eliminare tutte le risorse di gruppo.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Ogni comando della tabella include collegamenti alla documentazione specifica del comando. Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az signalr create](/cli/azure/ext/signalr/signalr#ext-signalr-az-signalr-create) | Crea una risorsa servizio Azure SignalR. |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | Elencare le chiavi che saranno usate dall'applicazione per il push in tempo reale degli aggiornamenti del contenuto con SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Crea un piano del servizio app di Azure per l'hosting di app Web. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Crea un'app Web di Azure usando il piano di servizio app host. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Aggiunge una nuova impostazione app per l'app Web. Questa impostazione app viene usata per archiviare la stringa di connessione SignalR. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando del servizio Azure SignalR sono disponibili nella [documentazione del servizio Azure SignalR](../signalr-cli-samples.md).
