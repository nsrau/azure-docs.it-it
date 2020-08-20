---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 9f079be76d4d75f05b4a8e132ac425255eed8558
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605978"
---
<!--Create a media services asset CLI-->

Il comando seguente dell'interfaccia della riga di comando di Azure consente di creare un nuovo asset di Servizi multimediali. Sostituire i valori `assetName` `amsAccountName` e `resourceGroup` con i valori attualmente usati.

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
