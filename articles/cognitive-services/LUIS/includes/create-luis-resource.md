---
title: Create LUIS resource
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: a765ac27936da9da5a2f41464c17491e3561f44b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465909"
---
## <a name="create-a-luis-resource"></a>Create a LUIS resource

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Click [Create **Language Understanding**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Enter all required settings:

    |Impostazione|Value|
    |--|--|
    |name|Desired name (2-64 characters)|
    |Sottoscrizione|Select appropriate subscription|
    |Località|Select any nearby and available location|
    |Piano tariffario|`F0` - the minimal pricing tier|
    |Gruppo di risorse|Select an available resource group|

1. Click **Create** and wait for the resource to be created. After it is created, navigate to the resource page.
1. Collect configured `endpoint` and an API key, see [gathering required parameters](#gathering-required-parameters).

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
