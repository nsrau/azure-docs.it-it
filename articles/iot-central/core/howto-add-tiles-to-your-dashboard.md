---
title: Configurare il dashboard di Azure IoT Central | Microsoft Docs
description: Come costruttore, informazioni su come configurare il dashboard dell'applicazione IoT Central di Azure predefinito con i riquadri.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 2ea2dd18cd5816c7c1406ac201421a2983e431a3
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491999"
---
# <a name="configure-the-application-dashboard"></a>Configurare il dashboard dell'applicazione

Il **Dashboard** è la prima pagina visualizzata quando ci si connette a un'applicazione IoT Central. Se si crea l'applicazione da uno dei [modelli di applicazione](./concepts-app-templates.md)incentrata sul settore, l'applicazione avrà un dashboard predefinito per l'avvio. Se si crea l'applicazione da un [modello di applicazione](./concepts-app-templates.md)personalizzato, il dashboard mostra alcuni suggerimenti per iniziare.

> [!TIP]
> Gli utenti possono [creare più dashboard](howto-create-personal-dashboards.md) oltre al dashboard dell'applicazione predefinito. Questi dashboard possono essere personali e riservati solo agli utenti che li hanno creati o condivisi tra tutti gli utenti dell'applicazione.  

## <a name="add-tiles"></a>Aggiunta di riquadri

La schermata seguente mostra il dashboard in un'applicazione creata dal modello di **applicazione personalizzata** . Per personalizzare il dashboard corrente, selezionare **modifica** per aggiungere un dashboard personale o condiviso personalizzato, quindi selezionare **nuovo** :

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Dashboard per le applicazioni basate sul modello di applicazione personalizzata":::

Dopo aver selezionato **modifica** o **nuovo** , il dashboard si trova in modalità di *modifica* . È possibile usare gli strumenti nel pannello **modifica dashboard** per aggiungere riquadri al dashboard e personalizzare e rimuovere i riquadri nel dashboard stesso. Ad esempio, per aggiungere un riquadro di **telemetria** per mostrare la temperatura corrente indicata da uno o più dispositivi:

1. Selezionare un **gruppo** di dispositivi e quindi scegliere i dispositivi nell'elenco a discesa **dispositivi** da visualizzare sul riquadro. Verranno ora visualizzati i dati di telemetria, le proprietà e i comandi disponibili dai dispositivi.

1. Se necessario, usare l'elenco a discesa per selezionare un valore di telemetria da visualizzare nel riquadro. È possibile aggiungere altri elementi al riquadro in un altro selezionando **+ telemetria** , **+ Proprietà** o **+ Proprietà Cloud**.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Aggiungere un riquadro di telemetria della temperatura al dashboard":::

Dopo aver selezionato tutti i valori da visualizzare nel riquadro, fare clic su **Aggiungi riquadro.** Il riquadro ora viene visualizzato nel dashboard in cui è possibile modificare la visualizzazione, ridimensionarla, spostarla e configurarla.

Al termine dell'aggiunta e della personalizzazione dei riquadri nel dashboard, selezionare **Salva** per salvare le modifiche nel dashboard, che consente di uscire dalla modalità di modifica.

## <a name="customize-tiles"></a>Personalizzare i riquadri

