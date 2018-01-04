---
title: 'Esercitazione sul bike sharing: preparazione avanzata dei dati con Azure Machine Learning Workbench'
description: Esercitazione di preparazione dei dati end-to-end con Azure Machine Learning Workbench
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial, azure
ms.topic: tutorial
ms.date: 09/21/2017
ms.openlocfilehash: 69f6911a95be382b06313d984f09c7e85aec10df
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="bike-share-tutorial-advanced-data-preparation-with-azure-machine-learning-workbench"></a>Esercitazione sul bike sharing: preparazione avanzata dei dati con Azure Machine Learning Workbench
I servizi di Azure Machine Learning (anteprima) sono una soluzione integrata di data science e analisi avanzata end-to-end che consente a data scientist professionisti di preparare i dati, sviluppare esperimenti e distribuire modelli a livello di cloud.

In questa esercitazione si usano i servizi di Azure Machine Learning (anteprima) per imparare a:
> [!div class="checklist"]
> * Preparare i dati in modo interattivo con lo strumento di preparazione dei dati di Azure Machine Learning
> * Importare, trasformare e creare un set di dati di test
> * Generare un pacchetto di preparazione dei dati
> * Eseguire il pacchetto di preparazione dei dati usando Python
> * Generare un set di dati di training riusando il pacchetto di preparazione dei dati per i file di input aggiuntivi

> [!IMPORTANT]
> In questa esercitazione si preparano solo i dati, non si compila il modello di previsione.
>
> È possibile usare i dati preparati per eseguire il training di modelli di previsione personalizzati. Ad esempio, si può creare un modello per stimare la richiesta di biciclette in un intervallo di 2 ore.

