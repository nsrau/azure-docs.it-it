---
title: Generare mappe
description: In questo articolo viene descritto come generare mappe in Azure FarmBeats.This article describes how to generate maps in Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271772"
---
# <a name="generate-maps"></a>Generare mappe

Usando FarmBeats di Azure, è possibile generare le mappe seguenti usando immagini satellitari e input di dati dei sensori. Le mappe consentono di visualizzare la posizione geografica della farm e di identificare la posizione appropriata per i dispositivi.

  - **Mappa di posizionamento dei**sensori : Fornisce consigli su quanti sensori utilizzare e dove posizionarli in una fattoria.
  - **Mappa indici satellitari**: Mostra l'indice di vegetazione e l'indice dell'acqua per una farm.
  - **Mappa di calore dell'umidità**del suolo : Mostra la distribuzione dell'umidità del suolo fondendo i dati satellitari e i dati dei sensori.

## <a name="sensor-placement-map"></a>Mappa di posizionamento del sensore

Una mappa di posizionamento di FarmBeats Sensor ti aiuta nel posizionamento dei sensori di umidità del suolo. L'output della mappa è costituito da un elenco di coordinate per la distribuzione dei sensori. Gli ingressi di questi sensori vengono utilizzati insieme alle immagini satellitari per generare la mappa termica dell'umidità del suolo.

Questa mappa è derivata dalla segmentazione del baldacchino come visto in più date durante tutto l'anno. Anche il terreno nudo e gli edifici fanno parte del baldacchino. È possibile rimuovere i sensori che non sono necessari sul posto. Questa mappa è di riferimento ed è possibile modificare leggermente la posizione e i numeri in base alle proprie conoscenze personalizzate. L'aggiunta di sensori non regredisce i risultati della mappa di calore per l'umidità del suolo, ma c'è la possibilità di peggiorare la precisione della mappa termica se il numero del sensore è ridotto.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di tentare di generare una mappa Sensor Placement, attenersi ai seguenti prerequisiti:

- La dimensione della farm deve essere superiore a un acro.
- Il numero di scene Sentinel senza nuvole deve essere superiore a sei per l'intervallo di date selezionato.
- Almeno sei scene Sentinel senza nuvole devono avere un Normalized Difference Vegetation Index (NDVI) maggiore o uguale a 0.3.

    > [!NOTE]
    > Sentinel consente solo due thread simultanei per utente. Di conseguenza, i processi vengono accodati e potrebbero richiedere più tempo per il completamento.

### <a name="dependencies-on-sentinel"></a>Dipendenze da Sentinel

Le dipendenze seguenti riguardano Sentinel:

- Dipendiamo dalle prestazioni di Sentinel per scaricare immagini satellitari. Controllare lo stato delle prestazioni Sentinel e le [attività](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)di manutenzione .
- Sentinel consente solo due thread di [download](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) simultanei per utente.
- La generazione di mappe di precisione è influenzata dalla copertura Sentinel e dalla frequenza di [rivisitazione.]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)

## <a name="create-a-sensor-placement-map"></a>Creare una mappa di posizionamento del sensore
In questa sezione vengono descritte in dettaglio le procedure per la creazione di mappe Sensor Placement.

> [!NOTE]
> È possibile avviare una mappa Di posizionamento sensore dalla pagina **Mappe** o dal menu a discesa Genera mappe di **precisione** nella pagina **Dettagli farm.**

Seguire questa procedura.

