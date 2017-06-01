---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Associare un certificato SSL personalizzato a un&quot;App Web | Microsoft Docs
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Associare un certificato SSL personalizzato a un&quot;App Web
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
ms.date: 04/10/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 27da647a10a52af40333a962c75c73cab93861d7
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Associare un certificato SSL personalizzato a un'app web

Questo script di esempio crea un'app Web nel servizio app con le relative risorse correlate, quindi associa ad essa il certificato SSL di un nome di dominio personalizzato. Per questo esempio sono necessari gli elementi seguenti:

* Accesso alla pagina di configurazione DNS del registrar.
* File PFX valido e relativa password per il certificato SSL da caricare e associare.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Associare un certificato SSL personalizzato a un'App Web")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Consente di creare un piano di servizio app. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Crea un'App Web di Azure. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Esegue il mapping di un dominio personalizzato a un'app Web. |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | Carica un certificato SSL in un'app Web. |
| [az appservice web config ssl bind](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | Associa un certificato SSL caricato a un'app Web. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../app-service-cli-samples.md).

