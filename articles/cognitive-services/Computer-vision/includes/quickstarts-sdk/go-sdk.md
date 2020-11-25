---
title: 'Avvio rapido: Libreria client di Visione artificiale per Go'
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido illustra come usare la libreria client di Visione artificiale per Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 4eb92e499a1381516ae6bbc33383963155df371f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95096361"
---
<a name="HOLTop"></a>

[Documentazione di riferimento](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [Pacchetto](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Versione più recente di [Go](https://golang.org/dl/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="creare una risorsa Visione artificiale"  target="_blank">creare una risorsa Visione artificiale <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione al servizio Visione artificiale. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
* [Creare le variabili di ambiente](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) per la chiave e per l'URL dell'endpoint, denominati rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-go-project-directory"></a>Creare una directory di progetto Go

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a un'area di lavoro per il progetto Go, denominato `my-app`.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

L'area di lavoro conterrà tre cartelle:

* **src**: questa directory conterrà il codice sorgente e i pacchetti. Tutti i pacchetti installati con il comando `go get` saranno disponibili in questa directory.
* **pkg**: questa directory conterrà gli oggetti pacchetto Go compilati. Tutti i file hanno estensione `.a`.
* **bin**: questa directory conterrà i file eseguibili binari creati durante l'esecuzione di `go install`.

> [!TIP]
> Per altre informazioni sulla struttura di un'area di lavoro Go, vedere la [documentazione del linguaggio Go](https://golang.org/doc/code.html#Workspaces). Questa guida include informazioni per l'impostazione di `$GOPATH` e `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Installare la libreria client per Go

Successivamente, installare la libreria client per Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

oppure, se si usa DEP, all'interno del repository eseguire:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Creare un'applicazione Go

Successivamente, creare un file nella directory **src** denominato `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Aprire `sample-app.go` nell'IDE o nell'editor di testo preferito. Aggiungere quindi il nome del pacchetto e importare le librerie seguenti:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Dichiarare inoltre un contesto nella radice dello script. Questo oggetto sarà necessario per eseguire la maggior parte delle chiamate di funzione di Visione artificiale:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Successivamente, si inizierà ad aggiungere il codice per eseguire diverse operazioni di Visione artificiale.

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle funzionalità principali dell'SDK Visione artificiale per Go.

|Nome|Descrizione|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Questa classe è necessaria per tutte le funzionalità di Visione artificiale, ad esempio l'analisi delle immagini e la lettura del testo. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per eseguire la maggior parte delle operazioni con le immagini.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Questo tipo contiene i risultati di una chiamata di funzione **AnalyzeImage**. Esistono tipi simili per ogni funzione specifica della categoria.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Questo tipo contiene i risultati di un'operazione di lettura batch. |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Questo tipo definisce le diverse tipologie di analisi delle immagini che è possibile eseguire in un'operazione di analisi standard. Specificare un set di valori di VisualFeatureTypes a seconda delle esigenze. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Visione artificiale per Go:

* [Autenticare il client](#authenticate-the-client)
* [Analizzare un'immagine](#analyze-an-image)
* [Leggere il testo stampato e scritto a mano](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticare il client

> [!NOTE]
> Questo passaggio presuppone che siano state [create variabili di ambiente](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) per la chiave e l'endpoint del servizio Visione artificiale, denominate rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.

Creare una funzione `main` e aggiungervi il codice seguente per creare un'istanza di un client con l'endpoint e la chiave.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

## <a name="analyze-an-image"></a>Analizzare un'immagine

Il codice seguente usa l'oggetto client per analizzare un'immagine remota e stampare i risultati nella console. È possibile ottenere una descrizione di testo, una categorizzazione, un elenco di tag, gli oggetti rilevati, i marchi rilevati, i visi rilegati, i flag di contenuto per adulti, i colori principali e il tipo di immagine.

### <a name="set-up-test-image"></a>Configurare l'immagine di test

Salvare prima un riferimento all'URL dell'immagine da analizzare. Includere il riferimento all'interno della funzione `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> È anche possibile analizzare un'immagine locale. Vedere i metodi [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient), ad esempio **DescribeImageInStream**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).

### <a name="specify-visual-features"></a>Specificare le caratteristiche visive

Le chiamate di funzione seguenti estraggono diverse funzionalità visive dall'immagine di esempio. Queste funzioni verranno definite nelle sezioni seguenti.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Ottenere la descrizione dell'immagine

La funzione seguente ottiene l'elenco delle didascalie generate per l'immagine. Per altre informazioni sulla descrizione dell'immagine, vedere [Descrivere le immagini](../../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Ottenere la categoria dell'immagine

La funzione seguente ottiene la categoria rilevata dell'immagine. Per altre informazioni, vedere [Categorizzare le immagini](../../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Ottenere i tag delle immagini

La funzione seguente ottiene il set di tag rilevati nell'immagine. Per altre informazioni, vedere [Tag del contenuto](../../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Rilevare oggetti

La funzione seguente rileva gli oggetti comuni nell'immagine e li stampa nella console. Per altre informazioni, vedere [Rilevamento di oggetti](../../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Rilevare marchi

Il codice seguente rileva i marchi e i logo aziendali nell'immagine e li stampa nella console. Per altre informazioni, vedere [Rilevamento di marchi](../../concept-brand-detection.md).

Dichiarare innanzitutto un riferimento a una nuova immagine all'interno della funzione `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Il codice seguente definisce la funzione di rilevamento dei marchi.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Rilevare visi

La funzione seguente restituisce i visi rilevati nell'immagine con le relative coordinate del rettangolo e determinati attributi del viso. Per altre informazioni, vedere [Rilevamento viso](../../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Rilevare contenuto per adulti, spinto o cruento

Il codice seguente stampa la presenza rilevata di contenuto per adulti nell'immagine. Per altre informazioni, vedere [Contenuto per adulti, spinto o cruento](../../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Ottenere la combinazione di colori dell'immagine

La funzione seguente stampa gli attributi di colore rilevati nell'immagine, ad esempio i colori dominanti e il colore principale. Per altre informazioni, vedere [Combinazioni di colori](../../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Ottenere contenuto specifico del dominio

Visione artificiale può usare modelli speciali per eseguire un'ulteriore analisi delle immagini. Per altre informazioni, vedere [Contenuto specifico del dominio](../../concept-detecting-domain-content.md). 

Il codice seguente analizza i dati sulle celebrità rilevate nell'immagine.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Il codice seguente analizza i dati sui luoghi di interesse rilevati nell'immagine.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Ottenere il tipo di immagine

La funzione seguente stampa informazioni sul tipo di immagine,&mdash;ad esempio se si tratta di ClipArt o di un disegno.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Leggere il testo stampato e scritto a mano

Visione artificiale può leggere il testo visibile di un'immagine e convertirlo in un flusso di caratteri. Il codice in questa sezione definisce una funzione, `RecognizeTextReadAPIRemoteImage`, che usa l'oggetto client per rilevare ed estrarre il testo stampato o scritto a mano nell'immagine.

Aggiungere il riferimento all'immagine di esempio e la chiamata di funzione nella funzione `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> È anche possibile estrarre il testo da un'immagine locale. Vedere i metodi [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient), ad esempio **BatchReadFileInStream**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).

### <a name="call-the-read-api"></a>Chiamare l'API di lettura

Definire la nuova funzione per la lettura del testo, `RecognizeTextReadAPIRemoteImage`. Aggiungere il codice seguente, che chiama il metodo **BatchReadFile** per l'immagine specificata. Questo metodo restituisce un ID operazione e avvia un processo asincrono per leggere il contenuto dell'immagine.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Ottenere risultati della lettura

Ottenere quindi l'ID operazione restituito dalla chiamata a **BatchReadFile** e usarlo con il metodo **GetReadOperationResult** per eseguire una query sul servizio per ottenere i risultati dell'operazione. Il codice seguente controlla l'operazione a intervalli di un secondo fino a quando non vengono restituiti i risultati. Stampa quindi i dati di testo estratti nella console.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Visualizzare i risultati della lettura

Aggiungere il codice seguente per analizzare e visualizzare i dati di testo recuperati e terminare la definizione della funzione.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione dalla directory dell'applicazione con il comando `go run`.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento per l'API Visione artificiale (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)

* [Informazioni su Visione artificiale](../../overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).