---
title: Aggiungere un'origine evento a un ambiente Azure Time Series Insights | Microsoft Docs
description: In questa esercitazione si connette un'origine evento a un ambiente Time Series Insights
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: ffa2eaf3680e68ac14aabf49b6308caeb173fd43
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017

---

<a id="create-an-event-source-for-your-time-series-insights-environment-using-the-ibiza-portal" class="xliff"></a>

# Creare un'origine evento per un ambiente Time Series Insights con il portale di Ibiza

Un'origine evento di Time Series Insights deriva da un gestore eventi, ad esempio Hub eventi di Azure. Time Series Insights si connette direttamente alle origini evento, inserendo il flusso di dati senza che gli utenti debbano scrivere neppure una riga di codice. Time Series Insights supporta attualmente gli hub eventi di Azure e gli hub IoT di Azure. In futuro verranno aggiunte altre origini evento.

<a id="steps-to-add-an-event-source-to-your-environment" class="xliff"></a>

## Procedura per aggiungere un'origine evento all'ambiente

1.  Accedere al [portale di Ibiza](https://portal.azure.com).
2.  Fare clic su "Tutte le risorse" nel menu sul lato sinistro del portale di Ibiza.
3.  Selezionare l'ambiente Time Series Insights.

  ![Creare l'origine evento di Time Series Insights](media/add-event-source/getstarted-create-event-source-1.png)

4.  Selezionare "Origini evento" e fare clic su "+ Aggiungi".

  ![Creare l'origine evento di Time Series Insights: dettagli](media/add-event-source/getstarted-create-event-source-2.png)

5.  Specificare il nome dell'origine evento. Questo nome viene associato a tutti gli eventi provenienti dall'origine evento ed è disponibile in fase di query.
6.  Selezionare un hub eventi dall'elenco di risorse di Hub eventi nella sottoscrizione corrente. In alternativa scegliere l'opzione di importazione "Specificare le impostazioni dell'hub eventi manualmente" per specificare l'hub eventi di un'altra sottoscrizione. Gli eventi devono essere pubblicati in formato JSON.
7.  Selezionare i criteri con autorizzazione di lettura nell'hub eventi.
8.  Specificare il gruppo di consumer dell'hub eventi.

  > [!IMPORTANT]
  > Verificare che questo gruppo di consumer non venga usato da altri servizi, ad esempio un processo di Analisi di flusso o un altro ambiente Time Series Insights. Se il gruppo di consumer viene usato da altri servizi, l'operazione di lettura viene compromessa per questo ambiente e per gli altri servizi. Se si usa "$Default" come gruppo di consumer, è probabile che venga usato anche da altri lettori.

9.  Fare clic su "Crea".

Dopo la creazione dell'origine evento, Time Series Insights inizierà automaticamente a trasmettere i dati nell'ambiente.

<a id="next-steps" class="xliff"></a>

## Passaggi successivi

* [Inviare eventi](time-series-insights-send-events.md) all'origine evento
* Visualizzare l'ambiente nel [portale di Time Series Insights](https://insights.timeseries.azure.com)

