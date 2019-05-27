---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 87fd6b626efb60c7fc7ec8896f2c3758ae5cc33c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132881"
---
### <a name="app-service-plan"></a>Piano di servizio app
Consente di creare il piano di servizio per ospitare l'app Web. Fornire il nome del piano tramite il parametro **hostingPlanName** . Il percorso del piano è identico a quello usato per il gruppo di risorse. Il piano tariffario e le dimensioni dei processi di lavoro sono specificati nei parametri **sku** e **workerSize**.

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

