---
title: Eseguire stime con il modello AutoML ONNX in .NET
description: Informazioni su come eseguire stime usando un modello ONNX di AutoML in .NET con ML.NET
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.date: 10/30/2020
ms.topic: conceptual
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.custom: how-to
ms.openlocfilehash: cb4e57cfe8b7494b7d5c38869f83190bff76ef2a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305767"
---
# <a name="make-predictions-with-an-automl-onnx-model-in-net"></a>Eseguire stime con un modello ONNX di AutoML in .NET

In questo articolo si apprenderà come usare un modello ONNX (Open Neural Network Exchange) automatizzato (AutoML) per eseguire stime in un'applicazione console .NET Core C# con ML.NET.

[Ml.NET](/dotnet/machine-learning/) è un framework open source, multipiattaforma e di machine learning per l'ecosistema .NET che consente di eseguire il training e l'utilizzo di modelli di apprendimento automatico personalizzati usando un approccio Code-First in C# o F #, oltre a strumenti di basso livello, come [Generatore di modelli](/dotnet/machine-learning/automate-training-with-model-builder) e l'interfaccia della riga di comando di [ml.NET](/dotnet/machine-learning/automate-training-with-cli). Il Framework è anche estendibile e consente di usare altri Framework di apprendimento automatico diffusi, ad esempio TensorFlow e ONNX.

ONNX è un formato Open Source per i modelli di intelligenza artificiale. ONNX supporta l'interoperabilità tra framework. Ciò significa che è possibile eseguire il training di un modello in uno dei numerosi framework di apprendimento automatico diffusi, ad esempio PyTorch, convertirlo in formato ONNX e utilizzare il modello ONNX in un Framework diverso come ML.NET. Per altre informazioni, vedere il [sito Web ONNX](https://onnx.ai/).

## <a name="prerequisites"></a>Prerequisiti

