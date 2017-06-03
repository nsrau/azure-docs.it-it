---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Associare un certificato SSL personalizzato a un&quot;app per le funzioni | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Associare un certificato SSL personalizzato a un&quot;app per le funzioni in Azure
services: functions
documentationcenter: 
author: ggailey777
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 04/10/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 9b7f84f5ad01335a3ce72b6b2aa654968fdd9499
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>Associare un certificato SSL personalizzato a un'app per le funzioni

Questo script di esempio crea un'app per le funzioni nel servizio app con le relative risorse correlate, quindi associa a essa il certificato SSL di un nome di dominio personalizzato. Per questo esempio sono necessari gli elementi seguenti:

* Accesso alla pagina di configurazione DNS del registrar.
* File PFX valido e relativa password per il certificato SSL da caricare e associare.

Per associare un certificato SSL, è necessario creare l'app per le funzioni in un piano di servizio app e non in un piano a consumo.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Associare un certificato SSL personalizzato a un'App Web")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crea un piano di servizio app necessario per associare i certificati SSL. |
| [az functionapp create]() | Creare un'app per le funzioni. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Esegue il mapping di un dominio personalizzato all'app per le funzioni. |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | Carica un certificato SSL in un'app per le funzioni. |
| [az appservice web config ssl bind](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | Associa un certificato SSL caricato a un'app per le funzioni. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure]().

