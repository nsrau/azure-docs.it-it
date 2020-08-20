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
ms.openlocfilehash: b40c936006fa47964ef67ffda37c80eb5732bdf0
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653887"
---
# <a name="create-an-asset"></a>Creare un asset

Questo articolo illustra come creare un asset di servizi multimediali.  Si userà un asset per conservare il contenuto multimediale per la codifica e lo streaming.  Per altre informazioni sugli asset di servizi multimediali, vedere [asset in servizi multimediali di Azure V3](assets-concept.md)

## <a name="prerequisites"></a>Prerequisiti

Per creare un asset, seguire la procedura descritta in [creare un account di servizi multimediali](./create-account-howto.md) per creare l'account di servizi multimediali e il gruppo di risorse necessari.

## <a name="methods"></a>Metodi

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="cli-shell"></a>[Shell dell'interfaccia della riga di comando](#tab/clishell/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>Uso di REST

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>Uso di cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="using-postman"></a>Uso di Postman

[!INCLUDE[Create an asset with Postman](./includes/task-create-asset-postman.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di servizi multimediali](media-services-overview.md)
