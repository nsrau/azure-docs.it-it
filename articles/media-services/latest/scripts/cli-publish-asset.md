---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Pubblicare un asset | Microsoft Docs
description: Usare lo script dell'interfaccia della riga di comando di Azure per pubblicare un asset.
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
ms.openlocfilehash: 9b23fba88f8a6f6d7ecd211f8ad9c137cb92789d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723086"
---
# <a name="cli-example-publish-an-asset"></a>Esempio dell'interfaccia della riga di comando: Pubblicare un asset

Lo script dell'interfaccia della riga di comando di Azure in questo articolo mostra come creare un localizzatore di streaming e ottenere gli URL di streaming. 

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede la versione 2.0.20 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi, vedere [Azure CLI samples](../cli-samples.md) (Esempi dell'interfaccia della riga di comando di Azure).
