---
title: Visualizzare le anomalie con il rilevamento in batch e Power BI
titleSuffix: Azure Cognitive Services
description: Usare l'API Rilevamento anomalie e Power BI per visualizzare eventuali anomalie nei dati delle serie temporali.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: 74b51d04f2706d890475c500e1e730cff75397c5
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721494"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Esercitazione: Visualizzare le anomalie con il rilevamento in batch e Power BI

Usare questa esercitazione per trovare anomalie nel set di dati di una serie temporale come batch. Usando Power BI Desktop si prepareranno i dati di un file di Excel per l'API Rilevamento anomalie e si visualizzeranno le anomalie statistiche contenute nei dati.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare Power BI Desktop per importare e trasformare un set di dati di una serie temporale
> * Integrare Power BI Desktop con l'API Rilevamento anomalie per il rilevamento anomalie in batch
> * Visualizzare le anomalie trovate all'interno dei dati, inclusi i valori previsti e quelli rilevati, e i limiti di rilevamento anomalie.

## <a name="prerequisites"></a>Prerequisiti

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), disponibile gratuitamente.
* Un file di Excel (con estensione xlsx) contenente i punti dati delle serie temporali. I dati di esempio per questa guida di avvio rapido sono disponibili in [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Caricare e formattare i dati della serie temporale

Per iniziare, aprire Power BI Desktop e caricare i dati della serie temporale scaricati dai prerequisiti. Questo file di Excel contiene una serie di coppie di valori e timestamp UTC (Coordinated Universal Time).  

> [!NOTE]
> Power BI può usare i dati di un'ampia gamma di origini, ad esempio file CSV, database SQL, archivi BLOB di Azure e altro.  

Nella finestra principale di Power BI Desktop fare clic sulla scheda **Home** sulla barra multifunzione. Nel gruppo **Dati esterni** sulla barra multifunzione aprire il menu a discesa **Recupera dati** e fare clic su **Excel**.

![Immagine del pulsante "Recupera dati" in Power BI](../media/tutorials/power-bi-get-data-button.png)

Nella finestra di dialogo visualizzata passare alla cartella in cui è stato scaricato il file xlsx di esempio e selezionarlo. Nella finestra di dialogo **Strumento di navigazione** fare clic su **Sheet1** e quindi su **Modifica**.

![Immagine della schermata "Strumento di navigazione" dell'origine dati in Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI convertirà i timestamp nella prima colonna in un tipo di dati `Date/Time`. Questi timestamp devono essere convertiti in testo per essere inviati all'API Rilevamento anomalie. Se l'editor di Power Query non si apre automaticamente, fare clic su **Modifica query** nella scheda Home. 

Fare clic sulla scheda **Trasforma** sulla barra multifunzione nell'editor di Power Query. Nel gruppo **Qualsiasi colonna** aprire l'elenco a discesa **Tipo di dati** e selezionare **Testo**.

![Immagine della schermata "Strumento di navigazione" dell'origine dati in Power BI](../media/tutorials/data-type-drop-down.png)

Quando si riceve un avviso sulla modifica del tipo di colonna, fare clic su **Sostituisci corrente**. Quindi fare clic su **Chiudi e applica** o su **Applica** nella scheda **Home** della barra multifunzione. 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Creare una funzione per inviare i dati e formattare la risposta

Per formattare e inviare il file di dati all'API Rilevamento anomalie, è possibile richiamare una query sulla tabella creata in precedenza. Nella scheda **Home** sulla barra multifunzione dell'editor di Power Query n aprire il menu a discesa **Nuova origine** e fare clic su **Query vuota**.

Verificare che la nuova query sia selezionata e quindi fare clic su **Editor avanzato**. 

![Immagine del pulsante "Editor avanzato" in Power BI](../media/tutorials/advanced-editor-screen.png)

In Editor avanzato usare il frammento di Power Query M seguente per estrarre le colonne dalla tabella e inviarle all'API. La query creerà quindi una tabella dalla risposta JSON e la restituirà. Sostituire la variabile `apiKey` con la chiave API Rilevamento anomalie valida e `endpoint` con l'endpoint. Dopo aver immesso la query in Editor avanzato, fare clic su **Fine**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Richiamare la query nel foglio dati selezionando `Sheet1` sotto **Immettere un parametro**, quindi fare clic su **Richiama**. 

![Immagine del pulsante "Editor avanzato"](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Privacy e autenticazione dell'origine dati

> [!NOTE]
> È importante conoscere i criteri dell'organizzazione a livello di privacy e accesso ai dati. Per altre informazioni, vedere [Livelli di privacy di Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-privacy-levels).

Quando si prova a eseguire la query si potrebbe ricevere un messaggio di avviso, poiché utilizza un'origine dati esterna. 

![Immagine che mostra un avviso creato da Power BI](../media/tutorials/blocked-function.png)

Per risolvere il problema, fare clic su **File** e quindi su **Opzioni e impostazioni**. Quindi fare clic su **Opzioni**. In **File corrente** selezionare **Privacy** e **Ignora i livelli di privacy per un potenziale miglioramento delle prestazioni**. 

Si potrebbe anche ricevere un messaggio che chiede di specificare in che modo ci si vuole connettere all'API.

![Immagine che mostra una richiesta di specificare le credenziali di accesso](../media/tutorials/edit-credentials-message.png)

Per risolvere il problema, fare clic su **Modifica credenziali**  nel messaggio. Nella finestra di dialogo visualizzata selezionare **Anonimo** per connettersi alll'API in modalità anonima. Fare clic su **Connetti**. 

Quindi fare clic su **Chiudi e applica** nella scheda **Home** della barra multifunzione per applicare le modifiche.

## <a name="visualize-the-anomaly-detector-api-response"></a>Visualizzare la risposta dell'API Rilevamento anomalie

Nella schermata principale di Power BI iniziare a usare le query create in precedenza per visualizzare i dati. Prima di tutto selezionare **Grafico a linee** in **Visualizzazioni**. Quindi aggiungere il timestamp della funzione richiamata all'**Asse** del grafico a linee. Fare clic su di esso con il pulsante destro del mouse e scegliere **Timestamp**. 

![Fare clic con il pulsante destro del mouse sul valore Timestamp](../media/tutorials/timestamp-right-click.png)

Aggiungere i campi seguenti da **Richiamata funzione**  al campo **Valori** del grafico. Usare lo screenshot seguente come aiuto per creare il grafico.

    * Valore
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![Immagine della nuova schermata di misura rapida](../media/tutorials/chart-settings.png)

Dopo aver aggiunto i campi, fare clic sul grafico e ridimensionarlo per mostrare tutti i punti dati. Il grafico avrà un aspetto simile a quello dello screenshot seguente:

![Immagine della nuova schermata di misura rapida](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Visualizzare i punti dati delle anomalie

Sul lato destro della finestra di Power BI, sotto il riquadro **CAMPI**, fare clic con il pulsante destro del mouse su **Value** sotto la query **Richiamata funzione** e scegliere **Nuova misura rapida** .

![Immagine della nuova schermata di misura rapida](../media/tutorials/new-quick-measure.png)

Nella schermata visualizzata selezionare **Valore filtrato** come calcolo. Impostare **Valore di base** su `Sum of Value`. Quindi trascinare `IsAnomaly` dai campi **Richiamata funzione** al campo **Filtro**. Selezionare `True` dall'elenco a discesa **Filtro**.

![Immagine della nuova schermata di misura rapida](../media/tutorials/new-quick-measure-2.png)

Dopo aver fatto clic su **OK** si vedrà un campo `Value for True` in fondo all'elenco dei campi. Fare clic su di esso con il pulsante destro del mouse e rinominarlo in **Anomalia**. Aggiungerlo all'asse **Valori** del grafico. Quindi selezionare lo strumento **Formato** e impostare il tipo di asse delle X su **Categorie**.

![Immagine della nuova schermata di misura rapida](../media/tutorials/format-x-axis.png)

Applicare i colori al grafico facendo clic sullo strumento **Formato** e su **Colori dati**. Il grafico dovrebbe essere simile al seguente:

![Immagine della nuova schermata di misura rapida](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Rilevamento anomalie nei dati di streaming con Azure Databricks](anomaly-detection-streaming-databricks.md)
