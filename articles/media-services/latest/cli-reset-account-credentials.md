---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Reimpostare le credenziali dell'account | Microsoft Docs
description: Usare lo script dell'interfaccia della riga di comando di Azure per reimpostare le credenziali dell'account e ottenere le impostazioni di app.config.
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
ms.date: 08/20/2019
ms.author: juliako
ms.custom: devx-track-azurecli
ms.openlocfilehash: 16eaf5ab9b0af97e18a7253d132516a16003d25b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489980"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Esempio di interfaccia della riga di comando di Azure: Reimpostare le credenziali dell'account

Lo script dell'interfaccia della riga di comando di Azure in questo articolo mostra come reimpostare le credenziali dell'account e ottenere le impostazioni di app.config.

## <a name="prerequisites"></a>Prerequisiti

[Creare un account di Servizi multimediali di Azure](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script di esempio

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Passaggi successivi

* [az ams](/cli/azure/ams)
* [Reimpostare le credenziali](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
