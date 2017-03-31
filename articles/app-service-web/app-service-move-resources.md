---
title: Spostamento di risorse di app Web a un altro gruppo di risorse
description: "Descrive gli scenari dove è possibile spostare le app Web e servizi app da un gruppo di risorse a un altro."
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: zarizvi
translationtype: Human Translation
ms.sourcegitcommit: 078833a21cd309087b842e21e3fc730695c254dd
ms.openlocfilehash: 89fdeca4ffc1a36e8aff829b00483f208fa03e0f
ms.lasthandoff: 12/21/2016


---
# <a name="supported-move-configurations"></a>Configurazioni di spostamento supportate
È possibile spostare le risorse di App Web di Azure usando l'[API di spostamento delle risorse di Resource Manager](../azure-resource-manager/resource-group-move-resources.md).

App Web di Microsoft Azure supporta attualmente i seguenti scenari di spostamento:

* Spostamento dell'intero contenuto di un gruppo di risorse (app Web, piani di servizio app e certificati) in un altro gruppo di risorse. 
   > [!Note]
   > In questo scenario il gruppo di risorse di destinazione non può contenere alcuna risorsa Microsoft.Web.

* Spostamento di singole app Web in un altro gruppo di risorse. Le app Web rimangono tuttavia ospitate nel relativo piano di servizio app (il piano di servizio app rimane nel gruppo di risorse precedente).



