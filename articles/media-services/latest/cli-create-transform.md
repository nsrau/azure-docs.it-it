---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una trasformazione | Microsoft Docs
description: Usare lo script dell'interfaccia della riga di comando di Azure per creare una trasformazione.
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
ms.openlocfilehash: 7fea8d997d25d6075e30620e7fd3527003c6a8bb
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128800"
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
