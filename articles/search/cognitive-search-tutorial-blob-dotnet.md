---
title: Esercitazione per C# sull'uso dell'intelligenza artificiale nei BLOB di Azure
titleSuffix: Azure Cognitive Search
description: Procedura di esempio di estrazione del testo ed elaborazione del linguaggio naturale sul contenuto in archiviazione BLOB con C# e Azure Cognitive Search SDK per .NET.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: da7a80842bec68fde8cc44401bb04c2dd061741f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787959"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>Esercitazione: Contenuto ricercabile generato tramite intelligenza artificiale da BLOB di Azure con .NET SDK

Se Archiviazione BLOB di Azure contiene immagini o testo non strutturato, una [pipeline di arricchimento tramite intelligenza artificiale](cognitive-search-concept-intro.md) può estrarre informazioni e creare nuovi contenuti per scenari di ricerca full-text o knowledge mining. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un ambiente di sviluppo.
> * Definire una pipeline sui BLOB usando il riconoscimento ottico dei caratteri (OCR), il rilevamento della lingua e il riconoscimento di entità e frasi chiave.
> * Eseguire la pipeline per richiamare trasformazioni e per creare e caricare un indice di ricerca.
> * Esplorare i risultati tramite un ricerca full-text e una sintassi di query avanzata.

Se non si ha una sottoscrizione di Azure, aprire un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="overview"></a>Panoramica

Questa esercitazione usa C# e la libreria client **Azure.Search.Documents** per creare un'origine dati, un indice, un indicizzatore e un set di competenze.

Il set di competenze usa competenze predefinite basate sull'API Servizi cognitivi. I passaggi della pipeline includono il riconoscimento ottico dei caratteri (OCR) sulle immagini, il rilevamento della lingua sul testo, l'estrazione di frasi chiave e il riconoscimento di entità (organizzazioni). Le nuove informazioni vengono archiviate in nuovi campi che è possibile sfruttare in query, facet e filtri.

## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio](https://visualstudio.microsoft.com/downloads/)
* [Pacchetto NuGet Azure.Search.Documents 11.x](https://www.nuget.org/packages/Azure.Search.Documents) 
* [Archiviazione di Azure](https://azure.microsoft.com/services/storage/)
* [Ricerca cognitiva di Azure](https://azure.microsoft.com/services/search/)

> [!Note]
> Per questa esercitazione è possibile usare il servizio di ricerca gratuito. Un servizio di ricerca gratuito consente di usare solo tre indici, tre indicizzatori e tre origini dati. Questa esercitazione crea un elemento per ogni tipo. Prima di iniziare, assicurarsi che lo spazio nel servizio sia sufficiente per accettare le nuove risorse.

## <a name="download-sample-data"></a>Scaricare dati di esempio

I dati di esempio sono costituiti da 14 file con tipo di contenuto misto che verranno caricati in Archiviazione BLOB di Azure in un passaggio successivo.

1. Aprire questa [cartella OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) e nell'angolo in alto a sinistra fare clic su **Scarica** per copiare i file nel computer. 

1. Fare clic con il pulsante destro del mouse sul file ZIP e selezionare **Estrai tutto** . Sono presenti 14 file di vari tipi. Per questo esercizio ne verranno usati 7.

È anche possibile scaricare il codice sorgente per questa esercitazione. Il codice sorgente è reperibile nella cartella **tutorial-ai-enrichment/v11** nel repository [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="1---create-services"></a>1 - Creare i servizi

Questa esercitazione usa Ricerca cognitiva di Azure per l'indicizzazione e le query, Servizi cognitivi nel back-end per l'arricchimento tramite intelligenza artificiale e Archiviazione BLOB di Azure per fornire i dati. Questa esercitazione non supera l'allocazione gratuita di 20 transazioni per indicizzatore al giorno in Servizi cognitivi, quindi gli unici servizi che è necessario creare sono il servizio di ricerca e quello di archiviazione.

Se possibile, crearli entrambi nella stessa area e nello stesso gruppo di risorse per motivi di prossimità e gestibilità. In pratica, l'account di archiviazione di Azure può trovarsi in qualsiasi area.

### <a name="start-with-azure-storage"></a>Iniziare con Archiviazione di Azure

1. [Accedere al portale di Azure](https://portal.azure.com/) e fare clic su **+ Crea una risorsa** .

1. Cercare *account di archiviazione* e selezionare l'offerta Account di archiviazione di Microsoft.

   ![Creare un account di archiviazione](media/cognitive-search-tutorial-blob/storage-account.png "Creare l'account di archiviazione")

1. Nella scheda Informazioni di base gli elementi seguenti sono obbligatori. Accettare le impostazioni predefinite per tutti gli altri elementi.

   * **Gruppo di risorse** . Selezionarne uno esistente o crearne uno nuovo, ma usare lo stesso gruppo per tutti i servizi in modo che sia possibile gestirli collettivamente.

   * **Nome account di archiviazione** . Se si ritiene che potrebbero esistere più risorse dello stesso tipo, usare il nome per distinguerle in base al tipo e all'area, ad esempio *blobstoragewestus* . 

   * **Località** . Se possibile, scegliere la stessa località usata per Ricerca cognitiva di Azure e Servizi cognitivi. La scelta di un'unica località consente di azzerare i costi correlati alla larghezza di banda.

   * **Tipologia account** . Scegliere l'impostazione predefinita *Archiviazione (utilizzo generico v2)* .

1. Fare clic su **Rivedi e crea** per creare il servizio.

1. Al termine dell'operazione, fare clic su **Vai alla risorsa** per aprire la pagina Panoramica.

1. Fare clic sul servizio **BLOB** .

1. Fare clic su **+ Contenitore** per creare un contenitore e assegnargli il nome *cog-search-demo* .

1. Selezionare *cog-search-demo* e quindi fare clic su **Carica** per aprire la cartella in cui sono stati salvati i file di download. Selezionare tutti i quattordici file e fare clic su **OK** per caricarli.

   ![Carica file di esempio](media/cognitive-search-quickstart-blob/sample-data.png "Carica file di esempio")

1. Prima di uscire da Archiviazione di Azure, ottenere una stringa di connessione in modo che sia possibile definire una connessione in Ricerca cognitiva di Azure. 

   1. Tornare alla pagina Panoramica dell'account di archiviazione (come esempio è stato usato *blobstoragewestus* ). 

   1. Nel riquadro di spostamento sinistro selezionare **Chiavi di accesso** e copiare una delle stringhe di connessione. 

   La stringa di connessione è un URL simile all'esempio seguente:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Salvare la stringa di connessione nel Blocco note. Sarà necessaria più avanti durante la configurazione della connessione all'origine dati.

### <a name="cognitive-services"></a>Servizi cognitivi

L'arricchimento tramite intelligenza artificiale è supportato dai Servizi cognitivi, ad esempio Analisi del testo e Visione artificiale per l'elaborazione del linguaggio naturale e delle immagini. Se l'obiettivo è quello di completare un prototipo o un progetto effettivo, a questo punto è necessario effettuare il provisioning di Servizi cognitivi (nella stessa area di Ricerca cognitiva di Azure) in modo da poter collegare il servizio alle operazioni di indicizzazione.

Per questo esercizio è tuttavia possibile ignorare il provisioning delle risorse perché Ricerca cognitiva di Azure riesce a connettersi a Servizi cognitivi in background e offre 20 transazioni gratuite per ogni esecuzione dell'indicizzatore. Dato che in questa esercitazione vengono usate 14 transazioni, l'allocazione gratuita è sufficiente. Per progetti di dimensioni maggiori, pianificare il provisioning di Servizi cognitivi al livello S0 con pagamento in base al consumo. Per altre informazioni, vedere [Collegare Servizi cognitivi](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Ricerca cognitiva di Azure

Il terzo componente è Ricerca cognitiva di Azure, un servizio di ricerca che è possibile [creare nel portale](search-create-service-portal.md) o [trovare](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione se esiste già.

Per completare questa procedura dettagliata, è possibile usare il livello gratuito. 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Copiare un URL e una chiave API di amministrazione per Ricerca cognitiva di Azure

Per interagire con il servizio Ricerca cognitiva di Azure, sono necessari l'URL del servizio e una chiave di accesso. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se il servizio Ricerca cognitiva di Azure è stato aggiunto alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

1. In **Impostazioni** > **Chiavi** copiare una chiave di amministrazione per avere diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

   Ottenere anche la chiave di query. È consigliabile inviare richieste di query con accesso di sola lettura.

   ![Ottenere il nome del servizio e le chiavi amministratore e di query](media/search-get-started-javascript/service-name-and-keys.png)

La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="2---set-up-your-environment"></a>2 - Configurare l'ambiente

Iniziare aprendo Visual Studio e creando un nuovo progetto di app console che può essere eseguito in .NET Core.

### <a name="install-azuresearchdocuments"></a>Installare Azure.Search.Documents

[Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search) consiste in una libreria client che consente di gestire indici, origini dati, indicizzatori e set di competenze, nonché di caricare e gestire documenti ed eseguire query, il tutto senza doversi occupare dei dettagli di HTTP e JSON. Questa libreria client viene distribuita come pacchetto NuGet.

Per questo progetto, installare la versione 11 o successiva di `Azure.Search.Documents` e l'ultima versione di `Microsoft.Extensions.Configuration`.

1. In Visual Studio selezionare **Strumenti** > **Gestione pacchetti NuGet** > **Gestisci pacchetti NuGet per la soluzione** .

1. Cercare [Azure.Search.Document](https://www.nuget.org/packages/Azure.Search.Documents).

1. Selezionare la versione più recente e fare clic su **Installa** .

1. Ripetere i passaggi precedenti per installare [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) e [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json).

### <a name="add-service-connection-information"></a>Aggiungere le informazioni di connessione del servizio

1. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Aggiungi** > **Nuovo elemento** . 

1. Assegnare un nome al file `appsettings.json` e selezionare **Aggiungi** . 

1. Includere questo file nella directory di output.
    1. Fare clic con il pulsante destro del mouse su `appsettings.json` e scegliere **Proprietà** . 
    1. Modificare il valore di **Copia nella directory di output** impostandolo su **Copia se più recente** .

1. Copiare il codice JSON seguente nel nuovo file JSON.

    ```json
    {
      "SearchServiceUri": "Put your search service URI here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Aggiungere le informazioni dell'account per il servizio di ricerca e l'archivio BLOB. Tenere presente che è possibile ottenere queste informazioni dalla procedura di provisioning del servizio indicata nella sezione precedente.

Per **SearchServiceUri** , immettere l'URL completo.

### <a name="add-namespaces"></a>Aggiungere spazi dei nomi

In `Program.cs` aggiungere gli spazi dei nomi seguenti.

```csharp
using Azure;
using Azure.Search.Documents.Indexes;
using Azure.Search.Documents.Indexes.Models;
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;
using System.Linq;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Creare un client

Creare un'istanza di `SearchIndexClient` e di `SearchIndexerClient` in `Main`.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    string searchServiceUri = configuration["SearchServiceUri"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];
    string cognitiveServicesKey = configuration["CognitiveServicesKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
    SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
}
```

> [!NOTE]
> I client si connettono al servizio di ricerca. Per evitare l'apertura di un numero eccessivo di connessioni, se possibile è consigliabile provare a condividere una singola istanza nell'applicazione. I metodi per abilitare tale condivisione sono thread-safe.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>Aggiungere una funzione per uscire dal programma in caso di errore

Questa esercitazione ha lo scopo di semplificare la comprensione di ogni passaggio della pipeline di indicizzazione. Se si verifica un problema critico che impedisce al programma di creare l'origine dati, il set di competenze, l'indice o l'indicizzatore, il programma restituirà un messaggio di errore e verrà chiuso per consentire l'analisi e la risoluzione del problema.

Aggiungere `ExitProgram` a `Main` per gestire gli scenari che richiedono la chiusura del programma.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 - Creare la pipeline

In Ricerca cognitiva di Azure l'elaborazione tramite intelligenza artificiale viene eseguita durante l'indicizzazione o l'inserimento dati. In questa parte della procedura dettagliata vengono creati quattro oggetti: origine dati, definizione dell'indice, set di competenze e indicizzatore. 

### <a name="step-1-create-a-data-source"></a>Passaggio 1: Creare un'origine dati

`SearchIndexerClient` include una proprietà [`DataSourceName`](/dotnet/api/azure.search.documents.indexes.models.searchindexer.datasourcename) che è possibile impostare su un oggetto `SearchIndexerDataSourceConnection`. Questo oggetto fornisce tutti i metodi necessari per creare, elencare, aggiornare o eliminare le origini dati di Ricerca cognitiva di Azure.

Creare una nuova istanza di `SearchIndexerDataSourceConnection` chiamando `indexerClient.CreateOrUpdateDataSourceConnection(dataSource)`. Il codice seguente crea un'origine dati di tipo `AzureBlob`.

```csharp
private static SearchIndexerDataSourceConnection CreateOrUpdateDataSource(SearchIndexerClient indexerClient, IConfigurationRoot configuration)
{
    SearchIndexerDataSourceConnection dataSource = new SearchIndexerDataSourceConnection(
        name: "demodata",
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["AzureBlobConnectionString"],
        container: new SearchIndexerDataContainer("cog-search-demo"))
    {
        Description = "Demo files to demonstrate cognitive search capabilities."
    };

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateDataSourceConnection(dataSource);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

Per una richiesta corretta, il metodo restituirà l'origine dati che è stata creata. Se si verifica un problema con la richiesta, ad esempio un parametro non valido, il metodo genererà un'eccezione.

A questo punto aggiungere una riga in `Main` per chiamare la funzione `CreateOrUpdateDataSource` appena aggiunta.

```csharp
// Create or Update the data source
Console.WriteLine("Creating or updating the data source...");
SearchIndexerDataSourceConnection dataSource = CreateOrUpdateDataSource(indexerClient, configuration);
```

Compilare ed eseguire la soluzione. Trattandosi della prima richiesta, controllare nel portale di Azure per verificare che l'origine dati sia stata creata in Ricerca cognitiva di Azure. Nella pagina di panoramica del servizio di ricerca verificare che l'elenco Origini dati contenga un nuovo elemento. Potrebbe essere necessario attendere alcuni minuti per l'aggiornamento della pagina del portale.

  ![Riquadro Origini dati nel portale](./media/cognitive-search-tutorial-blob/data-source-tile.png "Riquadro Origini dati nel portale")

### <a name="step-2-create-a-skillset"></a>Passaggio 2: Creare un set di competenze

In questa sezione viene definito un set di passaggi di arricchimento da applicare ai dati. Ogni passaggio di arricchimento è noto come *competenza* e il set di passaggi di arricchimento è un *set di competenze* . Questa esercitazione usa [competenze cognitive predefinite](cognitive-search-predefined-skills.md) per il set di competenze:

* [Riconoscimento ottico dei caratteri](cognitive-search-skill-ocr.md) per riconoscere testo stampato e scritto a mano nei file di immagine.

* [Unione testo](cognitive-search-skill-textmerger.md) per consolidare il testo di una raccolta di campi in un unico campo.

* [Rilevamento della lingua](cognitive-search-skill-language-detection.md) per identificare la lingua del contenuto.

* [Divisione del testo](cognitive-search-skill-textsplit.md) per suddividere il contenuto di grandi dimensioni in blocchi più piccoli prima di chiamare la competenza di estrazione di frasi chiave e la competenza di riconoscimento di entità. L'estrazione di frasi chiave e il riconoscimento di entità accettano input composti al massimo da 50.000 caratteri. Alcuni dei file di esempio devono essere suddivisi per rispettare questo limite.

* [Riconoscimento di entità](cognitive-search-skill-entity-recognition.md) per estrarre i nomi di organizzazioni dal contenuto nel contenitore BLOB.

* [Estrazione di frasi chiave](cognitive-search-skill-keyphrases.md) per estrarre le frasi chiave principali.

Durante l'elaborazione iniziale, Ricerca cognitiva di Azure esegue il cracking di ogni documento per estrarre il contenuto da formati di file diversi. Il testo trovato nel file di origine viene inserito in un campo `content` generato, uno per ogni documento. Impostare quindi l'input come `"/document/content"` per usare questo testo. Il contenuto dell'immagine viene inserito in un campo `normalized_images` generato, specificato in un set di competenze come `/document/normalized_images/*`.

Gli output possono essere mappati a un indice, usati come input per una competenza a valle, o entrambi, come nel caso del codice di lingua. Nell'indice, un codice di lingua è utile per le operazioni di filtro. Come input, il codice di lingua viene usato dalle competenze di analisi del testo per la selezione delle regole linguistiche per la separazione delle parole.

Per altre informazioni sui concetti di base dei set di competenze, vedere [How to create a skillset](cognitive-search-defining-skillset.md) (Come creare un set di competenze).

### <a name="ocr-skill"></a>Competenza OCR

La competenza **OCR** estrae il testo dalle immagini. Questa competenza presuppone che esista un campo normalized_images. Per generare questo campo, più avanti nell'esercitazione si imposterà la configurazione ```"imageAction"``` nella definizione dell'indicizzatore su ```"generateNormalizedImages"```.

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("image")
    {
        Source = "/document/normalized_images/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("text")
    {
        TargetName = "text"
    });

    OcrSkill ocrSkill = new OcrSkill(inputMappings, outputMappings)
    {
        Description = "Extract text (plain and structured) from image",
        Context = "/document/normalized_images/*",
        DefaultLanguageCode = OcrSkillLanguage.En,
        ShouldDetectOrientation = true
    };

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Competenza di unione

In questa sezione si creerà una competenza di **unione** che unisce il campo del contenuto del documento con il testo prodotto dalla competenza OCR.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/content"
    });
    inputMappings.Add(new InputFieldMappingEntry("itemsToInsert")
    {
        Source = "/document/normalized_images/*/text"
    });
    inputMappings.Add(new InputFieldMappingEntry("offsets")
    {
        Source = "/document/normalized_images/*/contentOffset"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("mergedText")
    {
        TargetName = "merged_text"
    });

    MergeSkill mergeSkill = new MergeSkill(inputMappings, outputMappings)
    {
        Description = "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        Context = "/document",
        InsertPreTag = " ",
        InsertPostTag = " "
    };

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Competenza di rilevamento lingua

La competenza di **rilevamento lingua** rileva la lingua del testo di input e restituisce un codice lingua singolo per ogni documento inviato nella richiesta. Si userà l'output della competenza di **rilevamento lingua** come parte dell'input per la competenza di **divisione del testo** .

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("languageCode")
    {
        TargetName = "languageCode"
    });

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(inputMappings, outputMappings)
    {
        Description = "Detect the language used in the document",
        Context = "/document"
    };

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Competenza di divisione del testo

La competenza di **divisione** divide il testo per pagine e limita la lunghezza della pagina a 4.000 caratteri, misurata da `String.Length`. L'algoritmo proverà a suddividere il testo in blocchi di dimensioni massime pari a `maximumPageLength`. In questo caso l'algoritmo cercherà di interrompere una frase al suo limite, quindi la dimensione del blocco potrebbe essere leggermente inferiore a `maximumPageLength`.

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("textItems")
    {
        TargetName = "pages",
    });

    SplitSkill splitSkill = new SplitSkill(inputMappings, outputMappings)
    {
        Description = "Split content into pages",
        Context = "/document",
        TextSplitMode = TextSplitMode.Pages,
        MaximumPageLength = 4000,
        DefaultLanguageCode = SplitSkillLanguage.En
    };

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Competenza di riconoscimento entità

Questa istanza `EntityRecognitionSkill` è impostata per riconoscere il tipo di categoria `organization`. La competenza di **riconoscimento entità** può anche riconoscere i tipi di categoria `person` e `location`.

Si noti che il campo "context" è impostato su ```"/document/pages/*"``` con un asterisco. Questo significa che il passaggio di arricchimento viene chiamato per ogni pagina presente in ```"/document/pages"```.

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("organizations")
    {
        TargetName = "organizations"
    });

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(inputMappings, outputMappings)
    {
        Description = "Recognize organizations",
        Context = "/document/pages/*",
        DefaultLanguageCode = EntityRecognitionSkillLanguage.En
    };
    entityRecognitionSkill.Categories.Add(EntityCategory.Organization);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Competenza di estrazione di frasi chiave

Come l'istanza `EntityRecognitionSkill` appena creata, viene chiamata la competenza di **estrazione di frase chiave** per ogni pagina del documento.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("keyPhrases")
    {
        TargetName = "keyPhrases"
    });

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(inputMappings, outputMappings)
    {
        Description = "Extract the key phrases",
        Context = "/document/pages/*",
        DefaultLanguageCode = KeyPhraseExtractionSkillLanguage.En
    };

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Compilare e creare il set di competenze

Compilare lo `Skillset`​ ​usando le competenze create.

```csharp
private static SearchIndexerSkillset CreateOrUpdateDemoSkillSet(SearchIndexerClient indexerClient, IList<SearchIndexerSkill> skills,string cognitiveServicesKey)
{
    SearchIndexerSkillset skillset = new SearchIndexerSkillset("demoskillset", skills)
    {
        Description = "Demo skillset",
        CognitiveServicesAccount = new CognitiveServicesAccountKey(cognitiveServicesKey)
    };

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateSkillset(skillset);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Aggiungere le righe seguenti a `Main`.

```csharp
// Create the skills
Console.WriteLine("Creating the skills...");
OcrSkill ocrSkill = CreateOcrSkill();
MergeSkill mergeSkill = CreateMergeSkill();
EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
SplitSkill splitSkill = CreateSplitSkill();
KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

// Create the skillset
Console.WriteLine("Creating or updating the skillset...");
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
```

### <a name="step-3-create-an-index"></a>Passaggio 3: Creare un indice

In questa sezione viene definito lo schema dell'indice specificando i campi da includere nell'indice di ricerca e gli attributi di ricerca per ogni campo. I campi hanno un tipo e possono accettare attributi che determinano il modo in cui viene usato il campo (searchable, sortable e così via). Non è necessario che i nomi dei campi in un indice corrispondano esattamente ai nomi dei campi nell'origine. In un passaggio successivo verranno aggiunti i mapping dei campi in un indicizzatore per collegare i campi di origine e destinazione. Per questo passaggio, definire l'indice usando le convenzioni di denominazione dei campi pertinenti per l'applicazione di ricerca in questione.

Questo esercizio usa i campi e i tipi di campi seguenti:

| Nomi dei campi | Tipi di campo |
| --- | --- |
| `id` | Edm.String |
| `content` | Edm.String |
| `languageCode` | Edm.String |
| `keyPhrases` | List<Edm.String> |
| `organizations` | List<Edm.String> |

#### <a name="create-demoindex-class"></a>Creare una classe DemoIndex

I campi per questo indice vengono definiti usando una classe modello. Ogni proprietà della classe modello contiene attributi che determinano i comportamenti correlati alla ricerca del campo di indice corrispondente. 

La classe modello verrà aggiunta a un nuovo file C#. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **Nuovo elemento** , selezionare "Classe", assegnare al file il nome `DemoIndex.cs` e quindi selezionare **Aggiungi** .

Indicare che si vogliono usare i tipi degli spazi dei nomi `Azure.Search.Documents.Indexes` e `System.Text.Json.Serialization`.

Aggiungere la definizione di classe modello seguente a `DemoIndex.cs` e includerla nello stesso spazio dei nomi in cui verrà creato l'indice.

```csharp
using Azure.Search.Documents.Indexes;
using System.Text.Json.Serialization;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase is currently unsupported as of this writing. 
    // Replace it with JsonPropertyName
    public class DemoIndex
    {
        [SearchableField(IsSortable = true, IsKey = true)]
        [JsonPropertyName("id")]
        public string Id { get; set; }

        [SearchableField]
        [JsonPropertyName("content")]
        public string Content { get; set; }

        [SearchableField]
        [JsonPropertyName("languageCode")]
        public string LanguageCode { get; set; }

        [SearchableField]
        [JsonPropertyName("keyPhrases")]
        public string[] KeyPhrases { get; set; }

        [SearchableField]
        [JsonPropertyName("organizations")]
        public string[] Organizations { get; set; }
    }
}
```

Dopo aver definito una classe modello, di nuovo in `Program.cs` è possibile creare una definizione di indice in modo molto semplice. Il nome di questo indice sarà `demoindex`. Se esiste già un indice con tale nome, verrà eliminato.

```csharp
private static SearchIndex CreateDemoIndex(SearchIndexClient indexClient)
{
    FieldBuilder builder = new FieldBuilder();
    var index = new SearchIndex("demoindex")
    {
        Fields = builder.Build(typeof(DemoIndex))
    };

    try
    {
        indexClient.GetIndex(index.Name);
        indexClient.DeleteIndex(index.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified index not exist, 404 will be thrown.
    }

    try
    {
        indexClient.CreateIndex(index);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Durante i test è possibile che si provi a creare l'indice più di una volta. Per questo motivo verificare se l'indice che si sta per creare esiste già prima di tentare di crearlo.

Aggiungere le righe seguenti a `Main`.

```csharp
// Create the index
Console.WriteLine("Creating the index...");
SearchIndex demoIndex = CreateDemoIndex(indexClient);
```

Aggiungere l'istruzione using seguente per risolvere il riferimento di disambiguazione.

```csharp
using Index = Azure.Search.Documents.Indexes.Models;
```

Per altre informazioni sui concetti degli indici, vedere [Creare un indice (API REST)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Passaggio 4: Creare ed eseguire un indicizzatore

Finora sono stati creati un'origine dati, un set di competenze e un indice. Questi tre componenti diventano parte di un [indicizzatore](search-indexer-overview.md) che riunisce tutti i dati in un'unica operazione in più fasi. Per unire questi elementi in un indicizzatore, è necessario definire mapping dei campi.

* Gli oggetti fieldMappings vengono elaborati prima del set di competenze, eseguendo il mapping dei campi di origine dall'origine dati ai campi di destinazione in un indice. Se i nomi e i tipi di campo sono uguali alle due estremità, non è necessario alcun mapping.

* Gli oggetti outputFieldMappings vengono elaborati dopo il set di competenze facendo riferimento agli oggetti sourceFieldNames che non esistono fino a quando non vengono creati dall'individuazione o dall'arricchimento dei documenti. L'oggetto targetFieldName è un campo in un indice.

Oltre ad associare gli input agli output, è anche possibile usare i mapping dei campi per rendere flat le strutture dei dati. Per altre informazioni, vedere [Come eseguire il mapping dei campi migliorati a un indice ricercabile](cognitive-search-output-field-mapping.md).

```csharp
private static SearchIndexer CreateDemoIndexer(SearchIndexerClient indexerClient, SearchIndexerDataSourceConnection dataSource, SearchIndexerSkillset skillSet, SearchIndex index)
{
    IndexingParameters indexingParameters = new IndexingParameters()
    {
        MaxFailedItems = -1,
        MaxFailedItemsPerBatch = -1,
    };
    indexingParameters.Configuration.Add("dataToExtract", "contentAndMetadata");
    indexingParameters.Configuration.Add("imageAction", "generateNormalizedImages");

    SearchIndexer indexer = new SearchIndexer("demoindexer", dataSource.Name, index.Name)
    {
        Description = "Demo Indexer",
        SkillsetName = skillSet.Name,
        Parameters = indexingParameters
    };

    FieldMappingFunction mappingFunction = new FieldMappingFunction("base64Encode");
    mappingFunction.Parameters.Add("useHttpServerUtilityUrlTokenEncode", true);

    indexer.FieldMappings.Add(new FieldMapping("metadata_storage_path")
    {
        TargetFieldName = "id",
        MappingFunction = mappingFunction

    });
    indexer.FieldMappings.Add(new FieldMapping("content")
    {
        TargetFieldName = "content"
    });

    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/organizations/*")
    {
        TargetFieldName = "organizations"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/keyPhrases/*")
    {
        TargetFieldName = "keyPhrases"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/languageCode")
    {
        TargetFieldName = "languageCode"
    });

    try
    {
        indexerClient.GetIndexer(indexer.Name);
        indexerClient.DeleteIndexer(indexer.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified indexer not exist, 404 will be thrown.
    }

    try
    {
        indexerClient.CreateIndexer(indexer);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```

Aggiungere le righe seguenti a `Main`.

```csharp
// Create the indexer, map fields, and execute transformations
Console.WriteLine("Creating the indexer and executing the pipeline...");
SearchIndexer demoIndexer = CreateDemoIndexer(indexerClient, dataSource, skillset, demoIndex);
```

L'elaborazione dell'indicizzatore richiederà del tempo. Anche se il set di dati è piccolo, le competenze analitiche prevedono un utilizzo elevato delle risorse di calcolo. Alcune competenze, ad esempio l'analisi delle immagini, hanno un'esecuzione prolungata.

> [!TIP]
> La creazione di un indicizzatore richiama la pipeline. Eventuali problemi a raggiungere i dati, per il mapping di input e output o nell'ordine delle operazioni vengono visualizzati in questa fase.

### <a name="explore-creating-the-indexer"></a>Esaminare la creazione dell'indicizzatore

Il codice imposta `"maxFailedItems"` su -1, che indica al motore di indicizzazione di ignorare gli errori durante l'importazione dei dati. Ciò è utile dato che l'origine dati di esempio include pochi documenti. Per un'origine dati più grande sarebbe necessario impostare un valore maggiore di 0.

Si noti inoltre che `"dataToExtract"` è impostato su `"contentAndMetadata"`. Questa istruzione indica all'indicizzatore di estrarre automaticamente il contenuto dai vari formati di file, oltre ai metadati correlati a ogni file.

Quando viene estratto il contenuto, è possibile impostare `imageAction` per estrarre il testo dalle immagini trovate nell'origine dati. L'impostazione di `"imageAction"` su `"generateNormalizedImages"`, unita alla competenza OCR e alla competenza di unione del testo, indica all'indicizzatore di estrarre il testo dalle immagini (ad esempio, la parola "stop" da un segnale stradale di stop) e incorporarlo come parte del campo del contenuto. Questo comportamento si applica sia alle immagini incorporate nei documenti, ad esempio un'immagine all'interno di un PDF, che alle immagini trovate nell'origine dati, ad esempio un file JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Monitorare l'indicizzazione

Dopo averlo definito, l'indicizzatore viene eseguito automaticamente quando si invia la richiesta. A seconda delle competenze cognitive definite, l'indicizzazione può richiedere più tempo del previsto. Per scoprire se l'indicizzatore è ancora in esecuzione, usare il metodo `GetStatus`.

```csharp
private static void CheckIndexerOverallStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        var demoIndexerExecutionInfo = indexerClient.GetIndexerStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Value.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", ex.Message);
    }
}
```

`demoIndexerExecutionInfo` rappresenta lo stato corrente e la cronologia di esecuzione di un indicizzatore.

Gli avvisi sono comuni con alcune combinazioni di file di origine e competenze e non sempre indicano un problema. In questa esercitazione, gli avvisi sono innocui (ad esempio, nessun input di testo dai file JPEG).

Aggiungere le righe seguenti a `Main`.

```csharp
// Check indexer overall status
Console.WriteLine("Check the indexer overall status...");
CheckIndexerOverallStatus(indexerClient, demoIndexer);
```

## <a name="5---search"></a>5 - Eseguire ricerche

Nelle app console dell'esercitazione su Ricerca cognitiva di Azure in genere viene aggiunto un ritardo di 2 secondi prima di eseguire query che restituiscono risultati, ma poiché l'arricchimento richiede diversi minuti, l'app console verrà chiusa e verrà usato un altro approccio.

L'opzione più semplice è costituita da [Esplora ricerche](search-explorer.md) nel portale. È possibile eseguire prima una query vuota che restituisce tutti i documenti oppure una ricerca più mirata che restituisce il nuovo contenuto del campo creato dalla pipeline. 

1. Nel portale di Azure, nella pagina di panoramica della ricerca, selezionare **Indici** .

1. Trovare **`demoindex`** nell'elenco. Dovrebbero essere presenti 14 documenti. Se il numero di documenti è zero, significa l'indicizzatore è ancora in esecuzione o che la pagina non è stata ancora aggiornata. 

1. Selezionare **`demoindex`** . Esplora ricerche è la prima scheda.

1. È possibile iniziare a eseguire ricerche nel contenuto subito dopo il caricamento del primo documento. Per verificare se esiste contenuto, eseguire una query non specificata facendo clic su **Cerca** . Questa query restituisce tutti i documenti attualmente indicizzati, offrendo un'idea del contenuto dell'indice.

1. Incollare quindi la stringa seguente per ottenere risultati più gestibili: `search=*&$select=id, languageCode, organizations`

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Reimpostare ed eseguire di nuovo

Nelle prime fasi sperimentali di sviluppo l'approccio più pratico per le iterazioni di progettazione consiste nell'eliminare gli oggetti da Ricerca cognitiva di Azure e consentire al codice di ricompilarli. I nomi di risorsa sono univoci. L'eliminazione di un oggetto consente di ricrearlo usando lo stesso nome.

Il codice di esempio per questa esercitazione verifica la presenza di oggetti esistenti e li elimina in modo da poter eseguire nuovamente il codice. È anche possibile usare il portale per eliminare indici, indicizzatori, origini dati e set di competenze.

## <a name="takeaways"></a>Risultati

Questa esercitazione ha illustrato i passaggi di base per la creazione di una pipeline di indicizzazione arricchita tramite la creazione delle parti componenti: un'origine dati, un set di competenze, un indice e un indicizzatore.

Sono state presentate le [competenze predefinite](cognitive-search-predefined-skills.md), oltre alla definizione del set di competenze e ai meccanismi di concatenamento delle competenze tramite input e output. Si è inoltre appreso che `outputFieldMappings` nella definizione dell'indicizzatore è necessario per indirizzare i valori arricchiti dalla pipeline in un indice di ricerca in un servizio Ricerca cognitiva di Azure.

Infine, è stato descritto come testare i risultati e reimpostare il sistema per ulteriori iterazioni. Si è appreso che l'esecuzione di query sull'indice consente di restituire l'output creato dalla pipeline di indicizzazione arricchita. È stato inoltre descritto come controllare lo stato dell'indicizzatore e quali oggetti eliminare prima di eseguire di nuovo una pipeline.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno rimuovere le risorse che non sono più necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento Tutte le risorse o Gruppi di risorse nel riquadro di spostamento a sinistra.

## <a name="next-steps"></a>Passaggi successivi

Ora che si ha familiarità con tutti gli oggetti in una pipeline di arricchimento tramite intelligenza artificiale, verranno esaminate in dettaglio le definizioni dei set di competenze e le singole competenze.

> [!div class="nextstepaction"]
> [Come creare un set di competenze](cognitive-search-defining-skillset.md)