---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Integrare il servizio app con un gateway applicazione | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Integrare il servizio app con un gateway applicazione
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 8e99d4f1d6de9aa9845ca83dfa68099aad25f9f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88960633"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>Integrare il servizio app con un gateway applicazione tramite l'interfaccia della riga di comando

Questo script di esempio crea un'app Web del servizio app di Azure, una rete virtuale di Azure e un gateway applicazione. Limita quindi il traffico per l'app Web in modo che provenga solo dalla subnet del gateway applicazione.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando di Azure in locale, è necessaria la versione 2.0.74 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, l'app del servizio app, Cosmos DB e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [`az network vnet create`](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) | Crea una rete virtuale. |
| [`az network public-ip create`](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) | Crea un indirizzo IP pubblico. |
| [`az network public-ip show`](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show) | Mostra i dettagli di un indirizzo IP pubblico. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Consente di creare un piano di servizio app. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Crea un'app Web del servizio app. |
| [`az webapp show`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-show) | Mostra i dettagli di un'app Web del servizio app. |
| [`az webapp config access-restriction add`](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest#az-webapp-config-access-restriction-add) | Aggiunge una restrizione di accesso all'app Web del servizio app. |
| [`az network application-gateway create`](/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-create) | Crea un gateway applicazione. |
| [`az network application-gateway http-settings update`](/cli/azure/network/application-gateway/http-settings?view=azure-cli-latest#az-network-application-gateway-http-settings-update) | Aggiorna le impostazioni HTTP del gateway applicazione. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../samples-cli.md).