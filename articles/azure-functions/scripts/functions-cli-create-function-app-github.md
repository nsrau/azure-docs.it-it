---
title: Creare un'app per le funzioni e distribuire codice di funzione da GitHub | Documentazione Microsoft
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un'app per le funzioni e distribuire codice di funzione da GitHub
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: d67e85f91c80efe464fceb1105243bedfba83a0f
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---
# <a name="create-a-function-app-and-deploy-function-code-from-github"></a>Creare un'app per le funzioni e distribuire codice di funzione da GitHub

Questo script di esempio crea un'app per le funzioni usando il [piano a consumo](../functions-scale.md#consumption-plan) con le relative risorse correlate e distribuisce il codice di funzione da un archivio GitHub pubblico (senza la distribuzione continua). Per la distribuzione continua del codice di funzione da GitHub, leggere [Create a function app and continuously deploy from GitHub](functions-cli-create-function-app-github-continuous.md) (Creare un'app per le funzioni ed eseguire la distribuzione continua da GitHub)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio

Questo esempio crea un'app per le funzioni di Azure e distribuisce il codice di funzione da GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github/deploy-function-app-with-function-github.sh?highlight=3 "Creare un'app per le funzioni con la distribuzione da GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Ogni comando della tabella include collegamenti alla documentazione specifica del comando. Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Consente di creare un piano di servizio app. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Crea un'app per le funzioni di Azure. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Associa un'app per le funzioni con un archivio Git o Mercurial. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).

