---
title: Creare un&quot;app per le funzioni e distribuire codice di funzione da GitHub | Documentazione Microsoft
description: Creare un&quot;app per le funzioni e distribuire codice di funzione da GitHub
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: sample
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 152dfe6a9bafada83457d3518fbc3758dd34cde0
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---
# <a name="create-an-app-service"></a>Creare un servizio app

Questo script di esempio crea un'app per le funzioni usando il [piano a consumo](../functions-scale.md#consumption-plan) con le relative risorse correlate e distribuisce in modo continuo il codice di funzione da un archivio GitHub. In questo esempio è necessario:

* Un archivio GitHub contenente il codice di funzione per il quale si hanno autorizzazioni amministrative.
* [Token di accesso personale](https://help.github.com/articles/creating-an-access-token-for-command-line-use) per il proprio account GitHub.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

Questo esempio crea un'app per le funzioni di Azure e distribuisce il codice di funzione da GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Servizio di Azure")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Ogni comando della tabella include collegamenti alla documentazione specifica del comando. Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Consente di creare un piano di servizio app. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#delete) |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Associa un'app per le funzioni con un archivio Git o Mercurial. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).

