---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 733fc9620cbd17e5e4d8bb101c54ff646a06d6a4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050264"
---
### <a name="app-service-plan"></a>Piano di servizio app
Consente di creare il piano di servizio per ospitare l'app Web. Fornire il nome del piano tramite il parametro **hostingPlanName** . Il percorso del piano è identico a quello usato per il gruppo di risorse. Il piano tariffario e le dimensioni dei processi di lavoro sono specificati nei parametri **sku** e **workerSize**.

```config
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('hostingPlanName')]",
  "type": "Microsoft.Web/serverfarms",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[parameters('sku')]",
    "capacity": "[parameters('workerSize')]"
  },
  "properties": {
    "name": "[parameters('hostingPlanName')]"
  }
},
```
