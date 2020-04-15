---
title: Aggiunta di riquadri al dashboard Documenti Microsoft
description: Come generatore, scopri come configurare il dashboard dell'applicazione Azure IoT Central predefinito.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f75e5a28f7ec56750432e74ee48ba68491a5e481
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310295"
---
# <a name="configure-the-application-dashboard"></a>Configurare il dashboard dell'applicazione

Il **dashboard** è la pagina che viene caricata quando gli utenti che hanno accesso all'applicazione passano all'URL dell'applicazione. Se l'applicazione è stata creata da uno dei modelli di **applicazione**, l'applicazione disterrà un dashboard predefinito da avviare. Se l'applicazione è stata creata dal modello di **applicazione Legacy,** il dashboard sarà vuoto per l'avvio.

> [!NOTE]
> Gli utenti possono [creare più dashboard](howto-create-personal-dashboards.md) oltre al dashboard dell'applicazione predefinito. Questi dashboard possono essere personali solo per l'utente o condivisi tra tutti gli utenti dell'applicazione. 

## <a name="add-tiles"></a>Aggiunta di riquadri

Nella schermata seguente viene illustrato il dashboard in un'applicazione creata dal modello **Applicazione personalizzata.** Per personalizzare il dashboard predefinito per l'applicazione, selezionare **Modifica** nella parte superiore sinistra della pagina.