- [.NET Core SDK 3,1 o versione successiva](https://dotnet.microsoft.com/download)
- Editor di testo o IDE (ad esempio [Visual Studio](https://visualstudio.microsoft.com/vs/) o [Visual Studio Code](https://code.visualstudio.com/Download))
- Modello ONNX. Per informazioni su come eseguire il training di un modello AutoML ONNX, vedere la pagina relativa al [notebook di classificazione di Bank marketing](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb).
- [Netron](https://github.com/lutzroeder/netron) (facoltativo)

## <a name="create-a-c-console-application"></a>Creare un'applicazione console C#

In questo esempio si usa il interfaccia della riga di comando di .NET Core per compilare l'applicazione, ma è possibile eseguire le stesse attività usando Visual Studio. Altre informazioni sul [interfaccia della riga di comando di .NET Core](/dotnet/core/tools/).

1. Aprire un terminale e creare una nuova applicazione console .NET Core C#. In questo esempio, il nome dell'applicazione è `AutoMLONNXConsoleApp` . Una directory viene creata con lo stesso nome con il contenuto dell'applicazione.

    ```dotnetcli
    dotnet new console -o AutoMLONNXConsoleApp
    ```

1. Nel terminale passare alla directory *AutoMLONNXConsoleApp*

    ```bash
    cd AutoMLONNXConsoleApp
    ```

## <a name="add-software-packages"></a>Aggiungi pacchetti software

1. Installare i pacchetti NuGet **Microsoft.ml** , **Microsoft. ml. OnnxRuntime** e **Microsoft. ml. OnnxTransformer** usando il interfaccia della riga di comando di .NET Core.

    ```dotnetcli
    dotnet add package Microsoft.ML
    dotnet add package Microsoft.ML.OnnxRuntime
    dotnet add package Microsoft.ML.OnnxTransformer
    ```

    Questi pacchetti contengono le dipendenze necessarie per usare un modello ONNX in un'applicazione .NET. ML.NET fornisce un'API che usa il [runtime di ONNX](https://github.com/Microsoft/onnxruntime) per le stime.

1. Aprire il file *Program.cs* e aggiungere le `using` istruzioni seguenti nella parte superiore per fare riferimento ai pacchetti appropriati.

    ```csharp
    using System.Linq;
    using Microsoft.ML;
    using Microsoft.ML.Data;
    using Microsoft.ML.Transforms.Onnx;
    ```

## <a name="add-a-reference-to-the-onnx-model"></a>Aggiungere un riferimento al modello ONNX

Un modo per l'applicazione console di accedere al modello ONNX consiste nell'aggiungerlo alla directory di output di compilazione.  Per ulteriori informazioni sugli elementi comuni di MSBuild, vedere la [Guida di MSBuild](/visualstudio/msbuild/common-msbuild-project-items).

Aggiungere un riferimento al file del modello ONNX nell'applicazione

1. Copiare il modello ONNX nella directory radice *AutoMLONNXConsoleApp* dell'applicazione.
1. Aprire il file *AutoMLONNXConsoleApp. csproj* e aggiungere il contenuto seguente all'interno del `Project` nodo.

    ```xml
    <ItemGroup>
        <None Include="automl-model.onnx">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    In questo caso, il nome del file di modello ONNX è *automl-Model. ONNX*.

1. Aprire il file *Program.cs* e aggiungere la riga seguente all'interno della `Program` classe.

    ```csharp
    static string ONNX_MODEL_PATH = "automl-model.onnx";
    ```

## <a name="initialize-mlcontext"></a>Inizializzare MLContext

All'interno del `Main` metodo della `Program` classe, creare una nuova istanza di [`MLContext`](xref:Microsoft.ML.MLContext) .

```csharp
MLContext mlContext = new MLContext();
```

La [`MLContext`](xref:Microsoft.ML.MLContext) classe è un punto di partenza per tutte le operazioni ml.NET e l'inizializzazione `mlContext` Crea un nuovo ambiente ml.NET che può essere condiviso attraverso il ciclo di vita del modello. Si tratta di una situazione simile, concettualmente, a DbContext in Entity Framework.

## <a name="define-the-model-data-schema"></a>Definire lo schema di dati del modello

Il modello prevede che i dati di input e di output abbiano un formato specifico. ML.NET consente di definire il formato dei dati tramite le classi. In alcuni casi è possibile che il formato sia già noto. Nei casi in cui non si conosce il formato dei dati, è possibile usare strumenti come Netron per esaminare il modello ONNX.

Il modello usato in questo esempio usa i dati del set di dati di NYC TLC taxi trip. Di seguito è riportato un esempio dei dati:

|vendor_id|rate_code|passenger_count|trip_time_in_secs|trip_distance|payment_type|fare_amount|
|---|---|---|---|---|---|---|
|VTS|1|1|1140|3,75|CRD|15.5|
|VTS|1|1|480|2.72|CRD|10,0|
|VTS|1|1|1680|7.8|CSH|26,5|

### <a name="inspect-the-onnx-model-optional"></a>Esaminare il modello ONNX (facoltativo)

Usare uno strumento come Netron per esaminare gli input e gli output del modello.

1. Aprire Netron.
1. Nella barra dei menu superiore selezionare **file > Apri** e usare il browser file per selezionare il modello.
1. Verrà visualizzato il modello. Ad esempio, la struttura del modello *automl-Model. Onnx* ha un aspetto simile al seguente:

    :::image type="content" source="media/how-to-use-automl-onnx-model-dotnet/netron-automl-onnx-model.png" alt-text="Modello Netron AutoML ONNX":::

1. Selezionare l'ultimo nodo nella parte inferiore del grafico ( `variable_out1` in questo caso) per visualizzare i metadati del modello. Gli input e gli output nella barra laterale mostrano gli input, gli output e i tipi di dati previsti dal modello. Utilizzare queste informazioni per definire lo schema di input e di output del modello.

### <a name="define-model-input-schema"></a>Definire lo schema di input del modello

Creare una nuova classe denominata `OnnxInput` con le proprietà seguenti all'interno del file *Program.cs* .

```csharp
public class OnnxInput
{
    [ColumnName("vendor_id")]
    public string VendorId { get; set; }

    [ColumnName("rate_code"),OnnxMapType(typeof(Int64),typeof(Single))]
    public Int64 RateCode { get; set; }

    [ColumnName("passenger_count"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 PassengerCount { get; set; }

    [ColumnName("trip_time_in_secs"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 TripTimeInSecs { get; set; }

    [ColumnName("trip_distance")]
    public float TripDistance { get; set; }

    [ColumnName("payment_type")]
    public string PaymentType { get; set; }
}
```

Ognuna delle proprietà esegue il mapping a una colonna nel set di dati. Le proprietà sono ulteriormente annotate con gli attributi.

L' [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) attributo consente di specificare il modo in cui ml.NET deve fare riferimento alla colonna quando opera sui dati. Sebbene la `TripDistance` Proprietà segua le convenzioni di denominazione .NET standard, ad esempio, il modello è a conoscenza di una colonna o di una funzionalità nota come `trip_distance` . Per risolvere questa discrepanza di denominazione, l' [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) attributo esegue il mapping della `TripDistance` proprietà a una colonna o a una funzionalità in base al nome `trip_distance` .
  
Per i valori numerici, ML.NET opera solo sui [`Single`](xref:System.Single) tipi di valore. Tuttavia, il tipo di dati originale di alcune colonne sono numeri interi. L' [`OnnxMapType`](xref:Microsoft.ML.Transforms.Onnx.OnnxMapTypeAttribute) attributo esegue il mapping dei tipi tra ONNX e ml.NET.

Per altre informazioni sugli attributi dei dati, vedere la [guida ml.NET Load data](/dotnet/machine-learning/how-to-guides/load-data-ml-net).

### <a name="define-model-output-schema"></a>Definire lo schema di output del modello

Una volta elaborati i dati, viene generato un output di un determinato formato. Definire lo schema di output dei dati. Creare una nuova classe denominata `OnnxOutput` con le proprietà seguenti all'interno del file *Program.cs* .

```csharp
public class OnnxOutput
{
    [ColumnName("variable_out1")]
    public float[] PredictedFare { get; set; }
}
```

Analogamente a `OnnxInput` , utilizzare l' [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) attributo per eseguire il mapping dell' `variable_out1` output a un nome più descrittivo `PredictedFare` .

## <a name="define-a-prediction-pipeline"></a>Definire una pipeline di stima

Una pipeline in ML.NET è in genere una serie di trasformazioni concatenate che operano sui dati di input per produrre un output. Per ulteriori informazioni sulle trasformazioni dei dati, vedere la [Guida alla trasformazione dei](/dotnet/machine-learning/resources/transforms)dati in ml.NET.

1. Creare un nuovo metodo chiamato `GetPredictionPipeline` all'interno della `Program` classe

    ```csharp
    static ITransformer GetPredictionPipeline(MLContext mlContext)
    {

    }
    ```

1. Definire il nome delle colonne di input e di output. Aggiungere il codice seguente all'interno del metodo `GetPredictionPipeline`.

    ```csharp
    var inputColumns = new string []
    {
        "vendor_id", "rate_code", "passenger_count", "trip_time_in_secs", "trip_distance", "payment_type"
    };

    var outputColumns = new string [] { "variable_out1" };
    ```

1. Definire la pipeline. Un oggetto [`IEstimator`](xref:Microsoft.ML.IEstimator%601) fornisce un progetto degli schemi di operazioni, input e output della pipeline.

    ```csharp
    var onnxPredictionPipeline =
        mlContext
            .Transforms
            .ApplyOnnxModel(
                outputColumnNames: outputColumns,
                inputColumnNames: inputColumns,
                ONNX_MODEL_PATH);
    ```

    In questo caso, [`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel%2A) è l'unica trasformazione nella pipeline, che accetta i nomi delle colonne di input e di output, nonché il percorso del file di modello ONNX.

1. Un oggetto [`IEstimator`](xref:Microsoft.ML.IEstimator%601) definisce solo il set di operazioni da applicare ai dati. Ciò che opera sui dati è noto come [`ITransformer`](xref:Microsoft.ML.ITransformer) . Usare il `Fit` metodo per crearne uno dall'oggetto `onnxPredictionPipeline` .

    ```csharp
    var emptyDv = mlContext.Data.LoadFromEnumerable(new OnnxInput[] {});

    return onnxPredictionPipeline.Fit(emptyDv);
    ```

    Il [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit%2A) metodo prevede un [`IDataView`](xref:Microsoft.ML.IDataView) come input su cui eseguire le operazioni. Un [`IDataView`](xref:Microsoft.ML.IDataView) è un modo per rappresentare i dati in ml.NET usando un formato tabulare. Poiché in questo caso la pipeline viene utilizzata solo per le stime, è possibile fornire un oggetto vuoto [`IDataView`](xref:Microsoft.ML.IDataView) per fornire le [`ITransformer`](xref:Microsoft.ML.ITransformer) informazioni necessarie sullo schema di input e di output. L'oggetto adattato [`ITransformer`](xref:Microsoft.ML.ITransformer) viene quindi restituito per un ulteriore utilizzo nell'applicazione.

    > [!TIP]
    > In questo esempio, la pipeline viene definita e usata all'interno della stessa applicazione. È tuttavia consigliabile utilizzare applicazioni separate per definire e utilizzare la pipeline per eseguire stime. In ML.NET le pipeline possono essere serializzate e salvate per un ulteriore utilizzo in altre applicazioni .NET per gli utenti finali. ML.NET supporta varie destinazioni di distribuzione, ad esempio applicazioni desktop, servizi Web, applicazioni webassembly * e molto altro ancora. Per altre informazioni sul salvataggio delle pipeline, vedere la [Guida ai modelli di ml.NET per il salvataggio e il caricamento](/dotnet/machine-learning/how-to-guides/save-load-machine-learning-models-ml-net).
    >
    > * L'assembly webassembly è supportato solo in .NET Core 5 o versione successiva

1. All'interno del `Main` metodo, chiamare il `GetPredictionPipeline` metodo con i parametri obbligatori.

    ```csharp
    var onnxPredictionPipeline = GetPredictionPipeline(mlContext);
    ```

## <a name="use-the-model-to-make-predictions"></a>Usare il modello per effettuare previsioni

Ora che si dispone di una pipeline, è possibile usarla per eseguire stime. ML.NET fornisce un'API utile per eseguire stime su una singola istanza di dati denominata [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) .

1. All'interno del `Main` metodo creare un oggetto [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) usando il [`CreatePredictionEngine`](xref:Microsoft.ML.ModelOperationsCatalog.CreatePredictionEngine%2A) metodo.

    ```csharp
    var onnxPredictionEngine = mlContext.Model.CreatePredictionEngine<OnnxInput, OnnxOutput>(onnxPredictionPipeline);
    ```

1. Creare un input dei dati di test.

    ```csharp
    var testInput = new OnnxInput
    {
        VendorId = "CMT",
        RateCode = 1,
        PassengerCount = 1,
        TripTimeInSecs = 1271,
        TripDistance = 3.8f,
        PaymentType = "CRD"
    };
    ```

1. Utilizzare `predictionEngine` per eseguire stime basate sui nuovi `testInput` dati utilizzando il [`Predict`](xref:Microsoft.ML.PredictionEngineBase%602.Predict%2A) metodo.

    ```csharp
    var prediction = onnxPredictionEngine.Predict(testInput);
    ```

1. Restituire il risultato della stima alla console.

    ```csharp
    Console.WriteLine($"Predicted Fare: {prediction.PredictedFare.First()}");
    ```

1. Usare il interfaccia della riga di comando di .NET Core per eseguire l'applicazione.

    ```dotnetcli
    dotnet run
    ```

    Il risultato dovrebbe essere simile all'output seguente:

    ```text
    Predicted Fare: 15.621523
    ```

Per ulteriori informazioni sull'esecuzione di stime in ML.NET, vedere la [Guida utilizzare un modello per eseguire stime](/dotnet/machine-learning/how-to-guides/machine-learning-model-predictions-ml-net).

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire il modello come API Web ASP.NET Core](/dotnet/machine-learning/how-to-guides/serve-model-web-api-ml-net)
- [Distribuire il modello come funzione .NET di Azure senza server](/dotnet/machine-learning/how-to-guides/serve-model-serverless-azure-functions-ml-net)