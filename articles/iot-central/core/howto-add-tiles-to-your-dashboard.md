---
title: Aggiungere riquadri al dashboard di Azure IoT Central | Microsoft Docs
description: Come costruttore, informazioni su come configurare il dashboard dell'applicazione IoT Central di Azure predefinito con i riquadri.
author: Haley-Rowland
ms.author: harowl
ms.date: 05/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 892bdcc08bd19b92c8b3d32d2954583f80005e87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84022881"
---
# <a name="configure-the-application-dashboard"></a>Configurare il dashboard dell'applicazione

Il **Dashboard** è la prima pagina visualizzata quando ci si connette a un'applicazione IoT Central. Se si crea l'applicazione da uno dei [modelli di applicazione](./concepts-app-templates.md)incentrata sul settore, l'applicazione avrà un dashboard predefinito per l'avvio. Se si crea l'applicazione da un [modello di applicazione](./concepts-app-templates.md)personalizzato, il dashboard mostra alcuni suggerimenti per iniziare.

> [!TIP]
> Gli utenti possono [creare più dashboard](howto-create-personal-dashboards.md) oltre al dashboard dell'applicazione predefinito. Questi dashboard possono essere personali e riservati solo agli utenti che li hanno creati o condivisi tra tutti gli utenti dell'applicazione.  

## <a name="add-tiles"></a>Aggiunta di riquadri

La schermata seguente mostra il dashboard in un'applicazione creata dal modello di **applicazione personalizzata** . Per personalizzare il dashboard corrente, selezionare **modifica**per aggiungere un dashboard personale o condiviso personalizzato, quindi selezionare **nuovo**:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Dashboard per le applicazioni basate sul modello di applicazione personalizzata":::

Dopo aver selezionato **modifica** o **nuovo**, il dashboard si trova in modalità di *modifica* . È possibile usare gli strumenti nel pannello **modifica dashboard** per aggiungere riquadri al dashboard e personalizzare e rimuovere i riquadri nel dashboard stesso. Ad esempio, per aggiungere un riquadro di **telemetria** per mostrare la temperatura corrente indicata da uno o più dispositivi:

1. Nel pannello **modifica dashboard** selezionare un gruppo di **dispositivi**.
1. Selezionare uno o più dispositivi nell'elenco a discesa **dispositivi** da visualizzare sul riquadro. Verranno ora visualizzati i dati di telemetria, le proprietà e i comandi disponibili dai dispositivi.
1. Selezionare **temperatura** nella sezione telemetria, quindi selezionare **Aggiungi riquadro**. Il riquadro ora viene visualizzato nel dashboard in cui è possibile modificare la visualizzazione, ridimensionare il riquadro e configurarlo:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Aggiungere un riquadro di telemetria della temperatura al dashboard":::

Al termine dell'aggiunta e della personalizzazione dei riquadri nel dashboard, selezionare **Salva**.

## <a name="customize-tiles"></a>Personalizzare i riquadri