1. Nella home page, vai a **Mappe** dal menu di navigazione a sinistra.
2. Selezionare **Crea mappe**e dal menu a discesa **Sensor Placement** .

    ![Selezionare Il posizionamento del sensore](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Dopo aver selezionato **Sensor Placement (Posizionamento sensore),** viene visualizzata la finestra **Sensor Placement (Posizionamento sensore).**

    ![Finestra Posizionamento sensore](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Selezionare una farm dal menu a discesa **Farm.**
   Per eseguire la ricerca e selezionare la farm, è possibile scorrere nell'elenco a discesa o immettere il nome della farm nella casella di testo.
5. Per generare una mappa per l'ultimo anno, selezionare **Consigliato**.
6. Per generare una mappa per un intervallo di date personalizzato, selezionare l'opzione **Seleziona intervallo di date**. Immettere la data di inizio e di fine per la quale si desidera generare la mappa Posizionamento sensore.
7. Selezionare **Genera mappe**.
 Viene visualizzato un messaggio di conferma con i dettagli del processo.

  Per informazioni sullo stato dei processi, vedere la sezione **Visualizzazione dei processi**. Se lo stato del processo è *Non riuscito*, viene visualizzato un messaggio di errore dettagliato nella descrizione comando dello stato *Non riuscito.* In questo caso, ripetere i passaggi precedenti e riprovare.

  Se il problema persiste, vedere la sezione [Risoluzione dei problemi](troubleshoot-azure-farmbeats.md) o contattare il [forum di Azure FarmBeats per assistenza](https://aka.ms/FarmBeatsMSDN) con i log pertinenti.

### <a name="view-and-download-a-sensor-placement-map"></a>Visualizzare e scaricare una mappa Sensor Placement

Seguire questa procedura.

1. Nella home page, vai a **Mappe** dal menu di navigazione a sinistra.

    ![Seleziona Mappe dal menu di navigazione a sinistra](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selezionare **Filtro** nella barra di spostamento sinistra della finestra.
  Nella finestra **Filtro** vengono visualizzati i criteri di ricerca.

    ![Finestra Filtro](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selezionare I valori **Type**, **Date**e **Name** dai menu a discesa. Selezionare **quindi Applica** per cercare la mappa che si desidera visualizzare.
  La data in cui è stato creato il lavoro viene visualizzata nel formato type_farmname_YYYY-MM-DD.
4. Scorrere l'elenco delle mappe disponibili utilizzando le barre di navigazione alla fine della pagina.
5. Selezionare la mappa che si desidera visualizzare. Viene visualizzata una finestra popup con l'anteprima della mappa selezionata.
6. Selezionare **Scarica**e scaricare il file GeoJSON delle coordinate del sensore.

    ![Anteprima mappa posizionamento sensore](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Mappa degli indici satellitari

Nelle sezioni seguenti vengono illustrate le procedure necessarie per la creazione e la visualizzazione di una mappa degli indici satellitari.

> [!NOTE]
> È possibile avviare una mappa degli indici satellitari dalla pagina **Mappe** o dal menu a discesa Genera mappe di **precisione** nella pagina **Dettagli farm.**

FarmBeats offre la possibilità di generare mappe NDVI, Enhanced Vegetation Index (EVI) e Normalized Difference Water Index (NDWI) per le farm. Questi indici aiutano a determinare come il raccolto è attualmente in crescita, o è cresciuto in passato, e il livello di acqua rappresentativa nel terreno.


> [!NOTE]
> Un'immagine Sentinel è necessaria per i giorni per i quali viene generata la mappa.


## <a name="create-a-satellite-indices-map"></a>Creazione di una mappa degli indici satellitari

Seguire questa procedura.

1. Nella home page, vai a **Mappe** dal menu di navigazione a sinistra.
2. Selezionare **Crea mappe**e dal menu a discesa **-.**

    ![Selezionare Indici satellitari dal menu a discesa](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Dopo aver selezionato **Indici Satellite**, viene visualizzata la finestra **Indici Satellite.**

    ![Finestra Indici satellite](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Selezionare una farm dal menu a discesa.
   Per eseguire la ricerca e selezionare la farm, è possibile scorrere nell'elenco a discesa o immettere il nome della farm.   
5. Per generare una mappa per l'ultima settimana, selezionare **Questa settimana**.
6. Per generare una mappa per un intervallo di date personalizzato, selezionare l'opzione **Seleziona intervallo di date**. Immettere la data di inizio e di fine per la quale si desidera generare la mappa Indici satellite.
7. Selezionare **Genera mappe**.
    Viene visualizzato un messaggio di conferma con i dettagli del processo.

    ![Messaggio di conferma della mappa degli indici satellitari](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Per informazioni sullo stato dei processi, vedere la sezione **Visualizzazione dei processi**. Se lo stato del processo è *Non riuscito*, viene visualizzato un messaggio di errore dettagliato nella descrizione comando dello stato *Non riuscito.* In questo caso, ripetere i passaggi precedenti e riprovare.

    Se il problema persiste, vedere la sezione [Risoluzione dei problemi](troubleshoot-azure-farmbeats.md) o contattare il [forum di Azure FarmBeats per assistenza](https://aka.ms/FarmBeatsMSDN) con i log pertinenti.

### <a name="view-and-download-a-map"></a>Visualizzare e scaricare una mappa

Seguire questa procedura.

1. Nella home page, vai a **Mappe** dal menu di navigazione a sinistra.

    ![Seleziona mappe](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selezionare **Filtro** nella barra di spostamento sinistra della finestra. Nella finestra **Filtro** vengono visualizzati i criteri di ricerca.

    ![La finestra Filtro visualizza i criteri di ricerca](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selezionare I valori **Type**, **Date**e **Name** dai menu a discesa. Selezionare **quindi Applica** per cercare la mappa che si desidera visualizzare.
  La data in cui è stato creato il lavoro viene visualizzata nel formato type_farmname_YYYY-MM-DD.

4. Scorrere l'elenco delle mappe disponibili utilizzando le barre di navigazione alla fine della pagina.
5. Per ogni combinazione di **Nome farm** e **Data**, sono disponibili le tre mappe seguenti:
    - Ndvi
    - Evi
    - NDWI (NDWI)
6. Selezionare la mappa che si desidera visualizzare. Viene visualizzata una finestra popup con l'anteprima della mappa selezionata.
7. Selezionare **Download** dal menu a discesa per selezionare il formato di download. La mappa viene scaricata e memorizzata nella cartella locale del computer.

    ![Anteprima mappa Indici satellitari selezionati](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Mappa di calore dell'umidità del suolo

L'umidità del suolo è l'acqua che si tiene negli spazi tra le particelle del suolo.La mappa termica dell'umidità del suolo consente di comprendere i dati relativi all'umidità del suolo a qualsiasi profondità, ad alta risoluzione all'interno dell'azienda agricola. Per generare una mappa di calore dell'umidità del suolo accurata e utilizzabile, è necessaria una distribuzione uniforme dei sensori. Tutti i sensori devono provenire dallo stesso fornitore. Diversi fornitori hanno differenze nel modo in cui l'umidità del suolo viene misurata insieme alle differenze nella calibrazione. La mappa termica viene generata per una particolare profondità utilizzando i sensori dispiegati a quella profondità.

### <a name="before-you-begin"></a>Prima di iniziare

Soddisfare i seguenti prerequisiti prima di tentare di generare una mappa termica dell'umidità del suolo:

- Devono essere impiegati almeno tre sensori di umidità del suolo. Non cercare di creare una mappa di calore dell'umidità del suolo prima che i sensori vengano distribuiti e associati alla farm.
- La generazione di una mappa di calore dell'umidità del suolo è influenzata dalla copertura del percorso di Sentinel, dalla copertura nuvolosa e dall'ombra della nuvola. Almeno una scena Sentinel senza nuvole deve essere disponibile per gli ultimi 120 giorni, dal giorno per il quale è stata richiesta la mappa termica dell'umidità del suolo.
- Almeno la metà dei sensori distribuiti nella farm deve essere online e disporre di streaming di dati nell'hub dati.
- La mappa termica deve essere generata utilizzando misure del sensore dello stesso fornitore.


## <a name="create-a-soil-moisture-heatmap"></a>Creare una mappa termica dell'umidità del suolo

Seguire questa procedura.

1. Nella home page, vai a **Mappe** dal menu di navigazione a sinistra per visualizzare la pagina **Mappe.**
2. Selezionare **Crea mappe**e dal menu a discesa **L'umidità** del suolo.

    ![Selezionare Umidità del suolo dal menu a discesa](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Dopo aver selezionato **Umidità del suolo**, viene visualizzata la finestra **Umidità suolo.**

    ![Finestra dell'umidità del suolo](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Selezionare una farm dal menu a discesa **Farm.**
   Per eseguire la ricerca e selezionare la farm, è possibile scorrere dall'elenco a discesa o immettere il nome della farm nel menu a discesa **Seleziona farm.**
5. Nel menu a discesa **Select Soil Moisture Sensor Measure,** selezionare la misura (profondità) del sensore di umidità del suolo per la quale si desidera generare la mappa.
Per trovare la misura del sensore, andare a **Sensori**e selezionare qualsiasi sensore di umidità del suolo. Quindi, nella sezione **Proprietà sensore,** utilizzare il valore in **Nome misura**.
6. Per generare una mappa per **Oggi** o **Questa settimana,** selezionare una delle opzioni.
7. Per generare una mappa per un intervallo di date personalizzato, selezionare l'opzione **Seleziona intervallo di date**. Immettere la data di inizio e di fine per la quale si desidera generare la mappa termica dell'umidità del suolo.
8. Selezionare **Genera mappe**.
 Viene visualizzato un messaggio di conferma con i dettagli del processo.

   ![Messaggio di conferma della mappa dell'umidità del suolo](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Per informazioni sullo stato dei processi, vedere la sezione **Visualizzazione dei processi**. Se lo stato del processo è *Non riuscito*, viene visualizzato un messaggio di errore dettagliato nella descrizione comando dello stato *Non riuscito.* In questo caso, ripetere i passaggi precedenti e riprovare.

    Se il problema persiste, vedere la sezione [Risoluzione dei problemi](troubleshoot-azure-farmbeats.md) o contattare il [forum di Azure FarmBeats per assistenza](https://aka.ms/FarmBeatsMSDN) con i log pertinenti.

### <a name="view-and-download-a-map"></a>Visualizzare e scaricare una mappa

Seguire questa procedura.

1. Nella home page, vai a **Mappe** dal menu di navigazione a sinistra.

    ![Vai a Mappe](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selezionare **Filtro** nella barra di spostamento sinistra della finestra. Viene visualizzata la finestra **Filtro** da cui è possibile cercare le mappe.

    ![Selezionare Filtro nel riquadro di spostamento sinistro](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Selezionare I valori **Type**, **Date**e **Name** dai menu a discesa. Selezionare **quindi Applica** per cercare la mappa che si desidera visualizzare. La data in cui è stato creato il lavoro viene visualizzata nel formato type_farmname_YYYY-MM-DD.
4. Selezionare l'icona **Ordina** accanto alle intestazioni di tabella da ordinare in base a **Farm**, **Data**, **Creato il**, ID **processo**e Tipo **di processo**.
5. Scorrere l'elenco delle mappe disponibili utilizzando i pulsanti di navigazione alla fine della pagina.
6. Selezionare la mappa che si desidera visualizzare. Viene visualizzata una finestra popup con l'anteprima della mappa selezionata.
7. Selezionare **Download** dal menu a discesa per selezionare il formato di download. La mappa viene scaricata e memorizzata nella cartella specificata.

    ![Anteprima della mappa di calore dell'umidità del suolo](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
