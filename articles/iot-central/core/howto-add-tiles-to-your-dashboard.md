---
title: Aggiungere riquadri al dashboard | Microsoft Docs
description: Informazioni per il responsabile della compilazione su come configurare il dashboard predefinito dell'applicazione Azure IoT Central.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1110f76a792a7e3955d5fd32e01ac1566d467151
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659046"
---
# <a name="configure-the-application-dashboard"></a>Configurare il dashboard dell'applicazione

Il **dashboard** è la pagina che viene caricata quando gli utenti che hanno accesso all'applicazione passano all'URL di questa. Se l'applicazione è stata creata usando uno dei **modelli di applicazione** , all'avvio visualizza un dashboard predefinito. Se l'applicazione è stata creata da un modello **Applicazione personalizzata**, il dashboard visualizza alcuni suggerimenti per iniziare.

> [!NOTE]
> Gli utenti possono [creare più dashboard](howto-create-personal-dashboards.md) oltre al dashboard dell'applicazione predefinito. Questi dashboard possono essere personali e riservati solo agli utenti che li hanno creati o condivisi tra tutti gli utenti dell'applicazione.  

## <a name="add-tiles"></a>Aggiunta di riquadri

Lo screenshot seguente visualizza il dashboard di un'applicazione creata dal modello **Applicazione personalizzata**. Per personalizzare il dashboard predefinito per l'applicazione, selezionare **Modifica** nella parte superiore sinistra della pagina.

