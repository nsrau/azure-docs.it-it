---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 56fa96810b9e47e817c64ecc1a0df4e6a0b3db93
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38731776"
---
[!INCLUDE [resource group intro text](resource-group.md)]

In Cloud Shell creare un gruppo di risorse con il comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *West Europe*. Per visualizzare tutte le località supportate per il servizio app in Linux nel livello **Standard**, eseguire il comando [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

In genere, il gruppo di risorse e le risorse vengono create in un'area nelle vicinanze. 

Al termine del comando, un output JSON mostra le proprietà del gruppo di risorse.