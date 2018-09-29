---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una trasformazione | Microsoft Docs
description: Usare lo script dell'interfaccia della riga di comando di Azure per creare una trasformazione.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: cbe20c4b75fff27fad60446fb933a9c8ba2e3312
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091779"
---
# <a name="cli-example-create-a-transform"></a>Esempio dell'interfaccia della riga di comando: Creare una trasformazione

Lo script dell'interfaccia della riga di comando di Azure in questo articolo mostra come creare una trasformazione. Le trasformazioni descrivono un semplice flusso di lavoro di attività per l'elaborazione dei file video o audio, spesso noto come "recipe". È sempre opportuno verificare se esiste già una trasformazione con il nome e il "recipe" desiderati. In caso affermativo, è consigliabile riutilizzarla.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede la versione 2.0.20 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi, vedere [Azure CLI samples](../cli-samples.md) (Esempi dell'interfaccia della riga di comando di Azure).
