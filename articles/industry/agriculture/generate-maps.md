---
title: Genera mappe
description: Viene descritto come generare mappe in FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 635431fb87e5f164f92ab4b7a1027ee96e9d801a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891020"
---
# <a name="generate-maps"></a>Genera mappe

Usando Azure FarmBeats, è possibile generare le mappe seguenti usando immagini satellite e input di dati del sensore. Le mappe consentono di visualizzare la posizione geografica della farm e di identificare la posizione appropriata per i dispositivi.

  -  **Mappa di posizionamento dei sensori** : fornisce consigli sul numero di sensori da usare e sulla posizione in cui inserirli in una farm.
  - **Mappa degli indici satellite** : Mostra l'indice della vegetazione e l'indice dell'acqua per una farm.
  - **Mappa umidità del suolo** : Mostra la distribuzione dell'umidità del suolo fondendo i dati satellite e i dati del sensore.

## <a name="sensor-placement-maps"></a>Mappe di selezione host del sensore

La mappa di posizionamento del sensore FarmBeats ti aiuta a posizionare i sensori di umidità del suolo. L'output della mappa è costituito da un elenco di coordinate per la distribuzione del sensore. Gli input di questi sensori vengono usati insieme alle immagini satellite per generare la mappa termica di umidità del suolo.  

Questa mappa è derivata dalla segmentazione del baldacchino, come illustrato in più date nell'anno, anche il suolo e gli edifici sono parte della copertura. È possibile rimuovere i sensori non necessari per la posizione. Questa mappa è per le linee guida ed è possibile modificare leggermente la posizione e i numeri in base alle proprie conoscenze personalizzate. l'aggiunta di sensori non comporta la regressione dei risultati della mappa termica di umidità, ma è possibile che si verifichi un peggioramento dell'accuratezza della mappa termica se il numero di sensore è ridotto.  

## <a name="before-you-begin"></a>Prima di iniziare  

Prima di provare a generare una mappa di selezione dei sensori, verificare quanto segue:

- Le dimensioni della farm devono essere maggiori di un acro.
- Il numero di scene Sentinel senza cloud deve essere superiore a sei per l'intervallo di date selezionato.  
- Almeno sei scene Sentinel gratuite nel cloud devono avere NDVI > = 0.3.

    > [!NOTE]
    > Sentinel consente solo due thread simultanei per utente. Di conseguenza, i processi vengono accodati e il completamento potrebbe richiedere più tempo.

### <a name="dependencies-on-sentinel"></a>Dipendenze da Sentinel  

Di seguito sono riportate le dipendenze sentinelle:

- Dipendono dalle prestazioni di Sentinel per il download di immagini satellite. Verificare lo stato delle prestazioni di Sentinel e le [attività](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)di manutenzione.
- Sentinel consente solo due [thread di download](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) simultanei per utente.
- La generazione della mappa di precisione sarà interessata dal [code coverage sentinella e dalla frequenza di rivisita]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage).

## <a name="create-sensor-placement-map"></a>Crea mappa di selezione host del sensore
La sezione descrive in dettaglio le procedure per la creazione di mappe di selezione host dei sensori.

> [!NOTE]
> È possibile avviare il posizionamento dei sensori dalla pagina **mappe** o dal menu a discesa **genera mappe di precisione** nella pagina dei **Dettagli della farm** .

Seguire questa procedura:

1. Nella home page passare a **Maps** dal menu di spostamento a sinistra.
2. Selezionare **Crea mappe** e selezionare **posizionamento dei sensori** dal menu a discesa.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Selezionare **posizionamento del sensore**.
  Viene visualizzata la finestra selezione host del sensore.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Selezionare una farm dal menu a discesa **Farm** .  
   Per cercare e selezionare la farm, è possibile scorrere l'elenco a discesa o digitare il nome della farm nella casella di testo.
