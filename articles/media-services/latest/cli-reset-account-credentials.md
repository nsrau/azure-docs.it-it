---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Reimpostare le credenziali dell'account | Microsoft Docs
description: Usare lo script dell'interfaccia della riga di comando di Azure per reimpostare le credenziali dell'account e ottenere le impostazioni di app.config.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: da997ec71655231876749d9f3aa65ba06fd3a1f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295836"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Esempio di interfaccia della riga di comando di Azure: Reimpostare le credenziali dell'account

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

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
