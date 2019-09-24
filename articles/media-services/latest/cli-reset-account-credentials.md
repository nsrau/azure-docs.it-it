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
ms.openlocfilehash: 09c93e2d851bea22e9d54dde35398f36335eb896
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967582"
---
# <a name="cli-example-reset-the-account-credentials"></a>Esempio di interfaccia della riga di comando: Reimpostare le credenziali dell'account

Lo script dell'interfaccia della riga di comando di Azure in questo articolo mostra come reimpostare le credenziali dell'account e ottenere le impostazioni di app.config.

## <a name="prerequisites"></a>Prerequisiti 

[Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script di esempio

```
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup 
 ```

## <a name="next-steps"></a>Passaggi successivi

* [az ams](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Reimpostare le credenziali](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-reset-credentials)
