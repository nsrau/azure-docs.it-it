---
title: File di inclusione
description: File di inclusione
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a7ed21d8246b618149aa0d116070a14b033d5370
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869645"
---
## <a name="enable-event-grid-resource-provider"></a>Abilitare il provider di risorse di Griglia di eventi

Se è la prima volta che si usa Griglia di eventi nella sottoscrizione di Azure, è necessario registrare il provider di risorse di Griglia di eventi. Eseguire il comando seguente:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

La registrazione può richiedere qualche minuto. Per controllare lo stato, eseguire:

```azurecli-interactive
az provider show -n Microsoft.EventGrid --query "registrationState"
```

Quando `registrationState` è `Registered`, è possibile continuare.