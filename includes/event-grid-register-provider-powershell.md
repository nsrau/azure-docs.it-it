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
ms.openlocfilehash: 0946b703b45f69ede409167213f2629a796f18b5
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869628"
---
## <a name="enable-event-grid-resource-provider"></a>Abilitare il provider di risorse di Griglia di eventi

Se è la prima volta che si usa Griglia di eventi nella sottoscrizione di Azure, è necessario registrare il provider di risorse di Griglia di eventi. Eseguire il comando seguente:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.EventGrid
```

La registrazione può richiedere qualche minuto. Per controllare lo stato, eseguire:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Quando `RegistrationStatus` è `Registered`, è possibile continuare.