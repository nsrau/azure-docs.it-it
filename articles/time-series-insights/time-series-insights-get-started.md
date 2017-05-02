---
title: Creare un ambiente Azure Time Series Insights | Microsoft Docs
description: In queste esercitazione viene illustrato come creare un ambiente Time Series, connetterlo a un&quot;origine evento e prepararsi ad analizzare i dati dell&quot;evento in pochi minuti.
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: eb710795916a2d7beea75a6408a0982fb4dc8750
ms.lasthandoff: 04/26/2017

---

# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Creare un nuovo ambiente Time Series Insights nel portale di Azure

L'ambiente Time Series Insights è una risorsa di Azure con capacità di inserimento e archiviazione. I clienti effettuano il provisioning degli ambienti tramite il portale di Azure con la capacità necessaria.

## <a name="steps-to-create-the-environment"></a>Procedura per creare l'ambiente

Seguire questa procedura per creare l'ambiente:

1.    Accedere al [portale di Azure](https://portal.azure.com).
2.    Fare clic sul segno più ("+") nell'angolo superiore sinistro.
3.    Cercare "Time Series Insights" nella casella di ricerca.

  ![Creare l'ambiente Time Series Insights](media/get-started/getstarted-create-environment1.png)

4.    Selezionare "Time Series Insights" e fare clic su "Crea".

  ![Creare il gruppo di risorse Time Series Insights](media/get-started/getstarted-create-environment2.png)

5.    Specificare il nome dell'ambiente. Questo nome rappresenterà l'ambiente nell'[utilità di esplorazione delle serie temporali](https://insights.timeseries.azure.com).
6.    Selezionare una sottoscrizione. Sceglierne una contenente l'origine evento. Time Series Insights può rilevare automaticamente le risorse di Hub IoT di Azure e di Hub eventi esistenti nella stessa sottoscrizione.
7.    Selezionare o creare un gruppo di risorse. Un gruppo di risorse è una raccolta di risorse di Azure usate insieme.
8.    Selezionare un percorso di hosting. Per evitare di spostare i dati tra i data center, scegliere un percorso contenente l'origine evento.
9.    Selezione di un piano tariffario.
10.    Selezionare la capacità. È possibile modificare la capacità di un ambiente dopo la creazione.
11.    Creare l'ambiente. È anche possibile aggiungere l'ambiente al dashboard per semplificare l'accesso.

  ![Creare l'aggiunta al dashboard per Time Series Insights](media/get-started/getstarted-create-environment3.png)

## <a name="next-steps"></a>Passaggi successivi

* [Definire i criteri di accesso ai dati](time-series-insights-data-access.md) per accedere all'ambiente nel [portale di Time Series Insights](https://insights.timeseries.azure.com)
* [Creare un'origine evento](time-series-insights-add-event-source.md)
* [Inviare eventi](time-series-insights-send-events.md) all'origine evento

