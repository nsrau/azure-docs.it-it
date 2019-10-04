---
title: File di inclusione
description: File di inclusione
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/18/2018
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 458782011623721bb44771d42caa32130e23bbc9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180634"
---
[!INCLUDE [resource group intro text](resource-group.md)]

In Cloud Shell creare un gruppo di risorse con il comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *South Central US*. Per visualizzare tutte le località supportate per il servizio app nel livello **gratuito**, eseguire il comando [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

In genere, il gruppo di risorse e le risorse vengono create in un'area vicina alla località dell'utente. 

Al termine del comando, un output JSON visualizza le proprietà del gruppo di risorse.