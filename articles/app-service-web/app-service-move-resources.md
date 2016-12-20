---
title: Spostamento di risorse di app Web a un altro gruppo di risorse
description: "Descrive gli scenari dove è possibile spostare le app Web e servizi app da un gruppo di risorse a un altro."
services: app-service
documentationcenter: 
author: ZainRizvi
manager: wpickett
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2016
ms.author: zarizvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 08b8953b91a87a131f70309e41be3a9ce0123299


---
# <a name="supported-move-configurations"></a>Configurazioni di spostamento supportate
È possibile spostare le risorse app Web di Azure usando l' [API di spostamento delle risorse ARM](../resource-group-move-resources.md).

App Web di Microsoft Azure supporta attualmente i seguenti scenari di spostamento:

* Spostamento dell'intero contenuto di un gruppo di risorse (app Web, piani di servizio app e certificati) in un altro gruppo di risorse 
  * Nota: il gruppo di risorse di destinazione non può contenere alcuna risorsa Microsoft.Web in questo scenario
* Spostamento di singole app Web in un gruppo di risorse diverse, mentre sono ancora ospitate nel relativo piano di servizio app corrente (il piano di servizio app rimane nel gruppo di risorse precedente)




<!--HONumber=Nov16_HO3-->