5. Se si desidera generare una mappa per l'ultimo anno, selezionare opzione **consigliata** .
6. Se si vuole generare una mappa per un intervallo di date personalizzato, selezionare **Seleziona intervallo di date**e specificare la data di inizio e di fine per cui si vuole generare la mappa di selezione host del sensore.
7. Selezionare **genera mappe**.
 Viene visualizzato un messaggio di conferma con i dettagli del processo.

  Per informazioni sullo stato dei processi, vedere la sezione **visualizzare i processi**. Se lo stato del processo *non è riuscito*, viene visualizzato un messaggio di errore dettagliato nella descrizione comando dello stato *non riuscito* . In questo caso, ripetere i passaggi sopra elencati e riprovare.

  Se il problema persiste, vedere la sezione [risoluzione dei problemi](troubleshoot-project-farmbeats.md) o contattare il [Forum di Azure FarmBeats per assistenza](https://aka.ms/FarmBeatsMSDN) con i log pertinenti.

### <a name="view-and-download-sensor-placement-map"></a>Visualizza e Scarica la mappa di selezione host del sensore

Seguire questa procedura:

1. Nella home page passare a **Maps** dal menu di spostamento a sinistra.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selezionare **filtro** nel percorso di spostamento a sinistra della finestra.
  Nella finestra **filtro** vengono visualizzati i criteri di ricerca.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selezionare **tipo**, **Data** e **nome** dal menu a discesa e quindi fare clic su **applica** per cercare la mappa che si desidera visualizzare.
  La data in cui è stato creato il processo viene visualizzata nel formato type_farmname_YYYY-MM-GG.
4. Scorrere l'elenco delle mappe disponibili usando le barre di navigazione alla fine della pagina.
5. Selezionare la mappa che si desidera visualizzare. In una finestra popup viene visualizzata l'anteprima per la mappa selezionata.
6. Selezionare **Scarica**e scaricare il file GeoJSON delle coordinate del sensore.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Mappa degli indici satellite

Le sezioni seguenti illustrano le procedure necessarie per la creazione e la visualizzazione della mappa degli indici satellite.

> [!NOTE]
> È possibile avviare il mapping degli indici satellite dalla pagina **mappe** o dal menu a discesa **genera mappe di precisione** nella pagina dei **Dettagli della farm** .

FarmBeats offre la possibilità di generare l'indice di vegetazione con differenze normalizzate (NDVI), l'indice di vegetazione migliorata (EVI) e le mappe NDWI (Normal Difference water index) per le farm. Questi indici consentono di determinare il modo in cui la coltura è in continua crescita o è cresciuta in passato e i livelli di acqua rappresentativi.


> [!NOTE]
> È necessaria un'immagine Sentinel per i giorni per i quali viene generata la mappa.


## <a name="create-satellite-indices-map"></a>Crea mappa degli indici satellite

Seguire questa procedura:

1. Nella home page passare a **Maps** dal menu di spostamento a sinistra.
2. Selezionare **Crea mappe** e selezionare **indici satellite** dal menu a discesa.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Selezionare **indici satellite**.
  Viene visualizzata la finestra indici satellite.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Selezionare una farm dal menu a discesa.  
   Per cercare e selezionare la farm, è possibile scorrere l'elenco a discesa o digitare il nome della farm.   
5. Se si desidera generare una mappa per l'ultima settimana, selezionare **questa opzione settimana** .
6. Se si vuole generare una mappa per un intervallo di date personalizzato, selezionare **Seleziona intervallo di date**e specificare la data di inizio e di fine per cui si vuole generare la mappa degli indici satellite.
7. Selezionare **genera mappe**.
    Viene visualizzato un messaggio di conferma con i dettagli del processo.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Per informazioni sullo stato dei processi, vedere la sezione **visualizzare i processi**. Se lo stato del processo *non è riuscito*, viene visualizzato un messaggio di errore dettagliato nella descrizione comando dello stato *non riuscito* . In questo caso, ripetere i passaggi sopra elencati e riprovare.

    Se il problema persiste, vedere la sezione [risoluzione dei problemi](troubleshoot-project-farmbeats.md) o contattare il [Forum di Azure FarmBeats per assistenza](https://aka.ms/FarmBeatsMSDN) con i log pertinenti.

### <a name="view-and-download-map"></a>Visualizza e Scarica mappa

Seguire questa procedura:

1. Nella home page passare a **Maps** dal menu di spostamento a sinistra.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selezionare **filtro** nella finestra di spostamento a sinistra della finestra. nella finestra **filtro** vengono visualizzati i criteri di ricerca.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selezionare **tipo**, **Data** e **nome** dal menu a discesa e quindi fare clic su **applica** per cercare la mappa che si desidera visualizzare.
  La data in cui è stato creato il processo viene visualizzata nel formato type_farmname_YYYY-MM-GG.

4. Scorrere l'elenco delle mappe disponibili usando le barre di navigazione alla fine della pagina.
5. Per ogni combinazione di **nome della farm** e **Data**, sono disponibili le tre mappe seguenti:
    - NDVI
    - EVI
    - NDWI
6. Selezionare la mappa che si desidera visualizzare. In una finestra popup viene visualizzata l'anteprima per la mappa selezionata.
7. Selezionare **download** menu a discesa per selezionare il formato di download e la mappa viene scaricata e archiviata nella cartella locale del computer.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="get-soil-moisture-heatmap"></a>Ottenere mappa termica umidità del suolo

L'umidità del suolo è l'acqua che viene mantenuta negli spazi tra le particelle del suolo. Il mappa termica di umidità del suolo ti aiuta a comprendere i dati di umidità del suolo a qualsiasi profondità, ad alta risoluzione nelle farm. Per generare un mappa termica di umidità del suolo accurato e utilizzabile, è necessaria una distribuzione uniforme dei sensori, in cui tutti i sensori appartengono allo stesso provider. Diversi provider avranno differenze nel modo in cui viene misurata l'umidità del suolo insieme alle differenze nella calibrazione. Il mappa termica viene generato per una particolare profondità usando i sensori distribuiti a tale profondità.

### <a name="before-you-begin"></a>Prima di iniziare  

Prima di provare a generare un mappa termica di umidità del suolo, verificare quanto segue:

- Devono essere distribuiti almeno tre sensori di umidità del suolo. Microsoft consiglia di non provare a creare una mappa di umidità del suolo prima che i sensori vengano distribuiti e associati alla farm.  
- Genera mappa termica di umidità del suolo è influenzato dalla copertura del percorso di Sentinel, dal cloud cover e dall'ombreggiatura cloud. Almeno una scena Sentinel gratuita nel cloud deve essere disponibile per gli ultimi 120 giorni, dal giorno in cui è stata richiesta la mappa di umidità del suolo.
- Almeno la metà dei sensori distribuiti nella farm deve essere online e disporre di flussi di dati nell'hub dati.
- Mappa termica deve essere generato usando le misure del sensore dello stesso provider.


## <a name="create-soil-moisture-heatmap"></a>Creare mappa termica di umidità del suolo

Seguire questa procedura:

1. Nella home page passare a **Maps** dal menu di spostamento a sinistra per visualizzare la pagina maps.
2. Selezionare **Crea mappe** e selezionare **umidità del suolo** dal menu a discesa.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Selezionare **umidità del suolo**.
    Viene visualizzata la finestra umidità del suolo.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Selezionare una farm dal menu a discesa **Farm** .  
   Per cercare e selezionare la farm, è possibile scorrere dall'elenco a discesa o digitare il nome della farm nel menu a discesa Seleziona farm.
5. Nel menu a discesa **Seleziona misura sensore di umidità del suolo** selezionare la misura del sensore di umidità del suolo (profondità) per cui si vuole generare la mappa.
Per trovare la misura del sensore, passare a **sensori**, selezionare un sensore di umidità del suolo. Quindi, nella sezione **Proprietà sensore** usare il valore in **Nome misura**
6. Se si desidera generare per **oggi** o **questa settimana**, selezionare una delle opzioni.
7. Se si vuole generare per un intervallo di date personalizzato, selezionare **Seleziona intervallo di date**e specificare la data di inizio e di fine per cui si vuole generare la mappa di umidità del terreno.
8. Selezionare **genera mappe**.
 Viene visualizzato un messaggio di conferma con i dettagli del processo.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Per informazioni sullo stato dei processi, vedere la sezione **visualizzare i processi**. Se lo stato del processo *non è riuscito*, viene visualizzato un messaggio di errore dettagliato nella descrizione comando dello stato *non riuscito* . In questo caso, ripetere i passaggi sopra elencati e riprovare.

    Se il problema persiste, vedere la sezione [risoluzione dei problemi](troubleshoot-project-farmbeats.md) o contattare il [Forum di Azure FarmBeats per assistenza](https://aka.ms/FarmBeatsMSDN) con i log pertinenti.

### <a name="view-and-download-map"></a>Visualizza e Scarica mappa

Seguire questa procedura:

1. Nella home page passare a **Maps** dal menu di spostamento a sinistra.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selezionare **filtro** nella finestra di spostamento a sinistra della finestra. verrà visualizzata la finestra di **filtro** in cui è possibile cercare maps.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Selezionare **tipo**, **Data** e **nome** dal menu a discesa e quindi fare clic su **applica** per cercare la mappa che si desidera visualizzare. La data in cui è stato creato il processo viene visualizzata nel formato type_farmname_YYYY-MM-GG.
4. Selezionare l'icona di **ordinamento** accanto alle intestazioni di tabella per eseguire l'ordinamento in base a farm, data, created on, ID processo e tipo di processo.
5. Scorrere l'elenco delle mappe disponibili usando i pulsanti di spostamento alla fine della pagina.
6. Selezionare la mappa che si desidera visualizzare. In una finestra popup viene visualizzata l'anteprima per la mappa selezionata.
7. Selezionare **download** menu a discesa per selezionare il formato di download e la mappa viene scaricata e archiviata nella cartella specificata.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
