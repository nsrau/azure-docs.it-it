---
title: Come usare i riquadri nel dashboard dell'applicazione Azure IoT Central | Microsoft Docs
description: Un generatore di informazioni su come usare i riquadri nel dashboard applicazioni, dispositivi set di dashboard e i dashboard dispositivo.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 26322461e4f53bffff2fb182df6c45ed3b83c4bf
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503526"
---
# <a name="how-to-use-tiles"></a>Come usare i riquadri
È possibile usare i riquadri per personalizzare i dashboard dell'applicazione, i dashboard dispositivo, e i dashboard di set di dispositivi. È possibile aggiungere più riquadri a un dashboard e personalizzare i riquadri per visualizzare le informazioni rilevanti per l'applicazione. È anche possibile ridimensionare i riquadri e personalizzare il layout su qualsiasi dashboard. La schermata seguente mostra il dashboard dell'applicazione con i riquadri diversi.

![Dashboard dell'applicazione](media/howto-use-tiles/image1a.png)


La tabella seguente riepiloga l'utilizzo dei riquadri in Azure IoT Central:

 
| Riquadro | dashboard | Descrizione
| ----------- | ------- | ------- |
| Collegamento | Applicazione e i dashboard di set di dispositivi |I riquadri di collegamento sono selezionabili riquadri che consentono di visualizzano il testo di intestazione e descrizione. Utilizzare un riquadro del collegamento per consentire agli utenti di accedere a un URL che è correlato all'applicazione. |
| Image | Applicazione e i dashboard di set di dispositivi |I riquadri immagine Visualizza un'immagine personalizzata e possono essere selezionabili. Usare il riquadro di un'immagine per aggiungere elementi grafici a un dashboard e, facoltativamente, consentire agli utenti di accedere a un URL che è rilevante per l'applicazione.|
| Etichetta | Dashboard applicazioni |I riquadri di etichetta visualizzare il testo personalizzato in un dashboard. È possibile scegliere le dimensioni del testo. Usare un riquadro etichetta per aggiungere le informazioni pertinenti al dashboard tali descrizioni, i dettagli di contatto o della Guida|
| Mappa | Applicazione e i dashboard di set di dispositivi |Tessere mappa di visualizzare il percorso e lo stato di un dispositivo su una mappa. Ad esempio, è possibile visualizzare dove è un dispositivo e indica se la ventola è attivata.|
| Grafico a linee | Dashboard dell'applicazione e dispositivo |I riquadri grafico riga visualizza un grafico della misura di aggregazione per un dispositivo per un periodo di tempo. Ad esempio, è possibile visualizzare un grafico a linee che mostra la temperatura media e la pressione in un dispositivo per l'ultima ora|
| Grafico a barre | Dashboard dell'applicazione e dispositivo |I riquadri di grafico a barre visualizzano un grafico delle misurazioni aggregate per un dispositivo per un periodo di tempo. Ad esempio, è possibile visualizzare un grafico a barre che mostra la temperatura media e la pressione in un dispositivo per l'ultima ora |
| Cronologia degli eventi | Dashboard dell'applicazione e dispositivo |Riquadro cronologia eventi visualizza gli eventi per un dispositivo in un periodo di tempo. Ad esempio, è possibile usarlo per mostrare che tutti i cambiamenti di temperatura di un dispositivo durante l'ultima ora. |
| Cronologia dello stato | Dashboard dell'applicazione e dispositivo |Cronologia dello stato riquadro Visualizza i valori di misura per un periodo di tempo. Ad esempio, è possibile usarlo per mostrare i valori di temperatura di un dispositivo durante l'ultima ora.|
| KPI | Dashboard dell'applicazione e dispositivo | Riquadro dell'indicatore KPI consente di visualizzare una misurazione aggregazione di eventi o dati di telemetria per un periodo di tempo. Ad esempio, è possibile usarlo per visualizzare la temperatura massima raggiunta per un dispositivo durante l'ultima ora.|
| Ultimo valore noto | Dashboard dell'applicazione e dispositivo |Ultimo valore noto riquadro Visualizza il valore più recente per una misurazione di stato o dati di telemetria. Ad esempio, è possibile utilizzare questo riquadro per visualizzare le misure più recenti di temperatura e pressione umidità per un dispositivo.|
| Griglia Set di dispositivi | Applicazione e i dashboard di set di dispositivi | Dispositivo impostato griglia visualizza le informazioni sul set di dispositivi. Utilizzare un riquadro griglia dispositivo impostato per mostrare informazioni quali nome, posizione e modello di tutti i dispositivi nel set di dispositivi.|


Per altre informazioni su come configurare i dashboard nell'applicazione Azure IoT Central, vedere [dashboard dell'applicazione configura](howto-configure-homepage.md).
