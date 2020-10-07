---
title: Registrare il provider di risorse della soluzione Azure VMware
description: Procedura per registrare il provider di risorse della soluzione Azure VMware.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 96d15546c5102a69a0b19f92de33d35d2e9ab6c2
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575742"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Per usare la soluzione Azure VMware, è prima necessario registrare il provider di risorse con la sottoscrizione.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>In alternativa è possibile usare l'interfaccia utente grafica per registrare il provider di risorse **Microsoft.AVS**.  Per altre informazioni, vedere l'articolo [Registrare il provider di risorse e i tipi di risorse](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).  
