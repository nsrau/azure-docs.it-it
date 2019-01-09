---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Ridimensionare l'app a livello globale con Gestione traffico | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Ridimensionare un'app del servizio app a livello globale con un'architettura a disponibilità elevata
services: appservice
documentationcenter: appservice
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: e4033a50-0e05-4505-8ce8-c876204b2acc
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 3165904eb7757b43d35a4fa3051d29d10bce70a6
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632909"
---
# <a name="scale-an-app-service-app-worldwide-with-a-high-availability-architecture-using-azure-cli"></a>Ridimensionare un'app del servizio app a livello globale con un'architettura a disponibilità elevata usando l'interfaccia della riga di comando di Azure

Questo script di esempio crea un gruppo di risorse, due piani di servizio app, due app, un profilo di Gestione traffico e due endpoint di gestione traffico. Dopo aver completato l'esercizio si avrà un'architettura a disponibilità elevata che fornisce la disponibilità globale dell'app in base alla latenza di rete più bassa.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, è necessaria la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/scale-geographic/scale-geographic.sh "Geographic Scale")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, un'app del servizio app, un profilo di Gestione traffico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Consente di creare un piano di servizio app. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Consente di creare un'app del servizio app. |
| [`az network traffic-manager profile create`](/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) | Consente di crea un profilo di Gestione traffico di Azure. |
| [`az network traffic-manager endpoint create`](/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) | Aggiunge un endpoint a un profilo di Gestione traffico di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../samples-cli.md).
