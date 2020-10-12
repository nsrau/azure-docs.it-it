---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 195caa6f8d7c00c741741fbf80a77bd7fe5579b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465053"
---
Il comando dell'interfaccia della riga di comando seguente negherà tutto il traffico diretto all'endpoint pubblico dell'account di archiviazione. Si noti che il parametro `-bypass` di questo comando è impostato su `AzureServices`. Questa impostazione consentirà ai servizi Microsoft attendibili, come Sincronizzazione file di Azure, di accedere all'account di archiviazione tramite l'endpoint pubblico.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```