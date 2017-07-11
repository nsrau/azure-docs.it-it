---
title: Abilitazione di Acquisizione di Hub eventi di Azure tramite il portale | Microsoft Docs
description: "Abilitare la funzionalità Acquisizione di Hub eventi usando il portale di Azure."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1f3d373944b909db290f6cf2da7bf12a8a00e1c5
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---

<a id="enable-event-hubs-capture-using-the-azure-portal" class="xliff"></a>

# Abilitare Acquisizione di Hub eventi usando il portale di Azure

È possibile configurare Acquisizione al momento della creazione dell'hub eventi usando il [portale di Azure](https://portal.azure.com). Per abilitare Acquisizione, fare clic sul pulsante **Sì** nel pannello del portale **Crea hub eventi**. Per configurare un account di archiviazione e un contenitore, fare quindi clic sulla sezione **Contenitore** del pannello. Poiché Acquisizione di Hub eventi usa l'autenticazione da servizio a servizio con la risorsa di archiviazione, non è necessario specificare una stringa di connessione di archiviazione. Il selettore risorse seleziona automaticamente l'URI della risorsa per l'account di archiviazione. Se si usa Azure Resource Manager, è necessario specificare questo URI in modo esplicito come stringa.

L'intervallo di tempo predefinito è di 5 minuti. Il valore minimo è 1, quello massimo 15. La finestra **Dimensione** ha un intervallo compreso tra 10 e 500 MB.

![][1]

<a id="adding-capture-to-an-existing-event-hub" class="xliff"></a>

## Aggiunta di Acquisizione a un hub eventi esistente

È possibile configurare Acquisizione in hub eventi esistenti negli spazi dei nomi di Hub eventi. La funzionalità non è disponibile per gli spazi dei nomi di tipo **Messaggistica** o **Misto** meno recenti. Per abilitare Acquisizione in un hub eventi esistente o per modificarne le impostazioni, fare clic sullo spazio dei nomi per caricare il pannello **Informazioni di base**, quindi fare clic sull'hub eventi per cui si vuole abilitare o modificare l'impostazione di Acquisizione. Infine fare clic sulla sezione **Proprietà** del pannello aperto, come illustrato nella figura seguente:

![][2]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png

<a id="next-steps" class="xliff"></a>

## Passaggi successivi

È anche possibile configurare Acquisizione di Hub eventi usando i modelli di Azure Resource Manager. Per altre informazioni, vedere [Enable Capture using an Azure Resource Manager template](event-hubs-resource-manager-namespace-event-hub-enable-capture.md) (Abilitare Acquisizione usando un modello di Azure Resource Manager).

