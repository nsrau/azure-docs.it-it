---
title: Visualizzare le anomalie con rilevamento batch e Power BI
titlesuffix: Azure Cognitive Services
description: Usare l'API rilevatore di anomalie e Power BI per visualizzare le anomalie in tutta la serie temporale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: de353c946da46b2bbe4e755e21e355f6bd18725b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827146"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Esercitazione: Visualizzare le anomalie con rilevamento batch e Power BI

Usare questa esercitazione per trovare le anomalie all'interno di un set di dati della serie temporale come batch. Usando Power BI desktop, si accetta un file di Excel, preparare i dati per l'API rilevatore di anomalie e visualizzare statistiche anomalie in corso.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare Power BI Desktop per importare e trasformare un set di dati di serie temporale
> * Integrazione di Power BI Desktop con l'API rilevatore di anomalie per il rilevamento anomalie di batch
> * Visualizzare le anomalie all'interno dei dati, inclusi i valori previsti ed individuati e i limiti di rilevamento delle anomalie.

## <a name="prerequisites"></a>Prerequisiti

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), disponibile gratuitamente.
* Fa riferimento un file (con estensione xlsx) contenente ora serie i dati di excel. I dati di esempio per questa Guida introduttiva sono reperibile in [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Caricare e formattare i dati delle serie temporali

Per iniziare, aprire Power BI Desktop e caricare i dati delle serie temporali che è stato scaricato dai prerequisiti. Questo file di excel contiene una serie di coppie di timestamp e il valore Coordinated Universal Time (UTC).  

> [!NOTE]
> Power BI è possibile usare i dati da un'ampia gamma di origini, ad esempio file con estensione csv, SQL database, archiviazione blob di Azure e altro ancora.  

Nella finestra principale di Power BI Desktop, selezionare la **Home** della barra multifunzione. Nel **dati esterni** gruppo della barra multifunzione, aprire il **recupera dati** dal menu a discesa e fare clic su **Excel**.

![Un'immagine del pulsante "Recupera dati" in Power BI](../media/tutorials/power-bi-get-data-button.png)

Dopo che viene visualizzata la finestra di dialogo, passare alla cartella in cui è stato scaricato il file con estensione xlsx di esempio e selezionarlo. Dopo il **Navigator** finestra di dialogo viene visualizzata, fare clic su **Sheet1**e quindi **Edit**.

![Un'immagine della schermata "Strumento di navigazione" origine dati in Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI converte il timestamp nella prima colonna a un `Date/Time` tipo di dati. Questi timestamp devono essere convertiti in testo per poter essere inviati per l'API rilevatore di anomalie. Se non viene automaticamente aperto l'editor di Power Query, fare clic su **modifica query** della scheda home. 

Scegliere il **trasformare** della barra multifunzione nell'Editor di Power Query. Nel **qualsiasi colonna** gruppo, aprire il **tipo di dati:** dal menu a discesa e selezionare **testo**.

![Un'immagine della schermata "Strumento di navigazione" origine dati in Power BI](../media/tutorials/data-type-drop-down.png)

Quando si riceve una notifica sulla modifica del tipo di colonna, fare clic su **Sostituisci corrente**. In un secondo momento, fare clic su **Chiudi e applica** oppure **Apply** nel **Home** della barra multifunzione. 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Creare una funzione per inviare i dati e formattare la risposta

Per formattare e inviare il file di dati per l'API rilevatore di anomalie, è possibile richiamare una query sulla tabella creata in precedenza. Nell'Editor di Power Query dal **Home** della barra multifunzione, aprire il **nuova origine** dal menu a discesa e fare clic su **Query vuota**.

Verificare che sia selezionata la nuova query, quindi fare clic su **Editor avanzato**. 

![Un'immagine del pulsante "Editor avanzato" in Power BI](../media/tutorials/advanced-editor-screen.png)

All'interno dell'Editor avanzato, usare il seguente frammento di Power Query M per estrarre le colonne dalla tabella e inviarlo all'API. La query verrà successivamente, creare una tabella utilizzando la risposta JSON e restituirlo. Sostituire il `apiKey` variabile con la chiave API rilevatore di anomalie valida, e `endpoint` associato all'endpoint. Dopo aver immesso la query nell'Editor avanzato, fare clic su **Fatto**.

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

Richiamare la query sul foglio dati selezionando `Sheet1` seguito **Immetti parametro**, fare clic su **Invoke**. 

![Un'immagine del pulsante "Editor avanzato"](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>L'autenticazione e riservatezza di origine dei dati

> [!NOTE]
> Tenere presente i criteri dell'organizzazione per l'accesso e privacy dei dati. Visualizzare [livelli di privacy di Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-privacy-levels) per altre informazioni.

È possibile ricevere un messaggio di avviso quando si tenta di eseguire la query perché usa un'origine dati esterna. 

![Un'immagine che mostra un avviso creato da Power BI](../media/tutorials/blocked-function.png)

Per risolvere questo problema, fare clic su **File**, e **opzioni e impostazioni**. Quindi fare clic su **opzioni**. Di seguito **File corrente**, selezionare **Privacy**, e **ignora i livelli di Privacy e potenziale miglioramento delle prestazioni**. 

Inoltre, è possibile ricevere un messaggio chiederà di specificare come si desidera connettersi all'API.

![Un'immagine che mostra una richiesta per specificare le credenziali di accesso](../media/tutorials/edit-credentials-message.png)

Per risolvere questo problema, fare clic su **Modifica credenziali** nel messaggio. Dopo che viene visualizzata la finestra di dialogo, selezionare **Anonymous** per connettersi all'API in modo anonimo. Fare clic su **Connetti**. 

In un secondo momento, fare clic su **Chiudi e applica** nel **Home** della barra multifunzione per applicare le modifiche.

## <a name="visualize-the-anomaly-detector-api-response"></a>Visualizzare la risposta dell'API rilevatore di anomalie

Nella schermata principale di Power BI, iniziare a usare le query create in precedenza per visualizzare i dati. Selezionare innanzitutto **grafico a linee** nelle **visualizzazioni**. Aggiungere quindi il timestamp dalla funzione di richiamata per il grafico a linee **asse**. Fare clic su di esso e selezionare **Timestamp**. 

![Pulsante destro del mouse il valore del Timestamp](../media/tutorials/timestamp-right-click.png)

Aggiungere i campi seguenti dal **funzione di richiamata** al grafico **valori** campo. Utilizzare la seguente schermata per la creazione del grafico.

    * Value
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![Un'immagine della schermata nuova misura rapida](../media/tutorials/chart-settings.png)

Dopo aver aggiunto i campi, fare clic sul grafico e ridimensionarlo in modo da mostrare tutti i punti dati. Il grafico avrà un aspetto simile alla schermata seguente:

![Un'immagine della schermata nuova misura rapida](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Visualizzare i punti dati delle anomalie

Sul lato destro della finestra di Power BI, sotto il **campi** pulsante destro del mouse sul riquadro **valore** sotto il **query della funzione di richiamata**, fare clic su **nuovo quick misura**.

![Un'immagine della schermata nuova misura rapida](../media/tutorials/new-quick-measure.png)

Nella schermata che viene visualizzato, selezionare **filtrato valore** come tipo di calcolo. Impostare **valore di Base** a `Sum of Value`. Quindi trascinare `IsAnomaly` dal **funzione di richiamata** campi **filtro**. Selezionare `True` dal **filtro** dal menu a discesa.

![Un'immagine della schermata nuova misura rapida](../media/tutorials/new-quick-measure-2.png)

Dopo aver fatto clic **accettabile**, si avrà un `Value for True` campo, nella parte inferiore dell'elenco di campi. Pulsante destro del mouse e rinominarlo **anomalie**. Aggiungerlo al grafico **valori**. Quindi selezionare il **formato** sullo strumento, quindi impostare il tipo di asse x su **Categorical**.

![Un'immagine della schermata nuova misura rapida](../media/tutorials/format-x-axis.png)

Applicare i colori per il grafico facendo clic sui **formato** dello strumento e **colori dati**. Il grafico dovrebbe essere simile al seguente:

![Un'immagine della schermata nuova misura rapida](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Rilevamento delle anomalie in streaming con Azure Databricks](anomaly-detection-streaming-databricks.md)
