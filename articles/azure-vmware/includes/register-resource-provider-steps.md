---
title: Registrare il provider di risorse della soluzione Azure VMware
description: Procedura per registrare il provider di risorse della soluzione Azure VMware.
ms.topic: include
ms.date: 09/04/2020
ms.openlocfilehash: 08741181b778b44289868a9f3fb1c2368955248c
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512369"
---
Per usare la soluzione Azure VMware, è prima necessario registrare il provider di risorse con la sottoscrizione.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Per informazioni su come registrare il provider di risorse in altri modi, vedere [Provider e tipi di risorse di Azure](../../azure-resource-manager/management/resource-providers-and-types.md).