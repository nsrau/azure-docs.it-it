---
title: File di inclusione
description: File di inclusione
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b48e5e28b15d17796138d493a7d9c628791df2e6
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830130"
---
## <a name="enable-the-event-grid-resource-provider"></a>Abilitare il provider di risorse di Griglia di eventi

Se è la prima volta che si usa Griglia di eventi nella sottoscrizione di Azure, può essere necessario registrare il provider di risorse di Griglia di eventi. Eseguire il comando seguente per registrare il provider:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

La registrazione può richiedere qualche secondo. Per controllare lo stato, eseguire:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Quando `registrationState` è `Registered`, è possibile continuare.