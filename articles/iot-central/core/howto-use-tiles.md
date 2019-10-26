---
title: Come usare i riquadri nel dashboard dell'applicazione IoT Central di Azure | Microsoft Docs
description: Come costruttore, informazioni su come usare i riquadri nei dashboard dell'applicazione, i dashboard del set di dispositivi e i dashboard del dispositivo.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0803258c362279049a49a7eee00e7a4763621672
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952628"
---
# <a name="how-to-use-tiles"></a>Come usare i riquadri
È possibile usare i riquadri per personalizzare i dashboard dell'applicazione, i dashboard del dispositivo e i dashboard dei set di dispositivi. È possibile aggiungere più riquadri a un dashboard e personalizzare i riquadri per visualizzare le informazioni rilevanti per l'applicazione. È anche possibile ridimensionare i riquadri e personalizzare il layout in qualsiasi dashboard. Lo screenshot seguente mostra il dashboard dell'applicazione con riquadri diversi.

![Dashboard dell'applicazione](media/howto-use-tiles/image1a.png)


La tabella seguente riepiloga l'uso dei riquadri in Azure IoT Central:

 
| Riquadro | dashboard | Description
| ----------- | ------- | ------- |
| Collegamento | Dashboard dell'applicazione e del set di dispositivi |I riquadri collegamento sono riquadri selezionabili che visualizzano l'intestazione e il testo della descrizione. Usare un riquadro di collegamento per consentire a un utente di passare a un URL correlato all'applicazione. |
| Image | Dashboard dell'applicazione e del set di dispositivi |I riquadri immagine visualizzano un'immagine personalizzata e possono essere selezionabili. Usare un riquadro immagine per aggiungere grafica a un dashboard e, facoltativamente, consentire a un utente di passare a un URL pertinente per l'applicazione.|
| Etichette | Dashboard dell'applicazione |I riquadri delle etichette visualizzano testo personalizzato in un dashboard. È possibile scegliere la dimensione del testo. Usare un riquadro etichetta per aggiungere informazioni rilevanti al dashboard, quali descrizioni, dettagli del contatto o Guida|
| Mappa | Dashboard dell'applicazione e del set di dispositivi |I riquadri mappa visualizzano la posizione e lo stato di un dispositivo su una mappa. Ad esempio, è possibile visualizzare la posizione in cui si trova un dispositivo e se la ventola è attivata.|
| Grafico a linee | Dashboard dell'applicazione e del dispositivo |I riquadri del grafico a linee visualizzano un grafico di misura aggregata per un dispositivo per un periodo di tempo. Ad esempio, è possibile visualizzare un grafico a linee che mostra la temperatura e la pressione media di un dispositivo nell'ultima ora|
| Grafico a barre | Dashboard dell'applicazione e del dispositivo |Nei riquadri del grafico a barre viene visualizzato un grafico di misurazioni aggregate per un dispositivo per un periodo di tempo. Ad esempio, è possibile visualizzare un grafico a barre che mostra la temperatura e la pressione medie di un dispositivo per l'ultima ora |
| Cronologia eventi | Dashboard dell'applicazione e del dispositivo |Riquadro cronologia eventi Visualizza gli eventi per un dispositivo in un periodo di tempo. Ad esempio, è possibile usarlo per mostrare tutte le variazioni di temperatura per un dispositivo nell'ultima ora. |
| Cronologia dello stato | Dashboard dell'applicazione e del dispositivo |Riquadro cronologia stato consente di visualizzare i valori di misurazione per un periodo di tempo. Ad esempio, è possibile usarlo per mostrare i valori di temperatura per un dispositivo nell'ultima ora.|
| KPI | Dashboard dell'applicazione e del dispositivo | Riquadro KPI visualizza una telemetria aggregata o la misurazione di un evento per un periodo di tempo. Ad esempio, è possibile usarlo per mostrare la temperatura massima raggiunta per un dispositivo nell'ultima ora.|
| Ultimo valore noto | Dashboard dell'applicazione e del dispositivo |Il riquadro ultimo valore noto Visualizza il valore più recente di una telemetria o di una misurazione dello stato. Ad esempio, è possibile usare questo riquadro per visualizzare le misurazioni più recenti di temperatura, pressione e umidità per un dispositivo.|
| Griglia del set di dispositivi | Dashboard dell'applicazione e del set di dispositivi | Griglia dei set di dispositivi Visualizza le informazioni sul set di dispositivi. Usare un riquadro della griglia del set di dispositivi per visualizzare informazioni quali nome, posizione e modello di tutti i dispositivi nel set di dispositivi.|


Per altre informazioni su come configurare il dashboard nell'applicazione IoT Central di Azure, vedere [aggiungere riquadri al dashboard](./howto-add-tiles-to-your-dashboard.md).
