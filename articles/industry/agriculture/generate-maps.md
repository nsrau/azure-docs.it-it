---
title: Generare mappe
description: Questo articolo descrive come generare mappe in Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: f46bf247e699eddf3082756798953b877381bd65
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851316"
---
# <a name="generate-maps"></a>Generare mappe

Usando Azure FarmBeats, è possibile generare le mappe seguenti usando immagini satellite e input di dati del sensore. Le mappe consentono di visualizzare la posizione geografica della farm e di identificare la posizione appropriata per i dispositivi.

  - **Mappa di posizionamento dei sensori**: fornisce consigli sul numero di sensori da usare e sulla posizione in cui posizionarli in una farm.
  - **Mappa degli indici satellite**: Mostra l'indice della vegetazione e l'indice dell'acqua per una farm.
  - **Soil umidità mappa termica**: Mostra la distribuzione dell'umidità del suolo fondendo i dati satellite e i dati del sensore.

## <a name="sensor-placement-map"></a>Mappa posizionamento sensori

Una mappa di posizionamento del sensore FarmBeats ti aiuta a posizionare i sensori di umidità del suolo. L'output della mappa è costituito da un elenco di coordinate per la distribuzione del sensore. Gli input di questi sensori vengono usati insieme alle immagini satellite per generare la mappa termica di umidità del suolo. 

Questa mappa è derivata dalla segmentazione del baldacchino, come illustrato in più date nell'anno. Anche il suolo e gli edifici sono parte della copertura. È possibile rimuovere i sensori che non sono necessari per la posizione. Questa mappa è per le linee guida ed è possibile modificare leggermente la posizione e i numeri in base alle informazioni personalizzate. Se si aggiungono sensori, i risultati del mappa termica di umidità del suolo non vengono regressioni, ma è possibile che si verifichi un peggioramento dell'accuratezza mappa termica se il numero del sensore è 

## <a name="before-you-begin"></a>Prima di iniziare 

Prima di provare a generare una mappa di posizionamento dei sensori, soddisfare i prerequisiti seguenti:

- La dimensione della farm deve essere maggiore di un acro.
- Il numero di scene Sentinel senza cloud deve essere superiore a sei per l'intervallo di date selezionato. 
- Almeno sei scene Sentinel gratuite nel cloud devono avere un indice di vegetazione della differenza normalizzata (NDVI) maggiore o uguale a 0,3.

    > [!NOTE]
    > Sentinel consente solo due thread simultanei per utente. Di conseguenza, i processi vengono accodati e il completamento potrebbe richiedere più tempo.

### <a name="dependencies-on-sentinel"></a>Dipendenze da Sentinel 

Le dipendenze seguenti riguardano Sentinel:

- Dipendono dalle prestazioni di Sentinel per il download di immagini satellite. Verificare lo stato delle prestazioni di Sentinel e le [attività](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)di manutenzione.
- Sentinel consente solo due [thread di download](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) simultanei per utente.
- La generazione della mappa di precisione è interessata dal [code coverage sentinella e dalla frequenza di rivisita]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage).

## <a name="create-a-sensor-placement-map"></a>Creare una mappa di posizionamento del sensore
Questa sezione illustra in dettaglio le procedure per la creazione di mappe di selezione host del sensore.

> [!NOTE]
> È possibile avviare una mappa di posizionamento dei sensori dalla pagina **mappe** o dal menu a discesa **genera mappe di precisione** nella pagina dei **Dettagli della farm** .

Seguire questa procedura.

