---
title: Spostare un'app Web di Azure in un altro gruppo di risorse | Microsoft Docs
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
ms.date: 10/19/2017
ms.author: zarizvi
ms.openlocfilehash: 4d5d6540dd0b51a4f172649a1fd9a2343efeceee
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2017
---
# <a name="move-an-azure-web-app-to-another-resource-group"></a>Spostare un'app Web di Azure in un altro gruppo di risorse
È possibile spostare un'app Web e/o le relative risorse correlate in un altro gruppo di risorse nella stessa sottoscrizione o in un gruppo di risorse di una sottoscrizione diversa. Questa operazione viene eseguita nell'ambito della gestione standard delle risorse in Azure. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md).

## <a name="limitations-when-moving-within-the-same-subscription"></a>Limitazioni nello spostamento all'interno della stessa sottoscrizione

Quando si sposta un'app Web _nella stessa sottoscrizione_, non è possibile spostare i certificati SSL caricati. È comunque possibile spostare un'app Web nel nuovo gruppo di risorse senza spostare il relativo certificato SSL caricato mantenendo effettiva la funzionalità SSL dell'app. 

Se si desidera spostare il certificato SSL con l'app Web, attenersi alla procedura seguente:

1.  Eliminare il certificato caricato dall'app Web.
2.  Spostare l'app Web.
3.  Caricare il certificato nell'app Web spostata.

## <a name="limitations-when-moving-across-subscriptions"></a>Limitazioni nello spostamento tra sottoscrizioni

Quando si sposta un'app Web _tra sottoscrizioni_, si applicano le limitazioni seguenti:

- Il gruppo di risorse di destinazione non deve contenere risorse del servizio app esistenti. Le risorse del servizio app includono:
    - App Web
    - Piani di servizio app
    - Certificati SSL importati o caricati
    - Ambienti del servizio app
- Tutte le risorse del servizio app nel gruppo di risorse devono essere spostate insieme.
- Le risorse del servizio app possono essere spostate solo dal gruppo di risorse in cui sono state originariamente create. Se una risorsa del servizio app non si trova più nel gruppo di risorse originale, deve essere spostata nuovamente in tale gruppo prima di poter essere spostata tra le sottoscrizioni. 