> [!div class="mx-imgBorder"]
> ![Dashboard per le applicazioni basate sul modello "Sample Contoso"](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Quando si seleziona **Modifica** viene aperto il pannello della libreria dashboard. La libreria contiene i riquadri e le primitive del dashboard che è possibile usare per personalizzare il dashboard stesso.

> [!div class="mx-imgBorder"]
> ![Libreria dashboard](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

È ad esempio possibile aggiungere un riquadro di **telemetriaper** la temperatura corrente del dispositivo. A tale scopo, procedere come indicato di seguito:

1. Selezionare un **modello di dispositivo**
1. Selezionare un dispositivo da **Dispositivi** corrispondente al dispositivo che si vuole visualizzare in un riquadro del dashboard. Verrà quindi visualizzato l'elenco delle proprietà del dispositivo utilizzabili nel riquadro.
1. Per creare il riquadro nel dashboard, fare clic su **Temperatura** e trascinare il riquadro nell'area del dashboard. È anche possibile fare clic sulla casella di controllo accanto a **Temperatura** e fare clic su **Aggiungi riquadro**. Lo screenshot seguente illustra la selezione di un modello di dispositivo e di un'istanza del dispositivo e la creazione di un riquadro di telemetria della temperatura nel dashboard.
1. Selezionare **Salva** in alto a sinistra per salvare il riquadro nel dashboard.

> [!div class="mx-imgBorder"]
> ![Modulo di configurazione dei dettagli del dispositivo con le impostazioni e le proprietà](media/howto-add-tiles-to-your-dashboard/device-details.png)

A questo punto, quando un operatore visualizza il dashboard dell'applicazione predefinito, vede il nuovo riquadro con la **temperatura** del dispositivo. Ogni riquadro ha un grafo, un grafico e così via preselezionato che viene visualizzato quando il riquadro viene creato. Gli utenti, tuttavia, possono scegliere di modificare e cambiare questa visualizzazione.  

> [!div class="mx-imgBorder"]
> ![Scheda "Dashboard" in cui sono visualizzate le impostazioni e le proprietà per il riquadro](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Modificare i riquadri

Per modificare un riquadro nel dashboard, fare prima clic su **Modifica** nella parte superiore sinistra della pagina, che consente di aprire la modalità di modifica per il dashboard e tutti i riquadri al suo interno.  

> [!div class="mx-imgBorder"]
> ![Schermata del dashboard con modalità di modifica attivata per un riquadro selezionato](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Fare quindi clic sull'icona a forma di **ingranaggio** nell'angolo superiore destro del riquadro che si vuole modificare. Qui è possibile modificare aspetti del riquadro quali il titolo, la visualizzazione, l'aggregazione e così via.

> [!div class="mx-imgBorder"]
> ![Elenco a discesa per le impostazioni di aggregazione del riquadro](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

È anche possibile modificare la visualizzazione del grafico facendo clic sull'icona a forma di **Righello** sul riquadro.

> [!div class="mx-imgBorder"]
> ![Elenco a discesa per le impostazioni di visualizzazione del riquadro](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Tipi di riquadro

La tabella seguente riepiloga l'utilizzo dei riquadri in Azure IoT Central:

| Riquadro | Dashboard | Descrizione
| ----------- | ------- | ------- |
| Contenuto | Dashboard di applicazioni e set di dispositivi |I riquadri supportati da Markdown sono riquadri selezionabili che visualizzano testo dell'intestazione e della descrizione. È anche possibile usare un riquadro di questo tipo come riquadro di collegamento per consentire a un utente di passare a un URL correlato all'applicazione.|
| Immagine | Dashboard di applicazioni e set di dispositivi |I riquadri immagine visualizzano un'immagine personalizzata e possono essere selezionabili. Usare un riquadro immagine per aggiungere grafica a un dashboard e, facoltativamente, consentire a un utente di passare a un URL pertinente per l'applicazione.|
| Etichetta | Dashboard di applicazioni |I riquadri etichetta visualizzano testo personalizzato in un dashboard. È possibile scegliere la dimensione del testo. Usare un riquadro etichetta per aggiungere informazioni pertinenti al dashboard, ad esempio descrizioni, dettagli di contatto o informazioni della Guida.|
| Mappa | Dashboard di applicazioni e dispositivi |I riquadri mappa visualizzano la posizione di un dispositivo in una mappa. È anche possibile visualizzare fino a 100 punti della cronologia della posizione di un dispositivo. È ad esempio possibile visualizzare la route campionata delle posizioni di un dispositivo nella settimana precedente.|
| Grafico a linee | Dashboard di applicazioni e dispositivi |I riquadri grafico a linee visualizzano un grafico di misure aggregate per un dispositivo per un periodo di tempo specifico. È ad esempio possibile visualizzare un grafico a linee che visualizza la temperatura e la pressione medie di un dispositivo nell'ultima ora.|
| Grafico a barre | Dashboard di applicazioni e dispositivi |I riquadri grafico a barre visualizzano un grafico di misure aggregate per un dispositivo per un periodo di tempo specifico. È ad esempio possibile visualizzare un grafico a barre che visualizza la temperatura e la pressione medie di un dispositivo nell'ultima ora.|
| Grafico a torta | Dashboard di applicazioni e set di dispositivi |I riquadri grafico a torta visualizzano un grafico di misure aggregate per un dispositivo per un periodo di tempo specifico.|
| Mappa termica | Dashboard di applicazioni e set di dispositivi |I riquadri mappa termica visualizzano informazioni sul dispositivo rappresentandole sotto forma di colori.|
| Cronologia eventi | Dashboard di applicazioni e dispositivi |I riquadri cronologia eventi visualizzano gli eventi per un dispositivo per un periodo di tempo specifico. È ad esempio possibile usare un riquadro di questo tipo per visualizzare tutte le variazioni di temperatura per un dispositivo nell'ultima ora.|
| Cronologia dello stato | Dashboard di applicazioni e dispositivi |I riquadri cronologia dello stato visualizzano i valori delle misurazioni e per un periodo di tempo specifico. È ad esempio possibile usare un riquadro di questo tipo per visualizzare i valori della temperatura per un dispositivo nell'ultima ora.|
| KPI | Dashboard di applicazioni e dispositivi | I riquadri KPI visualizzano telemetria aggregata o misurazioni di un evento per un periodo di tempo specifico. È ad esempio possibile usare un riquadro di questo tipo per visualizzare la temperatura massima raggiunta da un dispositivo nell'ultima ora.|
| Ultimo valore noto | Dashboard di applicazioni e dispositivi |I riquadri ultimo valore noto visualizza il valore più recente per una misura di telemetria o di stato. È ad esempio possibile usare un riquadro di questo tipo per visualizzare le misurazioni più recenti di temperatura, pressione e umidità per un dispositivo. |
| Proprietà | Dashboard di applicazioni e dispositivi | I riquadri proprietà visualizzano il valore corrente per le proprietà e le proprietà cloud di un dispositivo. È ad esempio possibile usare un riquadro di questo tipo per visualizzare proprietà del dispositivo quali produttore o versione del firmware. |

### <a name="customizing-visualizations"></a>Personalizzazione delle visualizzazioni

Per i grafici a linee, a barre e a torta è possibile personalizzare i colori visualizzati nel grafico per telemetrie diverse. A tale scopo, selezionare l'icona a forma di tavolozza accanto ai dati di telemetria che si vogliono personalizzare e scegliere un colore.

> [!div class="mx-imgBorder"]
> ![Elenco a discesa per le impostazioni di visualizzazione del colore dei dati di telemetria](media/howto-add-tiles-to-your-dashboard/color-customization.png)

Per i valori di telemetria o le proprietà di tipo stringa, è possibile scegliere come visualizzare il testo. Se, ad esempio, il dispositivo invia un URL come telemetria stringa, è possibile visualizzare tale URL come collegamento selezionabile. Se l'URL fa riferimento a un'immagine, è possibile eseguire il rendering dell'immagine in un riquadro ultimo valore noto o proprietà. È possibile modificare la modalità di visualizzazione dei dati di telemetria di tipo stringa selezionando l'icona a forma di ingranaggio accanto al nome della telemetria. Questo consente di visualizzare il testo come testo, collegamento o immagine.

> [!div class="mx-imgBorder"]
> ![Elenco a discesa per le impostazioni di visualizzazione delle stringhe](media/howto-add-tiles-to-your-dashboard/string-viz-customization.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare il dashboard dell'applicazione predefinito di Azure IoT Central, è possibile [acquisire informazioni su come creare un dashboard personale](howto-create-personal-dashboards.md).
