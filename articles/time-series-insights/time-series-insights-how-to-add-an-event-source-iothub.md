---
title: Come aggiungere un&quot;origine evento hub IoT a un ambiente Azure Time Series Insights | Documentazione Microsoft
description: Questa esercitazione illustra come aggiungere un&quot;origine evento connessa a un hub IoT all&quot;ambiente Time Series Insights
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 72037677fac528ff8174d25b474ca7e70826a7b0
ms.contentlocale: it-it
ms.lasthandoff: 05/01/2017

---
# <a name="how-to-add-an-iot-hub-event-source"></a>Come aggiungere un'origine evento hub IoT

Questa esercitazione illustra come usare il portale di Azure per aggiungere un'origine evento che legge da un hub IoT all'ambiente Time Series Insights.

## <a name="prerequisites"></a>Prerequisiti

È stato creato un hub IoT e si stanno scrivendo eventi in esso. Per altre informazioni sugli hub IoT, vedere <https://azure.microsoft.com/services/iot-hub/>

> [Gruppi di consumer] Tutte le origini evento Time Series Insights devono avere un proprio gruppo di consumer dedicato che non è condiviso con altri consumer. Se più lettori consumano eventi dello stesso gruppo di consumer, tutti i lettori hanno probabilità di vedere errori. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT](../iot-hub/iot-hub-devguide.md).

## <a name="choose-an-import-option"></a>Scegliere un'opzione di importazione

Le impostazioni per l'origine evento possono essere inserite manualmente oppure è possibile selezionare un hub IoT dagli hub IoT disponibili.
Nel selettore **Opzione di importazione** scegliere una delle opzioni seguenti:

* Specificare le impostazioni dell'hub IoT manualmente
* Usare un hub IoT delle sottoscrizioni disponibili

### <a name="select-an-available-iot-hub"></a>Selezionare un hub IoT disponibile

La tabella seguente riporta le singole opzioni della scheda Nuova origine evento con la relativa descrizione quando si seleziona un hub IoT disponibile come origine evento:

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Nome dell'origine evento | Il nome dell'origine evento. Questo nome deve essere univoco all'interno dell'ambiente Time Series Insights.
| Sorgente | Scegliere **Hub IoT** per creare un'origine evento hub IoT.
| ID sottoscrizione | Selezionare la sottoscrizione in cui l'hub IoT è stato creato.
| Nome dell'hub IoT | Selezionare il nome dell'hub IoT.
| Nome criterio dell'hub IoT | Selezionare il criterio di accesso condiviso che si può trovare nella scheda Impostazioni hub IoT. Ogni criterio di accesso condiviso ha un nome, autorizzazioni impostate e chiavi di accesso. Il criterio di accesso condiviso per l'origine evento *deve* avere le autorizzazioni per la **connessione al servizio**.
| Gruppo di consumer dell'hub IoT | Il gruppo di consumer per leggere gli eventi dall'hub IoT. È consigliabile usare un gruppo di consumer dedicato per l'origine evento.

### <a name="provide-iot-hub-settings-manually"></a>Specificare le impostazioni dell'hub IoT manualmente

La tabella seguente riporta le singole proprietà della scheda Nuova origine evento con la descrizione quando si immettono le impostazioni manualmente:

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Nome dell'origine evento | Il nome dell'origine evento. Questo nome deve essere univoco all'interno dell'ambiente Time Series Insights.
| Sorgente | Scegliere **Hub IoT** per creare un'origine evento hub IoT.
| ID sottoscrizione | La sottoscrizione in cui l'hub IoT è stato creato.
| Gruppo di risorse | La sottoscrizione in cui l'hub IoT è stato creato.
| Nome dell'hub IoT | Il nome dell'hub IoT. Quando l'hub IoT è stato creato gli è stato anche assegnato un nome specifico
| Nome criterio dell'hub IoT | Il criterio di accesso condiviso che può essere creato nella scheda Impostazioni hub IoT. Ogni criterio di accesso condiviso ha un nome, autorizzazioni impostate e chiavi di accesso. Il criterio di accesso condiviso per l'origine evento *deve* avere le autorizzazioni per la **connessione al servizio**.
| Chiave criteri hub IoT | Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi del bus di servizio. Digitare la chiave primaria o secondaria qui.
| Gruppo di consumer dell'hub IoT | Il gruppo di consumer per leggere gli eventi dall'hub IoT. È consigliabile usare un gruppo di consumer dedicato per l'origine evento.

## <a name="next-steps"></a>Passaggi successivi

1. Aggiungere un criterio di accesso ai dati nell'ambiente [Definire i criteri di accesso ai dati](time-series-insights-data-access.md)
1. Accedere all'ambiente nel [portale di Time Series Insights](https://insights.timeseries.azure.com)