1. Nella home page passare a **Maps** dal menu di spostamento a sinistra.
2. Selezionare **Crea mappe**e selezionare **posizionamento dei sensori** dal menu a discesa.

    ![Selezionare la posizione del sensore](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Dopo aver selezionato la **posizione dei sensori**, viene visualizzata la finestra **selezione host** .

    ![Finestra posizionamento sensore](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Selezionare una farm dal menu a discesa **Farm** . 
   Per cercare e selezionare la farm, è possibile scorrere l'elenco a discesa o immettere il nome della farm nella casella di testo.
5. Per generare una mappa per l'ultimo anno, selezionare **consigliato**.
6. Per generare una mappa per un intervallo di date personalizzato, selezionare l'opzione **Seleziona intervallo di date**. Immettere la data di inizio e di fine per cui si vuole generare la mappa di selezione host del sensore.
7. Selezionare **genera mappe**.
 Viene visualizzato un messaggio di conferma con i dettagli del processo.

  Per informazioni sullo stato dei processi, vedere la sezione **visualizzare i processi**. Se lo stato del processo *non è riuscito*, viene visualizzato un messaggio di errore dettagliato nella descrizione comando dello stato *non riuscito* . In questo caso, ripetere i passaggi precedenti e riprovare.

  Se il problema persiste, vedere la sezione [risoluzione dei problemi](troubleshoot-project-farmbeats.md) o contattare il [Forum di Azure FarmBeats per assistenza](https://aka.ms/FarmBeatsMSDN) con i log pertinenti.

### <a name="view-and-download-a-sensor-placement-map"></a>Visualizzare e scaricare una mappa di posizionamento del sensore

Seguire questa procedura.

1. Nella home page passare a **Maps** dal menu di spostamento a sinistra.

    ![Selezionare Maps dal menu di spostamento a sinistra](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selezionare **filtro** nel percorso di spostamento a sinistra della finestra.
  Nella finestra **filtro** vengono visualizzati i criteri di ricerca.

    ![Finestra Filtro](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selezionare i valori di **tipo**, **Data**e **nome** nei menu a discesa. Selezionare quindi **applica** per cercare la mappa che si desidera visualizzare.
  La data in cui è stato creato il processo viene visualizzata nel formato type_farmname_YYYY-MM-GG.
4. Scorrere l'elenco delle mappe disponibili usando le barre di navigazione alla fine della pagina.
5. Selezionare la mappa che si desidera visualizzare. In una finestra popup viene visualizzata l'anteprima per la mappa selezionata.
6. Selezionare **Scarica**e scaricare il file GeoJSON delle coordinate del sensore.

    ![Anteprima mappa posizionamento sensori](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Mappa degli indici satellite

Le sezioni seguenti illustrano le procedure necessarie per la creazione e la visualizzazione di una mappa di indici satellite.

> [!NOTE]
> È possibile avviare una mappa di indici satellite dalla pagina **mappe** o dal menu a discesa **genera mappe di precisione** nella pagina dei dettagli della **Farm** .

FarmBeats offre la possibilità di generare NDVI, Enhanced vegetazione index (EVI) e mappe NDWI (Difference water index) normalizzate per le farm. Questi indici consentono di determinare il modo in cui la coltura è in continua crescita o è cresciuta in passato e i livelli di acqua rappresentativi.


> [!NOTE]
> È necessaria un'immagine Sentinel per i giorni per i quali viene generata la mappa.


## <a name="create-a-satellite-indices-map"></a>Creare una mappa degli indici satellite

Seguire questa procedura.

1. Nella home page passare a **Maps** dal menu di spostamento a sinistra.
2. Selezionare **Crea mappe**e selezionare **indici satellite** dal menu a discesa.

    ![Selezionare indici satellite dal menu a discesa](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Dopo aver selezionato gli **indici satellite**, viene visualizzata la finestra **indici satellite** .

    ![Finestra indici satellite](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Selezionare una farm dal menu a discesa. 
   Per cercare e selezionare la farm, è possibile scorrere l'elenco a discesa o immettere il nome della farm.   
5. Per generare una mappa per l'ultima settimana, selezionare **questa settimana**.
6. Per generare una mappa per un intervallo di date personalizzato, selezionare l'opzione **Seleziona intervallo di date**. Immettere la data di inizio e di fine per cui si vuole generare la mappa degli indici satellite.
7. Selezionare **genera mappe**.
    Viene visualizzato un messaggio di conferma con i dettagli del processo.

    ![Messaggio di conferma Mappa degli indici satellite](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Per informazioni sullo stato dei processi, vedere la sezione **visualizzare i processi**. Se lo stato del processo *non è riuscito*, viene visualizzato un messaggio di errore dettagliato nella descrizione comando dello stato *non riuscito* . In questo caso, ripetere i passaggi precedenti e riprovare.

    Se il problema persiste, vedere la sezione [risoluzione dei problemi](troubleshoot-project-farmbeats.md) o contattare il [Forum di Azure FarmBeats per assistenza](https://aka.ms/FarmBeatsMSDN) con i log pertinenti.

### <a name="view-and-download-a-map"></a>Visualizzare e scaricare una mappa

Seguire questa procedura.

1. Nella home page passare a **Maps** dal menu di spostamento a sinistra.

    ![Selezione mappe](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selezionare **filtro** nel percorso di spostamento a sinistra della finestra. Nella finestra **filtro** vengono visualizzati i criteri di ricerca.

    ![Finestra Filtro Visualizza i criteri di ricerca](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selezionare i valori di **tipo**, **Data**e **nome** nei menu a discesa. Selezionare quindi **applica** per cercare la mappa che si desidera visualizzare.
  La data in cui è stato creato il processo viene visualizzata nel formato type_farmname_YYYY-MM-GG.

4. Scorrere l'elenco delle mappe disponibili usando le barre di navigazione alla fine della pagina.
5. Per ogni combinazione di **nome della farm** e **Data**, sono disponibili le tre mappe seguenti:
    - NDVI
    - EVI
    - NDWI
6. Selezionare la mappa che si desidera visualizzare. In una finestra popup viene visualizzata l'anteprima per la mappa selezionata.
7. Selezionare **Scarica** dal menu a discesa per selezionare il formato di download. La mappa viene scaricata e archiviata nella cartella locale del computer.

    ![Anteprima mappa degli indici satellite selezionati](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Umidità del suolo mappa termica

L'umidità del suolo è l'acqua tenuta negli spazi tra le particelle del suolo. Il mappa termica di umidità del suolo ti aiuta a comprendere i dati di umidità del suolo a qualsiasi profondità, a risoluzione elevata nella tua farm. Per generare un mappa termica di umidità del suolo accurato e utilizzabile, è necessaria una distribuzione uniforme dei sensori. Tutti i sensori devono provenire dallo stesso provider. Diversi provider presentano differenze nel modo in cui viene misurata l'umidità del suolo insieme alle differenze nella taratura. Il mappa termica viene generato per una particolare profondità usando i sensori distribuiti a tale profondità.

### <a name="before-you-begin"></a>Prima di iniziare 

Prima di provare a generare un mappa termica di umidità del suolo, rispettare i prerequisiti seguenti:

- Devono essere distribuiti almeno tre sensori di umidità del suolo. Non provare a creare un mappa termica di umidità del suolo prima che i sensori vengano distribuiti e associati alla farm. 
- La generazione di un mappa termica di umidità del suolo è influenzata dalla copertura del percorso di Sentinel, dal cloud cover e dall'ombreggiatura cloud. Almeno una scena Sentinel senza cloud deve essere disponibile negli ultimi 120 giorni, dal giorno in cui è stata richiesta la mappa termica di umidità del suolo.
- Almeno la metà dei sensori distribuiti nella farm deve essere online e disporre di flussi di dati nell'hub dati.
- Mappa termica deve essere generato usando le misure del sensore dello stesso provider.


## <a name="create-a-soil-moisture-heatmap"></a>Creare una mappa termica di umidità del suolo

Seguire questa procedura.

1. Nella home page passare a **Maps** dal menu di spostamento a sinistra per visualizzare la pagina **Maps** .
2. Selezionare **create maps**e selezionare **Soil umidità** dal menu a discesa.

    ![Selezionare umidità del suolo dal menu a discesa](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Dopo aver selezionato l' **umidità del suolo**, viene visualizzata la finestra umidità del **suolo** .

    ![Finestra umidità del suolo](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Selezionare una farm dal menu a discesa **Farm** . 
   Per cercare e selezionare la farm, è possibile scorrere dall'elenco a discesa o immettere il nome della farm nel menu a discesa **Seleziona Farm** .
5. Nel menu a discesa **Seleziona misura sensore di umidità del suolo** selezionare la misura del sensore di umidità del suolo (profondità) per cui si vuole generare la mappa.
Per trovare la misura del sensore, passare a **sensori**e selezionare qualsiasi sensore di umidità del suolo. Quindi, nella sezione **Proprietà sensore** , usare il valore in **Nome misura**.
6. Per generare una mappa per **oggi** o **questa settimana**, selezionare una delle opzioni.
7. Per generare una mappa per un intervallo di date personalizzato, selezionare l'opzione **Seleziona intervallo di date**. Immettere la data di inizio e di fine per cui si vuole generare la mappa termica di umidità del suolo.
8. Selezionare **genera mappe**.
 Viene visualizzato un messaggio di conferma con i dettagli del processo.

   ![Messaggio di conferma della mappa umidità del suolo](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Per informazioni sullo stato dei processi, vedere la sezione **visualizzare i processi**. Se lo stato del processo *non è riuscito*, viene visualizzato un messaggio di errore dettagliato nella descrizione comando dello stato *non riuscito* . In questo caso, ripetere i passaggi precedenti e riprovare.

    Se il problema persiste, vedere la sezione [risoluzione dei problemi](troubleshoot-project-farmbeats.md) o contattare il [Forum di Azure FarmBeats per assistenza](https://aka.ms/FarmBeatsMSDN) con i log pertinenti.

### <a name="view-and-download-a-map"></a>Visualizzare e scaricare una mappa

Seguire questa procedura.

1. Nella home page passare a **Maps** dal menu di spostamento a sinistra.

    ![Vai a Maps](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selezionare **filtro** nel percorso di spostamento a sinistra della finestra. Viene visualizzata la finestra **filtro** in cui è possibile cercare mappe.

    ![Selezionare filtro nel percorso di spostamento a sinistra](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Selezionare i valori di **tipo**, **Data**e **nome** nei menu a discesa. Selezionare quindi **applica** per cercare la mappa che si desidera visualizzare. La data in cui è stato creato il processo viene visualizzata nel formato type_farmname_YYYY-MM-GG.
4. Selezionare l'icona di **ordinamento** accanto alle intestazioni di tabella per eseguire l'ordinamento in base a **Farm**, **Data**, **created on**, **ID processo**e **tipo di processo**.
5. Scorrere l'elenco delle mappe disponibili usando i pulsanti di spostamento alla fine della pagina.
6. Selezionare la mappa che si desidera visualizzare. In una finestra popup viene visualizzata l'anteprima per la mappa selezionata.
7. Selezionare **Scarica** dal menu a discesa per selezionare il formato di download. La mappa viene scaricata e archiviata nella cartella specificata.

    ![Mappa termica di umidità del suolo](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