Per modificare un riquadro, è necessario essere in modalità di modifica.  Le opzioni di personalizzazione disponibili dipendono dal [tipo di riquadro](#tile-types):

* L'icona del righello in una sezione consente di modificare la visualizzazione. Le visualizzazioni includono grafici a linee, grafici a barre, grafici a torta, ultimi valori noti, indicatori di prestazioni chiave (o indicatori KPI), Heatmaps e mappe.

* L'icona quadrata consente di ridimensionare il riquadro.

* L'icona a forma di ingranaggio consente di configurare la visualizzazione. Per la visualizzazione di un grafico a linee, ad esempio, è possibile scegliere di visualizzare la legenda e gli assi e scegliere l'intervallo di tempo da tracciare.


## <a name="tile-types"></a>Tipi di riquadro

La tabella seguente descrive i diversi tipi di riquadro che è possibile aggiungere a un dashboard:

| Tile             | Descrizione |
| ---------------- | ----------- |
| Markdown         | I riquadri Markdown sono riquadri selezionabili che visualizzano un testo di intestazione e descrizione formattato usando Markdown. L'URL può essere un collegamento relativo a un'altra pagina dell'applicazione o un collegamento assoluto a un sito esterno.|
| Immagine            | I riquadri immagine visualizzano un'immagine personalizzata e possono essere selezionabili. L'URL può essere un collegamento relativo a un'altra pagina dell'applicazione o un collegamento assoluto a un sito esterno.|
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

Per impostazione predefinita, i grafici a linee mostrano i dati in un intervallo di tempo. L'intervallo di tempo selezionato è suddiviso in bucket di dimensioni pari a 50 e i dati del dispositivo vengono quindi aggregati per bucket per fornire 50 punti dati nell'intervallo di tempo selezionato. Se si desidera visualizzare i dati non elaborati, è possibile modificare la selezione per visualizzare gli ultimi 100 valori. Per modificare l'intervallo di tempo o selezionare visualizzazione dati non elaborati, utilizzare l'elenco a discesa intervallo di visualizzazione nel pannello **Configura grafico** .

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/display-range.png" alt-text="Modificare l'intervallo di visualizzazione di un grafico a linee":::

Per i riquadri che visualizzano i valori di aggregazione, selezionare l'icona dell'ingranaggio accanto al tipo di telemetria nel pannello **Configura grafico** per scegliere l'aggregazione. È possibile scegliere tra media, somma, massimo, minimo e conteggio.

Per i grafici a linee, i grafici a barre e i grafici a torta, è possibile personalizzare il colore dei diversi valori di telemetria. Selezionare l'icona della tavolozza accanto ai dati di telemetria che si desidera personalizzare:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Modificare il colore di un valore di telemetria":::

Per i riquadri che mostrano le proprietà di stringa o i valori di telemetria, è possibile scegliere come visualizzare il testo. Se, ad esempio, il dispositivo archivia un URL in una proprietà di stringa, è possibile visualizzarlo come collegamento selezionabile. Se l'URL fa riferimento a un'immagine, è possibile eseguire il rendering dell'immagine in un riquadro ultimo valore noto o proprietà. Per modificare la modalità di visualizzazione di una stringa, selezionare l'icona a forma di ingranaggio accanto alla proprietà o al tipo di telemetria:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Modificare il modo in cui una stringa viene visualizzata in un riquadro":::

Per **indicatori KPI** numerici, **ultimo valore noto** e riquadri delle **proprietà** , è possibile usare la formattazione condizionale per personalizzare il colore del riquadro in base al relativo valore corrente. Per aggiungere la formattazione condizionale, selezionare **Configura** nel riquadro, quindi selezionare l'icona **formattazione condizionale** accanto al valore da personalizzare:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="Screenshot che illustra come trovare l'opzione Configura per un riquadro e quindi l'icona formattazione condizionale":::

Aggiungere le regole di formattazione condizionale:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="Screenshot che illustra le regole di formattazione condizionale per il flusso medio. Sono presenti tre regole: meno di 20 è verde, minore di 50 è gialla e qualsiasi altro oltre 50 è rosso":::
   
Lo screenshot seguente mostra l'effetto della regola di formattazione condizionale:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="Screenshot che mostra il colore di sfondo rosso nel riquadro medio del flusso d'acqua. Il numero sul riquadro è 50,54":::

### <a name="tile-formatting"></a>formattazione "affiancata"
Questa funzionalità, disponibile nei riquadri KPI, LKV e proprietà, consente agli utenti di modificare le dimensioni del carattere, scegliere la precisione decimale, abbreviare i valori numerici (ad esempio, formattare 1.700 come 1.7 K) o racchiudere i valori di stringa nei riquadri.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/tile-format.png" alt-text="Formato riquadro":::

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare il dashboard dell'applicazione predefinito di Azure IoT Central, è possibile [acquisire informazioni su come creare un dashboard personale](howto-create-personal-dashboards.md).