> [!div class="mx-imgBorder"]
> ![Dashboard per le applicazioni basate sul modello "Sample Contoso"](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Selezionando **Modifica** si apre il pannello della libreria del quadro di lavoro. La raccolta contiene i riquadri e le primitive del dashboard che è possibile usare per personalizzare il dashboard.

> [!div class="mx-imgBorder"]
> ![Libreria dashboard](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Ad esempio, è possibile aggiungere un riquadro **Telemetria** per la temperatura corrente del dispositivo. A tale scopo, procedere come indicato di seguito:
1. Selezionare un **modello di dispositivo**
1. Selezionare **un'istanza del dispositivo** da visualizzare in un riquadro del dashboard. Verrà quindi visualizzato un elenco delle proprietà del dispositivo che possono essere utilizzate nel riquadro.
1. Per creare il riquadro nel dashboard, fare clic su **Temperatura** e trascinarlo nell'area del dashboard. È anche possibile fare clic sulla casella di controllo accanto a **Temperatura** e fare clic su **Combina**. La schermata seguente mostra la selezione di un modello di dispositivo e un'istanza del dispositivo e la creazione di un riquadro Di telemetria della temperatura nel dashboard.
1. Selezionare **Salva** in alto a sinistra per salvare il riquadro nel dashboard.

> [!div class="mx-imgBorder"]
> ![Modulo "Configure Device Details" (Configura dettagli dispositivo) con i dettagli per impostazioni e proprietà](media/howto-add-tiles-to-your-dashboard/device-details.png)

Ora, quando un operatore visualizza il dashboard dell'applicazione predefinito, vede il nuovo riquadro con la **temperatura** per il dispositivo. Ogni riquadro ha un grafico preselezionato, un grafico e così via che verranno visualizzati quando viene creato il riquadro. Tuttavia, gli utenti possono scegliere di modificare e modificare questa visualizzazione. 

> [!div class="mx-imgBorder"]
> ![Scheda "Dashboard" con visualizzate le impostazioni e le proprietà per il riquadro](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Modificare i riquadri

Per modificare un riquadro nel dashboard, fare clic su **Modifica** nella parte superiore sinistra della pagina, per aprire la modalità di modifica per il dashboard e tutti i relativi riquadri. 

> [!div class="mx-imgBorder"]
> ![Schermata del dashboard con la modalità di modifica attivata per un riquadro selezionato](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Quindi fai clic sull'icona a forma di **ingranaggio** nell'angolo in alto a destra del riquadro che desideri modificare. Qui è possibile modificare gli aspetti del riquadro, tra cui il titolo, la visualizzazione, l'aggregazione e così via.

> [!div class="mx-imgBorder"]
> ![Elenco a discesa per le impostazioni di aggregazione dei riquadri](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

È inoltre possibile modificare la visualizzazione del grafico facendo clic sull'icona **Righello** nel riquadro.

> [!div class="mx-imgBorder"]
> ![Elenco a discesa per le impostazioni di visualizzazione dei riquadri](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Tipi di riquadri

The following table summarizes the usage of tiles in Azure IoT Central:
 
| Riquadro | Dashboard | Descrizione
| ----------- | ------- | ------- |
| Contenuto | Dashboard del set di applicazioni e dispositivi |I riquadri supportati per il contrassegno sono riquadri selezionabili che visualizzano testo di intestazione e descrizione. È anche possibile usare questo riquadro come riquadro di collegamento per consentire a un utente di passare a un URL correlato all'applicazione.|
| Immagine | Dashboard del set di applicazioni e dispositivi |I riquadri immagine visualizzano un'immagine personalizzata e possono essere selezionabili. Usare un riquadro immagine per aggiungere elementi grafici a un dashboard e, facoltativamente, consentire a un utente di passare a un URL pertinente all'applicazione.|
| Etichetta | Dashboard dell'applicazione |I riquadri etichetta visualizzano testo personalizzato in un dashboard. È possibile scegliere la dimensione del testo. Usare un riquadro di etichette per aggiungere informazioni pertinenti al dashboard, ad esempio descrizioni, dettagli di contatto o Guida.|
| Mappa | Dashboard di applicazioni e dispositivi |I riquadri della mappa visualizzano la posizione di un dispositivo su una mappa. Puoi anche visualizzare fino a 100 punti della cronologia delle posizioni di un dispositivo. Ad esempio, è possibile visualizzare un percorso campionato in cui un dispositivo è stato nella settimana precedente.|
| Grafico a linee | Dashboard di applicazioni e dispositivi |I riquadri del grafico a linee visualizzano un grafico di misurazione aggregata per un dispositivo per un periodo di tempo. Ad esempio, è possibile visualizzare un grafico a linee che mostra la temperatura media e la pressione di un dispositivo per l'ultima ora.|
| Grafico a barre | Dashboard di applicazioni e dispositivi |I riquadri dei grafici a barre visualizzano un grafico di misure aggregate per un dispositivo per un periodo di tempo. Ad esempio, è possibile visualizzare un grafico a barre che mostra la temperatura media e la pressione di un dispositivo per l'ultima ora.|
| Grafico a torta | Dashboard del set di applicazioni e dispositivi |I riquadri del grafico a torta visualizzano un grafico delle misure di aggregazione per un dispositivo per un periodo di tempo.|
| Mappa termica | Dashboard del set di applicazioni e dispositivi |I riquadri Mappa di calore visualizzano informazioni sul set di dispositivi, rappresentati come colori.|
| Cronologia degli eventi | Dashboard di applicazioni e dispositivi |I riquadri Cronologia eventi visualizzano gli eventi per un dispositivo in un periodo di tempo. Ad esempio, è possibile utilizzarlo per mostrare tutte le variazioni di temperatura per un dispositivo durante l'ultima ora.|
| Cronologia dello stato | Dashboard di applicazioni e dispositivi |I riquadri della cronologia dello stato visualizzano i valori di misurazione per un periodo di tempo. Ad esempio, è possibile utilizzarlo per visualizzare i valori di temperatura per un dispositivo durante l'ultima ora.|
| KPI | Dashboard di applicazioni e dispositivi | I riquadri KPI visualizzano un'aggregazione di dati di telemetria o di misurazione degli eventi per un periodo di tempo. Ad esempio, è possibile utilizzarlo per visualizzare la temperatura massima raggiunta per un dispositivo durante l'ultima ora.|
| Ultimo valore noto | Dashboard di applicazioni e dispositivi |I riquadri con valori noti visualizzano il valore più recente per una misurazione di telemetria o di stato. Ad esempio, è possibile utilizzare questo riquadro per visualizzare le misurazioni più recenti di temperatura, pressione e umidità per un dispositivo.|

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come configurare il dashboard dell'applicazione predefinita di Azure IoT Central, è possibile [imparare a creare un dashboard personale.](howto-create-personal-dashboards.md)