## <a name="prerequisites"></a>Prerequisiti
* Azure Machine Learning Workbench deve essere installato localmente. Per altre informazioni, seguire le istruzioni della [Guida introduttiva all'installazione](quickstart-installation.md).
* Familiarità con la creazione di un nuovo progetto nel Workbench.

## <a name="data-acquisition"></a>Acquisizione dei dati
Questa esercitazione usa il [set di dati del servizio Boston Hubway](https://s3.amazonaws.com/hubway-data/index.html) e i dati meteo su Boston del [NOAA](http://www.noaa.gov/).

1. Scaricare i file di dati dai collegamenti seguenti nell'ambiente di sviluppo locale. 
   * [Dati meteo su Boston](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv). 
   * Dati sulle tratte del servizio Hubway dal sito Web di Hubway.

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. Dopo il download, decomprimere tutti i file con estensione zip.

## <a name="learn-about-the-datasets"></a>Informazioni sui set di dati
1. Il file con i __dati meteo su Boston__ contiene i seguenti campi relativi alle condizioni meteorologiche, presentati su base oraria:
   * DATE
   * REPORTTPYE
   * HOURLYDRYBULBTEMPF
   * HOURLYRelativeHumidity
   * HOURLYWindSpeed

2. I dati del servizio __Hubway__ sono organizzati in file per anno e mese. Ad esempio, il file denominato `201501-hubway-tripdata.zip` include un file con estensione csv con i dati relativi a gennaio 2015. I dati contengono i campi seguenti e ogni riga rappresenta una tratta effettuata in bicicletta:

   * Durata della tratta (in secondi)
   * Data e ora di inizio
   * Data e ora di fine
   * ID e nome della stazione di partenza
   * ID e nome della stazione di arrivo
   * ID della bicicletta
   * Tipo di utente (Casual = utente con pass di 24 ore o 72 ore; Member = membro annuale o mensile)
   * Codice postale (se l'utente è un membro)
   * Sesso (specificato dal membro)

## <a name="create-a-new-project"></a>Creare un nuovo progetto
1. Avviare **Azure Machine Learning Workbench** dal menu Start o dall'utilità di avvio.

2. Creare un nuovo progetto di Azure Machine Learning.  Fare clic sul pulsante **+** nella pagina **Projects** (Progetti) oppure scegliere **File** > **New** (File - Nuovo).
   - Usare il modello **Blank Project** (Progetto vuoto).
   - Denominare il progetto **BikeShare**. 

## <a id="newdatasource"></a>Creare una nuova origine dati

1. Creare una nuova origine dati. Fare clic sul pulsante **Data** (Dati) (icona a forma di cilindro) sulla barra degli strumenti a sinistra. Verrà aperta la **visualizzazione dati**.

   ![Immagine della scheda con la visualizzazione dati](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. Aggiungere un'origine dati. Selezionare l'icona **+** e quindi selezionare **Add Data Source** (Aggiungi origine dati).

   ![Immagine della voce Add Data Source (Aggiungi origine dati)](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Aggiungere i dati meteo

1. **Data Store** (Archivio dati): selezionare l'archivio dati contenente i dati. Dal momento che si usano file, selezionare **File(s)/Directory** (File/Directory). Selezionare **Avanti** per continuare.

   ![Immagine della voce File(s)/Directory (File/Directory)](media/tutorial-bikeshare-dataprep/datasources.png)

2. **File Selection** (Selezione file): aggiungere i dati meteo. Individuare e selezionare il file `BostonWeather.csv` scaricato in precedenza. Fare clic su **Avanti**.

   ![Immagine della selezione del file con il file BostonWeater.csv selezionato](media/tutorial-bikeshare-dataprep/pickweatherdatafile.png)

3. **File Details** (Dettagli file): verificare lo schema del file rilevato. Azure Machine Learning Workbench analizza i dati nel file e deduce lo schema da usare.

   ![Immagine della voce File Details (Dettagli file)](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > In alcuni casi il Workbench potrebbe non rilevare lo schema corretto. È consigliabile verificare sempre che i parametri siano corretti per il set di dati usato. Per i dati meteo, verificare che siano impostati sui valori seguenti:
   >
   > * __File Type__ (Tipo file): Delimited File (csv, tsv, txt, etc.) (File con valori delimitati da virgole - csv, tsv, txt, ecc.)
   > * __Separator__ (Separatore): Comma [,] (Virgola [,])
   > * __Comment Line Character__ (Carattere riga di commento): non è impostato alcun valore.
   > * __Skip Lines Mode__ (Modalità righe ignorate): Don't skip (Non ignorare)
   > * __File Encoding__ (Codifica file): utf-8
   > * __Promote Headers Mode__ (Modalità intestazioni alzate di livello): Use Headers From First File (Usa intestazioni dal primo file)

   L'anteprima dei dati dovrebbe visualizzare le colonne seguenti:
   * **Percorso**
   * **DATE**
   * **REPORTTYPE**
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

   Per continuare, selezionare **Next** (Avanti).

4. **Data Types** (Tipi di dati): esaminare i tipi di dati rilevati automaticamente. Azure Machine Learning Workbench analizza i dati nel file e deduce i tipi di dati da usare.

   Per questi dati, modificare il `DATA TYPE` di tutte le colonne impostandolo su `String`.

   > [!NOTE]
   > `String` viene usato per mettere in evidenza le funzionalità del Workbench più avanti in questa esercitazione. 

   ![Immagine dei tipi di dati](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   Per continuare, selezionare __Next__ (Avanti). 

5. **Sampling** (Campionamento): per creare uno schema di campionamento, selezionare il pulsante **+ New** (+ Nuovo). Selezionare la nuova riga __Top 10000__ (Primi 10000) che viene aggiunta e quindi selezionare __Edit__ (Modifica). Impostare __Sample Strategy__ (Strategia campione) su **Full File** (File completo) e quindi selezionare **Apply** (Applica).

   ![Immagine dell'aggiunta di una nuova strategia di campionamento](media/tutorial-bikeshare-dataprep/weatherdatasampling.png)

   Per usare la strategia __Full File__ (File completo), selezionare la voce __Full File__ (File completo) e quindi selezionare __Set as Active__ (Imposta come attivo). Accanto a __Full File__ (File completo) verrà visualizzata una stella che indica che si tratta della strategia attiva.

   ![Immagine di Full File (File completo) come strategia attiva](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   Per continuare, selezionare **Next** (Avanti).

6. **Path Column** (Colonna percorso): la sezione __Path Column__ (Colonna percorso) consente di includere il percorso completo del file come colonna nei dati importati. Selezionare __Do Not Include Path Column__ (Non includere la colonna percorso).

   > [!TIP]
   > Includere il percorso come colonna è utile se si sta importando una cartella di molti file con nomi di file diversi. È anche utile se i nomi dei file contengono informazioni da estrarre in un secondo momento.

   ![Immagine della colonna percorso impostata su Do Not Include (Non includere)](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **Finish** (Fine): per completare la creazione dell'origine dati, selezionare il pulsante **Finish** (Fine).

    Si aprirà una nuova scheda per l'origine dati denominata __BostonWeather__. Un campione di dati viene visualizzato in una visualizzazione griglia. Il campione è basato sullo schema di campionamento attivo specificato in precedenza.

    Nel riquadro **Steps** (Passaggi) sul lato destro della schermata vengono visualizzate le singole azioni eseguite durante la creazione di questa origine dati.

   ![Immagine che visualizza l'origine dati, il campione e i passaggi](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Visualizzare le metriche dell'origine dati

Selezionare il pulsante __Metrics__ (Metriche) in alto a sinistra della visualizzazione griglia della scheda. Questa visualizzazione mostra la distribuzione e altre statistiche aggregate dei dati campionati.

![Immagine della visualizzazione metriche](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> Per configurare la visibilità delle statistiche, usare l'elenco a discesa **Choose Metric** (Scegli metrica). Selezionare e deselezionare le metriche per modificare la visualizzazione griglia.

Per tornare alla __visualizzazione dati__ fare clic su __Dati__ nell'angolo in alto a sinistra della pagina.

## <a name="add-data-source-to-data-preparation-package"></a>Aggiungere l'origine dati al pacchetto di preparazione dei dati

1. Fare clic su __Prepare__ (Prepara) per iniziare a preparare i dati. 

2. Quando richiesto, immettere un nome per il pacchetto di preparazione dei dati, ad esempio `BikeShare Data Prep`. 

3. Selezionare __OK__ per continuare.

   ![Immagine della finestra di dialogo di preparazione dei dati](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. Un nuovo pacchetto denominato **BikeShare Data Prep** comparirà nella sezione __Data Preparation__ (Preparazione dei dati) della scheda __Data__ (Dati). 

   Per visualizzare il pacchetto, selezionare questa voce. 

5. Selezionare il pulsante **>>** per espandere la visualizzazione dei __flussi di dati__ contenuti nel pacchetto. In questo esempio __BostonWeather__ è l'unico flusso di dati.

   > [!IMPORTANT]
   > Un pacchetto può contenere più flussi di dati.

   ![Immagine dei flussi di dati contenuti nel pacchetto](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Filtrare i dati in base al valore
1. Per filtrare i dati, fare clic con il pulsante destro del mouse su una cella con un determinato valore, selezionare __Filter__ (Filtra) e quindi il tipo di filtro.

2. Per questa esercitazione, selezionare una cella contenente il valore `FM-15` e quindi impostare il filtro su **Equals** (È uguale a).  I dati sono ora filtrati in modo da restituire solo le righe in cui __REPORTTYPE__ è `FM-15`.

   ![Immagine della finestra di dialogo di filtro](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15 è un tipo di report METAR (Meteorological Terminal Aviation Routine Weather Report). I report FM-15 sono empiricamente definiti i più completi, con pochi dati mancanti.

## <a name="remove-a-column"></a>Rimuovere una colonna

La colonna __REPORTTYPE__ non è più necessaria. Fare clic con il pulsante destro del mouse sull'intestazione di colonna e selezionare **Remove Column** (Rimuovi colonna).

   ![Immagine dell'opzione Remove Column (Rimuovi colonna)](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Modificare i tipi di dati e rimuovere gli errori
1. Premendo __CTRL, comando ⌘ su Mac,__ mentre si selezionano le intestazioni di colonna, è possibile selezionare più colonne contemporaneamente. Usare questa opzione per selezionare le intestazioni di colonna seguenti:
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

2. **Fare clic con il pulsante destro del mouse** su una delle intestazioni di colonna selezionate e scegliere **Convert Field Type to Numeric** (Converti tipo di campo in numerico). Il tipo di dati per le colonne verrà convertito in numerico.

   ![Conversione di più colonne in numeriche](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. Filtrare i valori di errore. Alcune colonne hanno problemi di conversione del tipo di dati. Questo problema è indicato in rosso nella __barra della qualità dei dati__ per la colonna.

   Per rimuovere le righe che contengono errori, fare clic con il pulsante destro del mouse sull'intestazione di colonna **HOURLYDRYBULBTEMPF**. Selezionare **Filter Column** (Filtra colonna). Usare il valore predefinito per **I Want To** (Voglio) **Keep Rows** (Mantenere le righe). Modificare l'elenco a discesa **Conditions** (Condizioni) in modo da selezionare **is not error** (non è errore). Selezionare **OK** per applicare il filtro.

4. Per eliminare le righe di errore rimanenti nelle altre colonne, ripetere questo processo di filtro per le colonne **HOURLYRelativeHumidity** e **HOURLYWindSpeed**.

## <a name="use-by-example-transformations"></a>Usare le trasformazioni basate su esempi

Per usare i dati in una stima per blocchi di due ore, è necessario calcolare le condizioni meteo medie per periodi di due ore. A tale scopo, è possibile eseguire le operazioni seguenti:

* Suddividere la colonna **DATE** in colonne **Date** e **Time** distinte. Vedere la sezione successiva per i passaggi dettagliati.

* Derivare una colonna **Hour_Range** dalla colonna **Time**. Vedere la sezione successiva per i passaggi dettagliati.

* Derivare una colonna **Date\_Hour\_Range** dalle colonne **DATE** e **Hour_Range**. Vedere la sezione successiva per i passaggi dettagliati.

### <a name="split-column-by-example"></a>Suddividere le colonne in base a un esempio

1. Suddividere la colonna **DATE** in colonne di data e ora separate. Fare clic con il pulsante destro del mouse sull'intestazione di colonna **DATE** e selezionare **Split Column by Example** (Suddividi colonna in base all'esempio).

   ![Immagine della voce Split Column by Example (Suddividi colonna in base all'esempio)](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Azure Machine Learning Workbench identifica automaticamente un delimitatore significativo e crea due colonne suddividendo i dati in valori di data e ora. 

3. Selezionare __OK__ per accettare i risultati dell'operazione di suddivisione.

   ![Immagine delle colonne suddivise DATE_1 e DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Derivare le colonne in base a un esempio

1. Per derivare un intervallo di due ore, fare clic con il pulsante destro del mouse sull'intestazione di colonna __DATE\_2__ e selezionare **Derive Column by Example** (Deriva colonna in base all'esempio).

   ![Immagine della voce Derive Column by Example (Deriva colonna in base all'esempio)](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   Verrà aggiunta una nuova colonna vuota con valori Null.

2. Fare clic sulla prima cella vuota della nuova colonna. Fornire un esempio dell'intervallo di tempo desiderato digitando `12AM-2AM` nella nuova colonna e quindi premere INVIO.

   ![Immagine della nuova colonna con il valore 12AM-2AM](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Azure Machine Learning Workbench sintetizza un programma basato sugli esempi forniti e applica lo stesso programma alle righe rimanenti. Tutte le altre righe vengono popolate automaticamente in base all'esempio specificato. Il Workbench analizza anche i dati e tenta di identificare i casi limite. 
  
3. Il testo **Analyzing Data** (Analisi dei dati) sopra la griglia indica il Workbench sta tentando di rilevare i casi limite. Al termine, lo stato cambierà in **Review next suggested row** (Rivedi riga successiva suggerita) o **No suggestions** (Nessun suggerimento). In questo esempio viene restituito **Review next suggested row** (Rivedi riga successiva suggerita).

4. Per rivedere le modifiche suggerite, selezionare **Review next suggested row** (Rivedi riga successiva suggerita). La cella che è necessario rivedere e correggere (se necessario) viene evidenziata sullo schermo.

   ![Immagine della riga da rivedere](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Selezionare __OK__ per accettare la trasformazione.
 
5. Si torna alla visualizzazione griglia dei dati per __BostonWeather__. A questo punto, la griglia include le tre colonne aggiunte in precedenza.

   ![Immagine della visualizzazione griglia con le righe aggiunte](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   >  Tutte le modifiche apportate vengono conservate nel riquadro **Steps** (Passaggi). Andare al passaggio creato nel riquadro **Steps** (Passaggi), fare clic sulla freccia GIÙ e selezionare **Edit** (Modifica). Verrà visualizzata la finestra avanzata per **Derive Column by Example** (Deriva colonna in base all'esempio), in cui vengono conservati tutti gli esempi. È anche possibile aggiungere esempi manualmente facendo doppio clic su una riga nella griglia seguente. Selezionare **Cancel** (Annulla) per tornare alla griglia principale senza applicare le modifiche. È inoltre possibile accedere a questa visualizzazione selezionando **Advanced Mode** (Modalità avanzata) mentre si esegue una trasformazione **Derive Column by Example** (Deriva colonna in base all'esempio).

6. Per rinominare la colonna, fare doppio clic sull'intestazione di colonna e digitare **Hour Range**. Premere **INVIO** per salvare la modifica.

   ![Ridenominazione di una colonna](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. Per derivare l'intervallo di data e ora, selezionare contemporaneamente le colonne **Date\_1** e **Hour Range**, fare clic con il pulsante destro del mouse e quindi selezionare **Derive column by example** (Deriva colonna in base all'esempio).

   ![Derivazione di una colonna in base a un esempio](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Digitare `Jan 01, 2015 12AM-2AM` come esempio per la prima riga e premere **INVIO**.

   Il Workbench determina la trasformazione in base all'esempio fornito. In questo esempio il risultato è che il formato della data viene modificato e concatenato con l'intervallo di due ore.

   ![Immagine dell'esempio Jan 01, 2015 12AM-2AM](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)


8. Attendere che lo stato cambi da **Analyzing Data** (Analisi dei dati) a **Review next suggested row** (Rivedi riga successiva suggerita). Ciò può richiedere alcuni secondi. Selezionare il collegamento dello stato per passare alla riga suggerita. 

   ![Immagine della riga suggerita da rivedere](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   La riga ha un valore Null dal momento che il valore della data di origine potrebbe essere gg/mm/aaaa o mm/gg/aaaa. Digitare il valore corretto `Jan 13, 2015 2AM-4AM` e premere **INVIO**. Il Workbench usa i due esempi per migliorare la derivazione per le righe rimanenti.

   ![Immagine dei dati formattati correttamente](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. Selezionare **OK** per accettare la trasformazione.

   ![Immagine della griglia di trasformazione completata](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > È possibile usare la modalità avanzata di **Derive column by example** (Deriva colonna in base all'esempio) per questo passaggio facendo clic sulla freccia GIÙ nel riquadro **Steps** (Passaggi). Nella griglia dei dati sono presenti caselle di controllo accanto ai nomi delle colonne **DATE\_1** e **Hour Range**. Deselezionare la casella di controllo accanto alla colonna **Hour Range** per vedere come cambia l'output. In assenza della colonna **Hour Range** come input, **12AM-2AM** è considerato una costante e viene aggiunto ai valori derivati. Selezionare **Cancel** (Annulla) per tornare alla griglia principale senza applicare le modifiche.

10. Per rinominare la colonna, fare doppio clic sull'intestazione. Modificare il nome in **Date Hour Range** e quindi premere **INVIO**.

11. Selezionare contemporaneamente le colonne **DATE**, **DATE\_1**, **DATE\_2** e **Hour Range**. Fare clic con il pulsante destro del mouse e quindi selezionare **Remove column** (Rimuovi colonna).

## <a name="summarize-data-mean"></a>Riepilogare i dati (media)

Il passaggio successivo consiste nel riepilogare le condizioni meteo tramite la media dei valori, raggruppati per intervallo orario.

1. Selezionare la colonna **Date Hour Range** e quindi scegliere **Summarize** (Riepiloga) dal menu **Transforms** (Trasformazioni).

    ![Menu Transforms (Trasformazioni)](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. Per riepilogare i dati, trascinare le colonne dalla griglia nella parte inferiore della pagina nei riquadri di sinistra e destra in alto. Il riquadro di sinistra contiene il testo **Drag columns here to group data** (Trascinare le colonne per raggruppare i dati). Il riquadro di destra contiene il testo **Drag columns here to summarize data** (Trascinare le colonne per riepilogare i dati). 

    Trascinare la colonna **Date Hour Range** dalla griglia nella parte inferiore nel riquadro di sinistra. Trascinare **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity** e **HOURLYWindSpeed** nel riquadro di destra. 

    Nel riquadro di destra selezionare **Mean** (Media) come misura per **Aggregate** (Aggregazione) per ogni colonna. Fare clic su **OK** per completare il riepilogo.

   ![Immagine della schermata dei dati riepilogati](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-using-script"></a>Trasformare un flusso di dati tramite script

La modifica dei dati nelle colonne numeriche in un intervallo 0-1 consente ad alcuni modelli di convergere rapidamente. Attualmente non è disponibile alcuna trasformazione predefinita per eseguire in modo generico questa trasformazione, ma può essere usato uno script Python.

1. Dal menu **Transform** (Trasforma) scegliere **Transform Dataflow** (Trasforma flusso di dati).

2. Immettere il codice seguente nella casella di testo visualizzata. Se sono stati usati i nomi di colonna, il codice dovrebbe funzionare senza alcuna modifica. Si sta scrivendo una logica di normalizzazione min-max semplice in Python.

    > [!WARNING]
    > Lo script prevede l'uso dei nomi di colonna usati in precedenza in questa esercitazione. Se si usano nomi di colonna diversi, è necessario modificare i nomi nello script.

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > Lo script Python deve restituire `df` alla fine. Questo valore viene usato per popolare la griglia.
    
    ![Finestra di dialogo Transform Dataflow (Script) (Trasforma flusso di dati - Script)](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. Selezionare __OK__ per usare lo script. Le colonne numeriche nella griglia contengono ora valori nell'intervallo 0-1.

    ![Griglia contenente valori compresi tra 0 e 1](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

La preparazione dei dati meteo è conclusa. Ora verranno preparati i dati sulle tratte.

## <a name="load-trip-data"></a>Caricare i dati sulle tratte

1. Per importare il file `201701-hubway-tripdata.csv`, fare riferimento ai passaggi della sezione [Creare una nuova origine dati](#newdatasource). Usare le opzioni seguenti durante il processo di importazione:

    * __Sampling scheme__ (Schema di campionamento): schema di campionamento **Full File** (File completo), rendere il campione attivo e 
    * __Data Type__ (Tipo di dati): accettare i valori predefiniti.

2. Dopo avere importato i dati, selezionare il pulsante __Prepare__ (Prepara) per avviare la preparazione dei dati. Selezionare il pacchetto **BikeShare Data Prep.dprep** esistente e quindi scegliere __OK__.

    Questo processo aggiunge un **flusso di dati** al file di **preparazione dei dati** esistente anziché crearne uno nuovo.

    ![Immagine della selezione del pacchetto esistente](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. Dopo che la griglia è stata caricata, espandere __DATAFLOWS__. Sono ora disponibili due flussi di dati: **BostonWeather** e **201701-hubway-tripdata**. Selezionare la voce **201701-hubway-tripdata**.

    ![Immagine della voce 201701-hubway-tripdata](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-map-inspector"></a>Usare il controllo mappa

Per la preparazione dei dati sono disponibili numerose visualizzazioni utili chiamate **controlli** per i dati di tipo stringa, numerici e geografici che consentono di comprendere meglio i dati e di individuare gli outlier. Fare riferimento alla procedura seguente per usare il controllo mappa:

1. Selezionare contemporaneamente le colonne **start station latitude** e **start station longitude**. Fare clic con il pulsante destro del mouse su una delle colonne e quindi selezionare **Map** (Mappa).

    > [!TIP]
    > Per abilitare la selezione multipla, tenere premuto __CTRL, il comando ⌘ su Mac,__ e selezionare l'intestazione di ogni colonna.

    ![Immagine della visualizzazione mappa](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. Per ingrandire la visualizzazione mappa, selezionare l'icona di **ingrandimento**. Per adattare la mappa alla finestra, selezionare l'icona **E** nell'angolo in alto a sinistra della visualizzazione.

    ![Immagine ingrandita](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. Fare clic sul pulsante di **riduzione a icona** per tornare alla visualizzazione griglia.

## <a name="use-column-statistics-inspector"></a>Usare il controllo statistiche di colonna

Per usare il controllo statistiche di colonna, fare clic con il pulsante destro del mouse sulla colonna __tripduration__ e selezionare __Column Statistics__ (Statistiche di colonna).

![Voce Column Statistics (Statistiche di colonna)](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Questo processo aggiunge una nuova visualizzazione denominata __tripduration Statistics__ (Statistiche di tripduration) nel riquadro __INSPECTORS__ (CONTROLLI).

![Immagine del controllo statistiche di tripduration](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> Il valore massimo della durata della tratta è di **961.814** minuti, ovvero circa due anni. Sembra che ci siano alcuni outlier nel set di dati.

## <a name="use-histogram-inspector"></a>Usare il controllo istogramma

Per tentare di identificare gli outlier, fare clic con il pulsante destro del mouse sulla colonna __tripduration__ e selezionare __Histogram__ (Istogramma).

![Controllo istogramma](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

L'istogramma non è utile, dal momento che gli outlier causano un'alterazione del grafico.

## <a name="add-column-using-script"></a>Aggiungere una colonna tramite script

1. Fare clic con il pulsante destro del mouse sulla colonna **tripduration** e selezionare **Add Column (Script)** (Aggiungi colonna - Script).

    ![Immagine del menu Add Column (Script) (Aggiungi colonna - Script)](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. Nella finestra di dialogo __Add Column (Script)__ (Aggiungi colonna - Script) usare i valori seguenti:

    * __New Column Name__ (Nome nuova colonna): logtripduration
    * __Insert this New Column After__ (Inserire la nuova colonna dopo): tripduration
    * __New Column Code__ (Codice nuova colonna): `math.log(row.tripduration)`
    * __Code Block Type__ (Tipo blocco di codice): Expression (Espressione)

   ![Finestra di dialogo Add Column (Script) (Aggiungi colonna - Script)](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. Selezionare __OK__ per aggiungere la colonna **logtripduration**.

4. Fare clic con il pulsante destro del mouse sulla colonna e selezionare **Histogram** (Istogramma).

    ![Istogramma della colonna logtripduration](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

  Visivamente questo istogramma sembra una distribuzione normale con una coda anomala.

## <a name="use-advanced-filter"></a>Usare il filtro avanzato

L'applicazione di un filtro ai dati aggiorna i controlli in base alla nuova distribuzione. Fare clic con il pulsante destro del mouse sulla colonna **logtripduration** e selezionare **Filter Column** (Filtra colonna). Nella finestra di dialogo __Edit__ (Modifica) usare i valori seguenti:

* __Filter this Number Column__ (Filtrare la colonna numero): logtripduration
* __I Want To__ (Voglio): Keep Rows (Mantenere le righe)
* __When__ (Quando): Any of the Conditions below are True (logical OR) (Una delle condizioni seguenti è vera - OR logico)
* __If this Column__ (Se la colonna): less than (minore di)
* __The Value__ (Valore): 9

![Opzioni di filtro](media/tutorial-bikeshare-dataprep/loftripfilter.png)

Selezionare __OK__ per applicare il filtro.

![Istogrammi aggiornati dopo l'applicazione del filtro](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>Effetto alone

1. Ingrandire l'istogramma di **logtripduration**. Si vede un istogramma blu sovrapposto a un istogramma grigio. Questa visualizzazione viene chiamata **effetto alone**:

    * L'**istogramma grigio** rappresenta la distribuzione prima dell'operazione (in questo caso, l'operazione di filtro).
    * L'**istogramma blu** rappresenta l'istogramma dopo l'operazione. 

   L'effetto alone risulta utile per visualizzare l'effetto di un'operazione sui dati.

   ![Immagine dell'effetto alone](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > Si noti che l'istogramma blu sembra più corto rispetto a quello precedente. Ciò è dovuto al rebucket automatico dei dati nel nuovo intervallo.

2. Per rimuovere l'alone, selezionare __Edit__ (Modifica) e deselezionare __Show halo__ (Mostra alone).

    ![Opzioni per l'istogramma](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. Selezionare **OK** per disabilitare l'effetto alone e quindi ridurre al minimo l'istogramma.

### <a name="remove-columns"></a>Rimuovere le colonne

Nei dati sulle tratte ogni riga rappresenta un evento di prelievo di una bicicletta. Per questa esercitazione, sono necessarie solo le colonne **starttime** e **start station id**. Rimuovere le altre colonne selezionando contemporaneamente queste due colonne, fare clic con il pulsante destro del mouse sull'intestazione di colonna e quindi selezionare **Keep Column** (Mantieni colonna). Le altre colonne verranno rimosse.

![Immagine dell'opzione Keep Column (Mantieni colonna)](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>Riepilogare i dati (conteggio)

Per riepilogare la richiesta di biciclette per un periodo di due ore, usare colonne derivate.

1. Fare clic con il pulsante destro del mouse sulla colonna **starttime** e selezionare **Derive Column by Example** (Deriva colonna in base all'esempio).

    ![Immagine dell'opzione di derivazione in base all'esempio](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. Per questo esempio, immettere un valore di `Jan 01, 2017 12AM-2AM` per la prima riga.

    > [!IMPORTANT]
    > Nell'esempio precedente di derivazione delle colonne erano necessari più passaggi per derivare una colonna contenente il periodo di data e ora. In questo esempio si noterà che questa operazione può essere eseguita in un unico passaggio fornendo un esempio dell'output finale.

    > [!NOTE]
    > È possibile fornire un esempio in base a una qualsiasi delle righe. Per questo esempio, il valore di `Jan 01, 2017 12AM-2AM` è valido per la prima riga di dati.

    ![Immagine dei dati di esempio](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)
   
3. Attendere che l'applicazione calcoli i valori in base a tutte le righe. Ciò può richiedere alcuni secondi. Al termine dell'analisi, usare il collegamento __Review next suggested row__ (Rivedi riga successiva suggerita) per esaminare i dati.

   ![Immagine dell'analisi completata con il collegamento per la revisione](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Verificare che i valori calcolati siano corretti. In caso contrario, aggiornare il valore con il valore previsto e premere INVIO. Attendere quindi il completamento dell'analisi. Completare il processo previsto da **Review next suggested row** (Rivedi riga successiva suggerita) fino a visualizzare **No suggestions** (Nessun suggerimento). Dopo aver visualizzato **No suggestions** (Nessun suggerimento), l'applicazione ha esaminato i casi limite ed è soddisfatta del programma sintetizzato. È consigliabile eseguire un esame visivo dei dati trasformati prima di accettare la trasformazione. 

4. Selezionare **OK** per accettare la trasformazione. Rinominare la colonna appena creata in **Date Hour Range**.

    ![Immagine della colonna rinominata](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. Fare clic con il pulsante destro del mouse sull'intestazione di colonna**starttime** e selezionare **Remove column** (Rimuovi colonna).

6. Per riepilogare i dati, selezionare __Summarize__ (Riepiloga) dal menu __Transform__ (Trasforma). Per creare la trasformazione, eseguire le operazioni seguenti:

    * Trascinare __Date Hour Range__ e __start station id__ nel riquadro di sinistra (di raggruppamento).
    * Trascinare __start station id__ nel riquadro di destra (dati di riepilogo).

   ![Immagine delle opzioni di riepilogo](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. Selezionare **OK** per accettare i risultati del riepilogo.

## <a name="join-dataflows"></a>Congiungere flussi di dati

Per creare un join dei dati meteo e di quelli sulle tratte, usare la procedura seguente:

1. Scegliere __Join__ (Congiungi) dal menu __Transforms__ (Trasformazioni).

2. __Tables__ (Tabelle): selezionare **BostonWeather** come flusso di dati a sinistra e **201701-hubway-tripdata** come flusso di dati a destra. Per continuare, selezionare **Next** (Avanti).

    ![Immagine delle selezioni in Tables (Tabelle)](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __Key Columns__ (Colonne chiave): selezionare la colonna **Date Hour Range** in entrambe le tabelle e quindi scegliere __Next__ (Avanti).

    ![Immagine del join sulle colonne chiave](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __Join Type__ (Tipo di join): selezionare __Matching rows__ (Righe corrispondenti) come tipo di join e quindi scegliere __Finish__ (Fine).

    ![Tipo di join basato su righe corrispondenti](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Questo processo crea un nuovo flusso di dati denominato __Join Result__.

## <a name="create-additional-features"></a>Creare funzionalità aggiuntive

1. Per creare una colonna contenente il giorno della settimana, fare clic con il pulsante destro del mouse sulla colonna **Date Hour Range** e selezionare **Derive column by Example** (Deriva colonna in base all'esempio). Usare il valore __Sun__ per una data che si è verificata di domenica. Ad esempio, **Jan 01, 2017 12AM-2AM**. Premere **INVIO** e quindi scegliere **OK**. Rinominare la colonna __Weekday__.

    ![Immagine della creazione di una nuova colonna contenente il giorno della settimana](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. Per creare una colonna contenente l'intervallo di tempo per una riga, fare clic con il pulsante destro del mouse sulla colonna **Date Hour Range** e selezionare **Derive column by example** (Deriva colonna in base all'esempio). Usare il valore **12AM-2AM** per la riga contenente **Jan 01, 2017 12AM-2AM**. Premere **INVIO** e quindi scegliere **OK**. Rinominare la colonna **Period**.

    ![Immagine della colonna dell'intervallo di tempo](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. Per rimuovere le colonne **Date Hour Range** (Intervallo data e ora) e **rDate Hour Range** (Intervallo oraria rDate), premere **CTRL, comando ⌘ su Mac,** e selezionare ogni intestazione di colonna. Fare clic con il pulsante destro del mouse e quindi selezionare **Remove Column** (Rimuovi colonna).

## <a name="read-data-from-python"></a>Leggere i dati da Python

È possibile eseguire un pacchetto di preparazione dei dati da Python o PySpark e recuperare il risultato come **frame di dati**.

Per generare uno script Python di esempio, fare clic con il pulsante destro del mouse su __BikeShare Data Prep__ e selezionare __Generate Data Access Code File__ (Genera file di codice di accesso ai dati). Il file Python di esempio viene creato nella **cartella del progetto** e viene anche caricato in una scheda all'interno del Workbench. Lo script Python seguente è un esempio del codice generato:

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

Per questa esercitazione, il nome del file è `BikeShare Data Prep.py`. Questo file verrà usato più avanti nell'esercitazione.

## <a name="save-test-data-as-a-csv-file"></a>Salvare i dati di test come file con estensione csv

Per salvare il flusso di dati **Join Result** in un file con estensione csv, è necessario modificare lo script `BikeShare Data Prep.py`. Aggiornare lo script Python usando il codice seguente:

```python
from azureml.dataprep.package import run

# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTest.csv
df.to_csv('Your Test Data File Path here')
```

Scegliere **Run** (Esegui) nella parte superiore della schermata. Lo script viene inviato come **processo** nel computer locale. Dopo che lo stato del processo viene modificato in __Completed__ (Completato), il file è stato scritto nel percorso specificato.

## <a name="substitute-data-sources"></a>Sostituire le origini dati

Nei passaggi precedenti sono state usate le origini dati `201701-hubway-tripdata.csv` e `BostonWeather.csv` per preparare i dati di test. Per usare il pacchetto con gli altri file di dati sulle tratte, seguire questa procedura:

1. Creare una nuova **origine dati** usando i passaggi forniti in precedenza, con le modifiche seguenti al processo:

    * __Selezione dei file__: quando si seleziona un file, selezionare contemporaneamente i sei file con estensione csv rimanenti con i dati sulle tratte.

        ![Caricare i sei file rimanenti](media/tutorial-bikeshare-dataprep/selectsixfiles.png)

        > [!NOTE]
        > La voce __+ 5__ indica che ci sono altri cinque file oltre a quello elencato.

    * __Dettagli dei file__: impostare __Promote Headers Mode__ (Modalità intestazioni alzate di livello) su **All Files Have The Same Headers** (Tutti i file hanno le stesse intestazioni). Questo valore indica che ogni file contiene la stessa intestazione.

        ![Selezione dei dettagli dei file](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Salvare il nome dell'origine dati, dal momento che verrà usata nei passaggi successivi.

2. Selezionare l'icona della cartella per visualizzare i file nel progetto. Espandere la directory __aml\_config__ e quindi selezionare il file `local.runconfig`.

    ![Immagine della posizione di local.runconfig](media/tutorial-bikeshare-dataprep/localrunconfig.png) 

3. Aggiungere le righe seguenti alla fine del file `local.runconfig` e quindi selezionare l'icona del disco per salvare il file.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Questa modifica sostituisce l'origine dati originale con quella contenente i sei file di dati sulle tratte.

## <a name="save-training-data-as-a-csv-file"></a>Salvare i dati di training in un file con estensione csv

Passare al file Python `BikeShare Data Prep.py` modificato in precedenza e specificare un percorso di file diverso per salvare i dati di training.

```python
from azureml.dataprep.package import run
# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTrain.csv
df.to_csv('Your Training Data File Path here')
```

Per inviare un nuovo processo, usare l'icona **Run** (Esegui) nella parte superiore della pagina. Verrà inviato un **processo** con la nuova configurazione. L'output di questo processo è costituito dai dati di training. Questi dati vengono creati usando gli stessi passaggi per la preparazione dei dati creati in precedenza. Il completamento del processo potrebbe richiedere alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi
L'esercitazione di preparazione dei dati sul bike sharing è conclusa. In questa esercitazione sono stati usati i servizi di Azure Machine Learning (anteprima) per imparare a:
> [!div class="checklist"]
> * Preparare i dati in modo interattivo con lo strumento di preparazione dei dati di Azure Machine Learning
> * Importare, trasformare e creare un set di dati di test
> * Generare un pacchetto di preparazione dei dati
> * Eseguire il pacchetto di preparazione dei dati usando Python
> * Generare un set di dati di training riusando il pacchetto di preparazione dei dati per i file di input aggiuntivi

Altre informazioni sulla preparazione dei dati:
> [!div class="nextstepaction"]
> [Data Preparation User Guide](data-prep-user-guide.md) (Guida dell'utente per la preparazione dati)