Per personalizzare un riquadro nel dashboard, è necessario che il dashboard si trovi in modalità di modifica. Le opzioni di personalizzazione disponibili dipendono dal [tipo di riquadro](#tile-types):

* L'icona del righello in una sezione consente di modificare la visualizzazione. Le visualizzazioni includono grafici a linee, ultimi valori noti e mappe termiche.

* L'icona quadrata consente di ridimensionare il riquadro.

* L'icona a forma di ingranaggio consente di configurare la visualizzazione. Per la visualizzazione di un grafico a linee, ad esempio, è possibile scegliere di visualizzare la legenda e gli assi e scegliere l'intervallo di tempo da tracciare.

## <a name="tile-types"></a>Tipi di riquadro

La tabella seguente descrive i diversi tipi di riquadro che è possibile aggiungere a un dashboard:

| Riquadro             | Descrizione |
| ---------------- | ----------- |
| Markdown         | I riquadri Markdown sono riquadri selezionabili che visualizzano un testo di intestazione e descrizione formattato usando Markdown. L'URL può essere un collegamento relativo a un'altra pagina dell'applicazione o un collegamento assoluto a un sito esterno.|
| Image            | I riquadri immagine visualizzano un'immagine personalizzata e possono essere selezionabili. L'URL può essere un collegamento relativo a un'altra pagina dell'applicazione o un collegamento assoluto a un sito esterno.|
| Label            | I riquadri etichetta visualizzano testo personalizzato in un dashboard. È possibile scegliere la dimensione del testo. Usare un riquadro etichetta per aggiungere informazioni pertinenti al dashboard, ad esempio descrizioni, dettagli di contatto o informazioni della Guida.|
| Conteggio            | I riquadri conteggio visualizzano il numero di dispositivi in un gruppo di dispositivi.|
| Mappa              | I riquadri mappa visualizzano la posizione di uno o più dispositivi su una mappa. È anche possibile visualizzare fino a 100 punti della cronologia della posizione di un dispositivo. Ad esempio, è possibile visualizzare la route campionata in cui si è verificato un dispositivo nella settimana precedente.|
| KPI              |  I riquadri KPI visualizzano i valori di telemetria aggregati per uno o più dispositivi in un periodo di tempo. Ad esempio, è possibile usarlo per mostrare la temperatura massima e la pressione raggiunta per uno o più dispositivi nell'ultima ora.|
| Grafico a linee       | I riquadri del grafico a linee tracciano uno o più valori di telemetria aggregati per uno o più dispositivi per un periodo di tempo. Ad esempio, è possibile visualizzare un grafico a linee per tracciare la temperatura e la pressione media di uno o più dispositivi nell'ultima ora.|
| Grafico a barre        | I riquadri del grafico a barre tracciano uno o più valori di telemetria aggregati per uno o più dispositivi per un periodo di tempo. Ad esempio, è possibile visualizzare un grafico a barre per mostrare la temperatura e la pressione media di uno o più dispositivi nell'ultima ora.|
| Grafico a torta        | I riquadri del grafico a torta visualizzano uno o più valori di telemetria aggregati per uno o più dispositivi per un periodo di tempo.|
| Mappa termica         | I riquadri mappa termica visualizzano informazioni su uno o più dispositivi, rappresentati come colori.|
| Ultimo valore noto | Ultimi riquadri valore noti visualizzano i valori di telemetria più recenti per uno o più dispositivi. Ad esempio, è possibile usare questo riquadro per visualizzare i valori di temperatura, pressione e umidità più recenti per uno o più dispositivi. |
| Cronologia eventi    | I riquadri cronologia eventi visualizzano gli eventi per un dispositivo per un periodo di tempo specifico. Ad esempio, è possibile usarlo per visualizzare tutti gli eventi di apertura e chiusura di una valvola per uno o più dispositivi nell'ultima ora.|
| Proprietà         |  I riquadri delle proprietà visualizzano il valore corrente per le proprietà e le proprietà del cloud di uno o più dispositivi. Ad esempio, è possibile usare questo riquadro per visualizzare le proprietà del dispositivo, ad esempio la versione del produttore o del firmware per un dispositivo. |

Attualmente, è possibile aggiungere fino a 10 dispositivi ai riquadri che supportano più dispositivi.

### <a name="customizing-visualizations"></a>Personalizzazione delle visualizzazioni

Per i riquadri che visualizzano i valori di aggregazione, selezionare l'icona dell'ingranaggio accanto al tipo di telemetria nel pannello **Configura grafico** per scegliere l'aggregazione. È possibile scegliere tra media, somma, massimo, minimo e conteggio.

Per i grafici a linee, i grafici a barre e i grafici a torta, è possibile personalizzare il colore dei diversi valori di telemetria. Selezionare l'icona della tavolozza accanto ai dati di telemetria che si desidera personalizzare:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Modificare il colore di un valore di telemetria":::

Per i riquadri che mostrano le proprietà di stringa o i valori di telemetria, è possibile scegliere come visualizzare il testo. Se, ad esempio, il dispositivo archivia un URL in una proprietà di stringa, è possibile visualizzarlo come collegamento selezionabile. Se l'URL fa riferimento a un'immagine, è possibile eseguire il rendering dell'immagine in un riquadro ultimo valore noto o proprietà. Per modificare la modalità di visualizzazione di una stringa, selezionare l'icona a forma di ingranaggio accanto alla proprietà o al tipo di telemetria:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Chana la visualizzazione di una stringa in un riquadro":::

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare il dashboard dell'applicazione predefinito di Azure IoT Central, è possibile [acquisire informazioni su come creare un dashboard personale](howto-create-personal-dashboards.md).
