---
title: Caricare il contenuto in un asset di Servizi multimediali di Azure usando l'interfaccia della riga di comando di Azure
description: Lo script dell'interfaccia della riga di comando di Azure in questo argomento mostra come creare un asset di Servizi multimediali di Azure in cui caricare contenuto.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7b362ba30baf13211dc41a6043dcedd05bd506ea
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585416"
---
# <a name="create-an-asset"></a>Creare un asset

Questo articolo illustra come creare un asset di Servizi multimediali.  L'asset verrà usato per includere contenuti multimediali per la codifica e lo streaming.  Per altre informazioni sugli asset di Servizi multimediali, leggere [Asset in Servizi multimediali di Azure v3](assets-concept.md)

## <a name="prerequisites"></a>Prerequisiti

Seguire la procedura disponibile in [Creare un account di Servizi multimediali](./create-account-howto.md) per creare l'account e il gruppo di risorse di Servizi multimediali necessari per creare un asset.

## <a name="methods"></a>Metodi

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="cli-shell"></a>[Shell dell'interfaccia della riga di comando](#tab/clishell/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

---

## <a name="next-steps"></a>Passaggi successivi

[Gestire le risorse](manage-asset-concept.md)
