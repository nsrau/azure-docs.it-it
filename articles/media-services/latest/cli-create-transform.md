---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una trasformazione | Microsoft Docs
description: Le trasformazioni descrivono un semplice flusso di lavoro di attività per l'elaborazione dei file video o audio, detto spesso "recipe". Lo script dell'interfaccia della riga di comando di Azure in questo articolo mostra come creare una trasformazione.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: c21a16d043f972042949d6340985774741b3df6a
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888616"
---
# <a name="cli-example-create-a-transform"></a>Esempio di interfaccia della riga di comando: Creare un oggetto Transform

Lo script dell'interfaccia della riga di comando di Azure in questo articolo mostra come creare una trasformazione. Le trasformazioni descrivono un semplice flusso di lavoro di attività per l'elaborazione dei file video o audio, spesso noto come "recipe". È sempre opportuno verificare se esiste già una trasformazione con il nome e il "recipe" desiderati. In caso affermativo, è consigliabile riutilizzarla.

## <a name="prerequisites"></a>Prerequisiti 

[Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> È possibile specificare solo un percorso di un file JSON predefinito del codificatore standard personalizzato per [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Vedere l'esempio di [codifica con una trasformazione personalizzata](custom-preset-cli-howto.md).
>
> Non è possibile passare un nome file quando si usa [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Passaggi successivi

[az ams transform (interfaccia della riga di comando)](https://docs.microsoft.com/cli/azure/ams/transform?view=azure-cli-latest)
