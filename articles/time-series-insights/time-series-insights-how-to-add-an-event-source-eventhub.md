---
title: Come aggiungere un'origine evento dell'hub eventi all'ambiente Time Series Insights di Azure | Microsoft Docs
description: Questa esercitazione illustra come aggiungere un'origine evento connessa a un hub eventi all'ambiente Time Series Insights.
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.openlocfilehash: f6a993b3858cfb94dd9795f5e55f15fa6ec7dcb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-an-event-hub-event-source"></a>Come aggiungere un'origine evento di un hub eventi

Questa esercitazione illustra come usare il portale di Azure per aggiungere all'ambiente Time Series Insights un'origine evento che legge i dati da un hub eventi.

## <a name="prerequisites"></a>Prerequisiti

È stato creato un hub eventi e si stanno scrivendo eventi al suo interno. Per altre informazioni sugli hub eventi, vedere <https://azure.microsoft.com/services/event-hubs/>

> [Gruppi di consumer] Tutte le origini evento Time Series Insights devono avere un proprio gruppo di consumer dedicato che non è condiviso con altri consumer. Se più lettori consumano eventi dello stesso gruppo di consumer, è probabile che tutti i lettori riscontrino errori. Si noti che è presente anche un limite di 20 gruppi di utenti per Hub eventi. Per informazioni dettagliate, vedere la [Guida alla programmazione di Hub eventi](../event-hubs/event-hubs-programming-guide.md).

## <a name="choose-an-import-option"></a>Scegliere un'opzione di importazione

Le impostazioni per l'origine evento possono essere inserite manualmente oppure è possibile selezionare un hub eventi dagli hub eventi disponibili.
Nel selettore **Opzione di importazione** scegliere una delle opzioni seguenti:

* Specificare le impostazioni dell'hub eventi manualmente
* Usare un hub eventi dalle sottoscrizioni disponibili

### <a name="select-an-available-event-hub"></a>Selezionare un hub eventi disponibile

La tabella seguente riporta le singole opzioni della scheda Nuova origine evento con la relativa descrizione quando si seleziona un hub eventi disponibile come origine evento:

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Nome origine evento | Il nome dell'origine evento. Questo nome deve essere univoco all'interno dell'ambiente Time Series Insights.
| Sorgente | Scegliere **Hub eventi** per creare un'origine evento di hub eventi.
| ID sottoscrizione | Selezionare la sottoscrizione in cui l'hub eventi è stato creato.
| Spazio dei nomi del bus di servizio | Selezionare lo spazio dei nomi del bus di servizio che contiene l'hub eventi.
| Nome hub eventi | Selezionare il nome dell'hub eventi.
| Nome criteri hub eventi | Selezionare i criteri di accesso condiviso, che possono essere creati nella scheda Configura dell'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. I criteri di accesso condiviso per l'origine evento *devono* disporre di autorizzazioni di **lettura**.
| Gruppo di consumer dell'hub eventi | Il gruppo di consumer per leggere gli eventi dall'hub eventi. È consigliabile usare un gruppo di consumer dedicato per l'origine evento.

### <a name="provide-event-hub-settings-manually"></a>Specificare le impostazioni dell'hub eventi manualmente

La tabella seguente riporta le singole proprietà della scheda Nuova origine evento con la relativa descrizione quando si immettono le impostazioni manualmente:

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Nome origine evento | Il nome dell'origine evento. Questo nome deve essere univoco all'interno dell'ambiente Time Series Insights.
| Sorgente | Scegliere **Hub eventi** per creare un'origine evento di hub eventi.
| ID sottoscrizione | La sottoscrizione in cui l'hub eventi è stato creato.
| Gruppo di risorse | La sottoscrizione in cui l'hub eventi è stato creato.
| Spazio dei nomi del bus di servizio | Uno spazio dei nomi Service Bus è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo Hub eventi, viene inoltre creato uno spazio dei nomi Service Bus.
| Nome hub eventi | Nome dell'hub eventi. Quando è stato creato l'hub eventi gli è stato assegnato anche un nome specifico
| Nome criteri hub eventi | Criteri di accesso condiviso che possono essere creati nella scheda Configura dell'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. I criteri di accesso condiviso per l'origine evento *devono* disporre di autorizzazioni di **lettura**.
| Chiave criteri hub eventi | Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi del bus di servizio. Digitare la chiave primaria o secondaria qui.
| Gruppo di consumer dell'hub eventi | Il gruppo di consumer per leggere gli eventi dall'hub eventi. È consigliabile usare un gruppo di consumer dedicato per l'origine evento.

## <a name="next-steps"></a>Passaggi successivi

1. Aggiungere i criteri di accesso ai dati nell'ambiente [Definire i criteri di accesso ai dati](time-series-insights-data-access.md)
1. Accedere all'ambiente nel [portale di Time Series Insights](https://insights.timeseries.azure.com)