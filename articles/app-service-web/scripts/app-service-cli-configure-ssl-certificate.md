---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Associare un certificato SSL personalizzato a un'App Web | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Associare un certificato SSL personalizzato a un'App Web
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/19/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: ab4e211bc2c3f7e99ab0246e7abba57795318245
ms.contentlocale: it-it
ms.lasthandoff: 09/09/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Associare un certificato SSL personalizzato a un'app web

Questo script di esempio crea un'app Web nel servizio app con le relative risorse correlate, quindi associa ad essa il certificato SSL di un nome di dominio personalizzato. Per questo esempio sono necessari gli elementi seguenti:

* Accesso alla pagina di configurazione DNS del registrar.
* File PFX valido e relativa password per il certificato SSL da caricare e associare.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Associare un certificato SSL personalizzato a un'App Web")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Consente di creare un piano di servizio app. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Crea un'App Web di Azure. |
| [az webapp config hostname add](https://docs.microsoft.com/cli/azure/webapp/config/hostname#az_webapp_config_hostname_add) | Esegue il mapping di un dominio personalizzato a un'app Web. |
| [az webapp config ssl upload](https://docs.microsoft.com/cli/azure/webapp/config/ssl#az_webapp_config_ssl_upload) | Carica un certificato SSL in un'app Web. |
| [az webapp config ssl bind](https://docs.microsoft.com/cli/azure/webapp/config/ssl#az_webapp_config_ssl_bind) | Associa un certificato SSL caricato a un'app Web. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../app-service-cli-samples.md).

