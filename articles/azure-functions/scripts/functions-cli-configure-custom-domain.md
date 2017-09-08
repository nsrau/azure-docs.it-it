---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Eseguire il mapping di un dominio personalizzato a un'app per le funzioni | Documentazione Microsoft
description: Esempio di script dell'interfaccia della riga di comando di Azure - Eseguire il mapping di un dominio personalizzato a un'app per le funzioni in Azure.
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/01/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 5a17830ad3b879382b7e82d435f1286c7ecdf7c5
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---
# <a name="map-a-custom-domain-to-a-function-app"></a>Esegue il mapping di un dominio personalizzato a un'app per le funzioni

Questo script di esempio crea un'app per le funzioni con le relative risorse e quindi esegue il mapping di `www.<yourdomain>` a essa. Per eseguire il mapping a un dominio personalizzato, è necessario creare l'app per le funzioni in un piano di servizio app e non in un piano a consumo. Funzioni di Azure supporta il mapping di un dominio personalizzato solo usando un record A.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, è necessario usare la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Eseguire il mapping di un dominio personalizzato a un'app per le funzioni")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Lo script usa i seguenti comandi: ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Crea un account di archiviazione necessario per l'app per le funzioni. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crea un piano di servizio app necessario per eseguire il mapping di un dominio personalizzato. |
| [az functionapp create]() | Creare un'app per le funzioni. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Esegue il mapping di un dominio personalizzato a un'app per le funzioni. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure]().

