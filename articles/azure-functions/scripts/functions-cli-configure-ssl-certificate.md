---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Associare un certificato SSL personalizzato a un'app per le funzioni | Documentazione Microsoft
description: Esempio di script dell'interfaccia della riga di comando di Azure - Associare un certificato SSL personalizzato a un'app per le funzioni in Azure
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
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
ms.openlocfilehash: f8e8570d9c3093b5f49b000916644888304eed4e
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>Associare un certificato SSL personalizzato a un'app per le funzioni

Questo script di esempio crea un'app per le funzioni nel servizio app con le relative risorse correlate, quindi associa a essa il certificato SSL di un nome di dominio personalizzato. Per questo esempio è necessario:

* Accesso alla pagina di configurazione DNS del registrar.
* File PFX valido e relativa password per il certificato SSL da caricare e associare.

Per associare un certificato SSL, è necessario creare l'app per le funzioni in un piano di servizio app e non in un piano a consumo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e utilizzare l'interfaccia CLI in locale, è necessario essere in esecuzione l'interfaccia CLI di Azure versione 2.0 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Crea un piano di servizio app necessario per associare i certificati SSL. |
| [az functionapp create]() | Creare un'app per le funzioni. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#az_appservice_web_config_hostname_add) | Esegue il mapping di un dominio personalizzato all'app per le funzioni. |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#az_appservice_web_config_ssl_upload) | Carica un certificato SSL in un'app per le funzioni. |
| [az appservice web config ssl bind](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#az_appservice_web_config_ssl_bind) | Associa un certificato SSL caricato a un'app per le funzioni. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure]().
