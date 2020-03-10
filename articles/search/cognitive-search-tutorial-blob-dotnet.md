---
title: 'Esercitazione: C# e intelligenza artificiale su BLOB di Azure'
titleSuffix: Azure Cognitive Search
description: Procedura di esempio di estrazione del testo ed elaborazione del linguaggio naturale sul contenuto in archiviazione BLOB con C# e Azure Cognitive Search SDK per .NET.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 0b9e7732e5274fd71c773a19d17e09ecdaa2ceb0
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270012"
---
# <a name="tutorial-use-c-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Esercitazione: Usare C# e intelligenza artificiale per generare contenuto ricercabile dai BLOB di Azure

Se si dispone di immagini o di testo non strutturato in Archiviazione BLOB di Azure, è possibile usare la [pipeline di arricchimento tramite intelligenza artificiale](cognitive-search-concept-intro.md) per estrarre informazioni e creare nuovo contenuto utile per gli scenari di ricerca full-text o di knowledge mining. In questa esercitazione per C# viene applicato il riconoscimento ottico dei caratteri (OCR) sulle immagini e viene eseguita l'elaborazione del linguaggio naturale per creare nuovi campi che è possibile sfruttare in query, facet e filtri.

In questa esercitazione si usa C# e [.NET SDK](https://aka.ms/search-sdk) per le attività seguenti:

> [!div class="checklist"]
> * Iniziare a usare file e immagini dell'applicazione nell'archivio BLOB di Azure.
> * Definire una pipeline per aggiungere riconoscimento ottico dei caratteri (OCR), estrazione del testo, rilevamento della lingua e riconoscimento di frasi chiave ed entità.
> * Definire un indice per archiviare l'output (contenuto non elaborato, nonché coppie nome-valore generate dalla pipeline).
> * Eseguire la pipeline per avviare le trasformazioni e l'analisi, nonché per creare e caricare l'indice.
> * Esplorare i risultati tramite un ricerca full-text e una sintassi di query avanzata.

Se non si ha una sottoscrizione di Azure, aprire un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

+ [Archiviazione di Azure](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Creare](search-create-service-portal.md) o [trovare un servizio di ricerca esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> È possibile usare il servizio gratuito per questa esercitazione. Un servizio di ricerca gratuito consente di usare solo tre indici, tre indicizzatori e tre origini dati. Questa esercitazione crea un elemento per ogni tipo. Prima di iniziare, assicurarsi che lo spazio nel servizio sia sufficiente per accettare le nuove risorse.

## <a name="download-files"></a>Scaricare i file

1. Aprire questa [cartella OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) e nell'angolo in alto a sinistra fare clic su **Scarica** per copiare i file nel computer. 

1. Fare clic con il pulsante destro del mouse sul file ZIP e selezionare **Estrai tutto**. Sono presenti 14 file di vari tipi. Per questa esercitazione verranno usati tutti.

## <a name="1---create-services"></a>1 - Creare i servizi

Questa esercitazione usa Ricerca cognitiva di Azure per l'indicizzazione e le query, Servizi cognitivi nel back-end per l'arricchimento tramite intelligenza artificiale e Archiviazione BLOB di Azure per fornire i dati. Questa esercitazione non supera l'allocazione gratuita di 20 transazioni per indicizzatore al giorno in Servizi cognitivi, quindi gli unici servizi che è necessario creare sono il servizio di ricerca e quello di archiviazione.

Se possibile, crearli entrambi nella stessa area e nello stesso gruppo di risorse per motivi di prossimità e gestibilità. In pratica, l'account di archiviazione di Azure può trovarsi in qualsiasi area.

### <a name="start-with-azure-storage"></a>Iniziare con Archiviazione di Azure

1. [Accedere al portale di Azure](https://portal.azure.com/) e fare clic su **+ Crea una risorsa**.

1. Cercare *account di archiviazione* e selezionare l'offerta Account di archiviazione di Microsoft.

   ![Creare un account di archiviazione](media/cognitive-search-tutorial-blob/storage-account.png "Creare l'account di archiviazione")

1. Nella scheda Informazioni di base gli elementi seguenti sono obbligatori. Accettare le impostazioni predefinite per tutti gli altri elementi.

   + **Gruppo di risorse**. Selezionarne uno esistente o crearne uno nuovo, ma usare lo stesso gruppo per tutti i servizi in modo che sia possibile gestirli collettivamente.

   + **Nome account di archiviazione**. Se si ritiene che potrebbero esistere più risorse dello stesso tipo, usare il nome per distinguerle in base al tipo e all'area, ad esempio *blobstoragewestus*. 

   + **Località**. Se possibile, scegliere la stessa località usata per Ricerca cognitiva di Azure e Servizi cognitivi. La scelta di un'unica località consente di azzerare i costi correlati alla larghezza di banda.

   + **Tipologia account**. Scegliere l'impostazione predefinita *Archiviazione (utilizzo generico v2)* .

1. Fare clic su **Rivedi e crea** per creare il servizio.

1. Al termine dell'operazione, fare clic su **Vai alla risorsa** per aprire la pagina Panoramica.

1. Fare clic sul servizio **BLOB**.

1. Fare clic su **+ Contenitore** per creare un contenitore e assegnargli il nome *basic-demo-data-pr*.

1. Selezionare *basic-demo-data-pr* e quindi fare clic su **Carica** per aprire la cartella in cui sono stati salvati i file scaricati. Selezionare tutti i quattordici file e fare clic su **OK** per caricarli.

   ![Carica file di esempio](media/cognitive-search-quickstart-blob/sample-data.png "Carica file di esempio")

1. Prima di uscire da Archiviazione di Azure, ottenere una stringa di connessione in modo che sia possibile definire una connessione in Ricerca cognitiva di Azure. 

   1. Tornare alla pagina Panoramica dell'account di archiviazione (come esempio è stato usato *blobstragewestus*). 
   
   1. Nel riquadro di spostamento sinistro selezionare **Chiavi di accesso** e copiare una delle stringhe di connessione. 

   La stringa di connessione è un URL simile all'esempio seguente:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Salvare la stringa di connessione nel Blocco note. Sarà necessaria più avanti durante la configurazione della connessione all'origine dati.

### <a name="cognitive-services"></a>Servizi cognitivi

L'arricchimento tramite intelligenza artificiale è supportato dai Servizi cognitivi, ad esempio Analisi del testo e Visione artificiale per l'elaborazione del linguaggio naturale e delle immagini. Se l'obiettivo è quello di completare un prototipo o un progetto effettivo, a questo punto è necessario effettuare il provisioning di Servizi cognitivi (nella stessa area di Ricerca cognitiva di Azure) in modo da poter collegare il servizio alle operazioni di indicizzazione.

Per questo esercizio è tuttavia possibile ignorare il provisioning delle risorse perché Ricerca cognitiva di Azure riesce a connettersi a Servizi cognitivi in background e offre 20 transazioni gratuite per ogni esecuzione dell'indicizzatore. Dal momento che in questa esercitazione vengono usate 7 transazioni, l'allocazione gratuita è sufficiente. Per progetti di dimensioni maggiori, pianificare il provisioning di Servizi cognitivi al livello S0 con pagamento in base al consumo. Per altre informazioni, vedere [Collegare Servizi cognitivi](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Ricerca cognitiva di Azure

Il terzo componente è Ricerca cognitiva di Azure, che è [possibile creare nel portale](search-create-service-portal.md). Per completare questa procedura dettagliata, è possibile usare il livello gratuito. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Ottenere un URL e una chiave API di amministrazione per Ricerca cognitiva di Azure

Per interagire con il servizio Ricerca cognitiva di Azure, sono necessari l'URL del servizio e una chiave di accesso. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se il servizio Ricerca cognitiva di Azure è stato aggiunto alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

1. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

   Ottenere anche la chiave di query. È consigliabile inviare richieste di query con accesso di sola lettura.

   ![Ottenere il nome del servizio e le chiavi amministratore e di query](media/search-get-started-nodejs/service-name-and-keys.png)

La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="2---set-up-your-environment"></a>2 - Configurare l'ambiente

Iniziare aprendo Visual Studio e creando un nuovo progetto di app console che può essere eseguito in .NET Core.

### <a name="install-nuget-packages"></a>Installare i pacchetti NuGet

[Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk) è costituito da alcune librerie client che consentono di gestire indici, origini dati, indicizzatori e set di competenze, nonché di caricare e gestire documenti ed eseguire query, senza doversi occupare dei dettagli di HTTP e JSON. Tutte queste librerie client vengono distribuite come pacchetti NuGet.

Per questo progetto, installare la versione 9 o successiva del pacchetto NuGet `Microsoft.Azure.Search`.

1. Aprire la Console di Gestione pacchetti. Fare clic su **Strumenti** > **Gestione Pacchetti NuGet** > **Console di Gestione pacchetti**. 

1. Passare alla [pagina del pacchetto NuGet Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Selezionare la versione più recente (versione 9 o successiva).

1. Copiare il comando Gestione pacchetti.

1. Tornare alla Console di Gestione pacchetti ed eseguire il comando copiato nel passaggio precedente.

Successivamente, installare il pacchetto NuGet `Microsoft.Extensions.Configuration.Json` più recente.

1. Selezionare **Strumenti** > **Gestione pacchetti NuGet** > **Gestisci pacchetti NuGet per la soluzione**. 

1. Fare clic su **Sfoglia** e cercare il pacchetto NuGet `Microsoft.Extensions.Configuration.Json`. 

1. Selezionare il pacchetto, selezionare il progetto, verificare che la versione sia la versione stabile più recente, quindi fare clic su **Installa**.

### <a name="add-service-connection-information"></a>Aggiungere le informazioni di connessione del servizio

1. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Aggiungi** > **Nuovo elemento**. 

1. Assegnare un nome al file `appsettings.json` e selezionare **Aggiungi**. 

1. Includere questo file nella directory di output.
    1. Fare clic con il pulsante destro del mouse su `appsettings.json` e scegliere **Proprietà**. 
    1. Modificare il valore di **Copia nella directory di output** impostandolo su **Copia se più recente**.

1. Copiare il codice JSON seguente nel nuovo file JSON.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Aggiungere le informazioni dell'account per il servizio di ricerca e l'archivio BLOB. Tenere presente che è possibile ottenere queste informazioni dalla procedura di provisioning del servizio indicata nella sezione precedente.

### <a name="add-namespaces"></a>Aggiungere spazi dei nomi

In `Program.cs` aggiungere gli spazi dei nomi seguenti.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Creare un client

Creare un'istanza della classe `SearchServiceClient` in Main.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` crea un nuovo oggetto `SearchServiceClient` usando i valori archiviati nel file config dell'applicazione (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> La classe `SearchServiceClient` gestisce le connessioni al servizio di ricerca. Per evitare l'apertura di un numero eccessivo di connessioni, se possibile è consigliabile provare a condividere una singola istanza di `SearchServiceClient` nell'applicazione. I metodi per abilitare tale condivisione sono thread-safe.
> 

## <a name="3---create-the-pipeline"></a>3 - Creare la pipeline

In Ricerca cognitiva di Azure l'elaborazione tramite intelligenza artificiale viene eseguita durante l'indicizzazione o l'inserimento dati. In questa parte della procedura dettagliata vengono creati quattro oggetti: origine dati, definizione dell'indice, set di competenze e indicizzatore. 

### <a name="step-1-create-a-data-source"></a>Passaggio 1: Creare un'origine dati

`SearchServiceClient` include una proprietà `DataSources`. Questa proprietà fornisce tutti i metodi necessari per creare, elencare, aggiornare o eliminare le origini dati di Ricerca cognitiva di Azure.

Creare una nuova istanza di `DataSource` chiamando `serviceClient.DataSources.CreateOrUpdate(dataSource)`. `DataSource.AzureBlobStorage` richiede di specificare il nome dell'origine dati, la stringa di connessione e il nome del contenitore BLOB.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "basic-demo-data-pr",
        description: "Demo files to demonstrate cognitive search capabilities.");

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.DataSources.CreateOrUpdate(dataSource);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

Per una richiesta corretta, il metodo restituirà l'origine dati che è stata creata. Se si verifica un problema con la richiesta, ad esempio un parametro non valido, il metodo genererà un'eccezione.

A questo punto aggiungere una riga in Main per chiamare la funzione `CreateOrUpdateDataSource` appena aggiunta.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    // Create or Update the data source
    Console.WriteLine("Creating or updating the data source...");
    DataSource dataSource = CreateOrUpdateDataSource(serviceClient, configuration);
```


<!-- 
```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

Now that you have initialized the `DataSource` object, create the data source. `SearchServiceClient` has a `DataSources` property. This property provides all the methods you need to create, list, update, or delete Azure Cognitive Search data sources.

For a successful request, the method will return the data source that was created. If there is a problem with the request, such as an invalid parameter, the method will throw an exception.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
``` -->

Compilare ed eseguire la soluzione. Trattandosi della prima richiesta, controllare nel portale di Azure per verificare che l'origine dati sia stata creata in Ricerca cognitiva di Azure. Nella pagina del dashboard del servizio di ricerca, verificare che nel riquadro Origini dati sia disponibile un nuovo elemento. Potrebbe essere necessario attendere alcuni minuti per l'aggiornamento della pagina del portale.

  ![Riquadro Origini dati nel portale](./media/cognitive-search-tutorial-blob/data-source-tile.png "Riquadro Origini dati nel portale")

### <a name="step-2-create-a-skillset"></a>Passaggio 2: Creare un set di competenze

In questa sezione viene definito un set di passaggi di arricchimento da applicare ai dati. Ogni passaggio di arricchimento è noto come *competenza* e il set di passaggi di arricchimento è un *set di competenze*. Questa esercitazione usa [competenze cognitive predefinite](cognitive-search-predefined-skills.md) per il set di competenze:

+ [Riconoscimento ottico dei caratteri](cognitive-search-skill-ocr.md) per riconoscere testo stampato e scritto a mano nei file di immagine.

+ [Unione testo](cognitive-search-skill-textmerger.md) per consolidare il testo di una raccolta di campi in un unico campo.

+ [Rilevamento della lingua](cognitive-search-skill-language-detection.md) per identificare la lingua del contenuto.

+ [Divisione del testo](cognitive-search-skill-textsplit.md) per suddividere il contenuto di grandi dimensioni in blocchi più piccoli prima di chiamare la competenza di estrazione di frasi chiave e la competenza di riconoscimento di entità. L'estrazione di frasi chiave e il riconoscimento di entità accettano input composti al massimo da 50.000 caratteri. Alcuni dei file di esempio devono essere suddivisi per rispettare questo limite.

+ [Riconoscimento di entità](cognitive-search-skill-entity-recognition.md) per estrarre i nomi di organizzazioni dal contenuto nel contenitore BLOB.

+ [Estrazione di frasi chiave](cognitive-search-skill-keyphrases.md) per estrarre le frasi chiave principali.

Durante l'elaborazione iniziale, Ricerca cognitiva di Azure esegue il cracking di ogni documento per leggere il contenuto da formati di file diversi. Il testo trovato con origine nel file di origine viene inserito in un campo ```content``` generato, uno per ogni documento. Impostare quindi l'input come ```"/document/content"``` per usare questo testo. 

Gli output possono essere mappati a un indice, usati come input per una competenza a valle, o entrambi, come nel caso del codice di lingua. Nell'indice, un codice di lingua è utile per le operazioni di filtro. Come input, il codice di lingua viene usato dalle competenze di analisi del testo per la selezione delle regole linguistiche per la separazione delle parole.

Per altre informazioni sui concetti di base dei set di competenze, vedere [How to create a skillset](cognitive-search-defining-skillset.md) (Come creare un set di competenze).

### <a name="ocr-skill"></a>Competenza OCR

La competenza **OCR** estrae il testo dalle immagini. Questa competenza presuppone che esista un campo normalized_images. Per generare questo campo, più avanti nell'esercitazione si imposterà la configurazione ```"imageAction"``` nella definizione dell'indicizzatore su ```"generateNormalizedImages"```.

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "text",
        targetName: "text"));

    OcrSkill ocrSkill = new OcrSkill(
        description: "Extract text (plain and structured) from image",
        context: "/document/normalized_images/*",
        inputs: inputMappings,
        outputs: outputMappings,
        defaultLanguageCode: OcrSkillLanguage.En,
        shouldDetectOrientation: true);

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Competenza di unione

In questa sezione si creerà una competenza di **unione** che unisce il campo del contenuto del documento con il testo prodotto dalla competenza OCR.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text"));

    MergeSkill mergeSkill = new MergeSkill(
        description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        insertPreTag: " ",
        insertPostTag: " ");

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Competenza di rilevamento lingua

La competenza di **rilevamento lingua** rileva la lingua del testo di input e restituisce un codice lingua singolo per ogni documento inviato nella richiesta. Si userà l'output della competenza di **rilevamento lingua** come parte dell'input per la competenza di **divisione del testo**.

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode"));

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Competenza di divisione del testo

La competenza di **divisione** divide il testo per pagine e limita la lunghezza della pagina a 4.000 caratteri, misurata da `String.Length`. L'algoritmo proverà a suddividere il testo in blocchi di dimensioni massime pari a `maximumPageLength`. In questo caso l'algoritmo cercherà di interrompere una frase al suo limite, quindi la dimensione del blocco potrebbe essere leggermente inferiore a `maximumPageLength`.

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();

    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages"));

    SplitSkill splitSkill = new SplitSkill(
        description: "Split content into pages",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        textSplitMode: TextSplitMode.Pages,
        maximumPageLength: 4000);

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
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations"));

    List<EntityCategory> entityCategory = new List<EntityCategory>();
    entityCategory.Add(EntityCategory.Organization);

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
        description: "Recognize organizations",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings,
        categories: entityCategory,
        defaultLanguageCode: EntityRecognitionSkillLanguage.En);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Competenza di estrazione di frasi chiave

Come l'istanza `EntityRecognitionSkill` appena creata, viene chiamata la competenza di **estrazione di frase chiave** per ogni pagina del documento.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "keyPhrases",
        targetName: "keyPhrases"));

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
        description: "Extract the key phrases",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings);

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Compilare e creare il set di competenze

Compilare lo `Skillset`​ ​usando le competenze create.

```csharp
private static Skillset CreateOrUpdateDemoSkillSet(SearchServiceClient serviceClient, IList<Skill> skills)
{
    Skillset skillset = new Skillset(
        name: "demoskillset",
        description: "Demo skillset",
        skills: skills);

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.Skillsets.CreateOrUpdate(skillset);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Aggiungere le righe seguenti in Main.

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
    List<Skill> skills = new List<Skill>();
    skills.Add(ocrSkill);
    skills.Add(mergeSkill);
    skills.Add(languageDetectionSkill);
    skills.Add(splitSkill);
    skills.Add(entityRecognitionSkill);
    skills.Add(keyPhraseExtractionSkill);

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>Passaggio 3: Creare un indice

In questa sezione viene definito lo schema dell'indice specificando i campi da includere nell'indice di ricerca e gli attributi di ricerca per ogni campo. I campi hanno un tipo e possono accettare attributi che determinano il modo in cui viene usato il campo (searchable, sortable e così via). Non è necessario che i nomi dei campi in un indice corrispondano esattamente ai nomi dei campi nell'origine. In un passaggio successivo verranno aggiunti i mapping dei campi in un indicizzatore per collegare i campi di origine e destinazione. Per questo passaggio, definire l'indice usando le convenzioni di denominazione dei campi pertinenti per l'applicazione di ricerca in questione.

Questo esercizio usa i campi e i tipi di campi seguenti:

| field-names: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


#### <a name="create-demoindex-class"></a>Creare una classe DemoIndex

I campi per questo indice vengono definiti usando una classe modello. Ogni proprietà della classe modello contiene attributi che determinano i comportamenti correlati alla ricerca del campo di indice corrispondente. 

La classe modello verrà aggiunta a un nuovo file C#. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **Nuovo elemento**, selezionare "Classe", assegnare al file il nome `DemoIndex.cs` e quindi selezionare **Aggiungi**.

Indicare che si vogliono usare i tipi degli spazi dei nomi `Microsoft.Azure.Search` e `Microsoft.Azure.Search.Models`.

Aggiungere la definizione di classe modello seguente a `DemoIndex.cs` e includerla nello stesso spazio dei nomi in cui verrà creato l'indice.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
    // It ensures that Pascal-case property names in the model class are mapped to camel-case
    // field names in the index.
    [SerializePropertyNamesAsCamelCase]
    public class DemoIndex
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsSearchable, IsSortable]
        public string Id { get; set; }

        [IsSearchable]
        public string Content { get; set; }

        [IsSearchable]
        public string LanguageCode { get; set; }

        [IsSearchable]
        public string[] KeyPhrases { get; set; }

        [IsSearchable]
        public string[] Organizations { get; set; }
    }
}
```

<!-- Add the below model class definition to `DemoIndex.cs` and include it in the same namespace where you'll create the index.

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Cognitive Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public class DemoIndex
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsSearchable, IsSortable]
    public string Id { get; set; }

    [IsSearchable]
    public string Content { get; set; }

    [IsSearchable]
    public string LanguageCode { get; set; }

    [IsSearchable]
    public string[] KeyPhrases { get; set; }

    [IsSearchable]
    public string[] Organizations { get; set; }
}
``` -->

Dopo aver definito una classe modello, di nuovo in `Program.cs` è possibile creare una definizione di indice in modo molto semplice. Il nome di questo indice sarà `demoindex`. Se esiste già un indice con tale nome, verrà eliminato.

```csharp
private static Index CreateDemoIndex(SearchServiceClient serviceClient)
{
    var index = new Index()
    {
        Name = "demoindex",
        Fields = FieldBuilder.BuildForType<DemoIndex>()
    };

    try
    {
        bool exists = serviceClient.Indexes.Exists(index.Name);

        if (exists)
        {
            serviceClient.Indexes.Delete(index.Name);
        }

        serviceClient.Indexes.Create(index);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Durante i test è possibile che si tenti di creare l'indice più di una volta. Per questo motivo verificare se l'indice che si sta per creare esiste già prima di tentare di crearlo.

Aggiungere le righe seguenti in Main.

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Index demoIndex = CreateDemoIndex(serviceClient);
```

<!-- ```csharp
try
{
    bool exists = serviceClient.Indexes.Exists(index.Name);

    if (exists)
    {
        serviceClient.Indexes.Delete(index.Name);
    }

    serviceClient.Indexes.Create(index);
}
catch (Exception e)
{
    // Handle exception
}
```
 -->

Per altre informazioni sulla definizione di un indice, vedere [Creare un indice - API REST di Ricerca cognitiva di Azure](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Passaggio 4: Creare ed eseguire un indicizzatore

Finora sono stati creati un'origine dati, un set di competenze e un indice. Questi tre componenti diventano parte di un [indicizzatore](search-indexer-overview.md) che riunisce tutti i dati in un'unica operazione in più fasi. Per unire questi elementi in un indicizzatore, è necessario definire mapping dei campi.

+ Gli oggetti fieldMappings vengono elaborati prima del set di competenze, eseguendo il mapping dei campi di origine dall'origine dati ai campi di destinazione in un indice. Se i nomi e i tipi di campo sono uguali alle due estremità, non è necessario alcun mapping.

+ Gli oggetti outputFieldMappings vengono elaborati dopo il set di competenze facendo riferimento agli oggetti sourceFieldNames che non esistono fino a quando non vengono creati dall'individuazione o dall'arricchimento dei documenti. L'oggetto targetFieldName è un campo in un indice.

Oltre ad associare gli input agli output, è anche possibile usare i mapping dei campi per rendere flat le strutture dei dati. Per altre informazioni, vedere [Come eseguire il mapping dei campi migliorati a un indice ricercabile](cognitive-search-output-field-mapping.md).

```csharp
private static Indexer CreateDemoIndexer(SearchServiceClient serviceClient, DataSource dataSource, Skillset skillSet, Index index)
{
    IDictionary<string, object> config = new Dictionary<string, object>();
    config.Add(
        key: "dataToExtract",
        value: "contentAndMetadata");
    config.Add(
        key: "imageAction",
        value: "generateNormalizedImages");

    List<FieldMapping> fieldMappings = new List<FieldMapping>();
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")));
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content"));

    List<FieldMapping> outputMappings = new List<FieldMapping>();
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode"));

    Indexer indexer = new Indexer(
        name: "demoindexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        description: "Demo Indexer",
        skillsetName: skillSet.Name,
        parameters: new IndexingParameters(
            maxFailedItems: -1,
            maxFailedItemsPerBatch: -1,
            configuration: config),
        fieldMappings: fieldMappings,
        outputFieldMappings: outputMappings);

    try
    {
        bool exists = serviceClient.Indexers.Exists(indexer.Name);

        if (exists)
        {
            serviceClient.Indexers.Delete(indexer.Name);
        }

        serviceClient.Indexers.Create(indexer);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```
Aggiungere le righe seguenti in Main.

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

La creazione dell'indicizzatore richiederà del tempo. Anche se il set di dati è piccolo, le competenze analitiche prevedono un utilizzo elevato delle risorse di calcolo. Alcune competenze, ad esempio l'analisi delle immagini, hanno un'esecuzione prolungata.

> [!TIP]
> La creazione di un indicizzatore richiama la pipeline. Eventuali problemi a raggiungere i dati, per il mapping di input e output o nell'ordine delle operazioni vengono visualizzati in questa fase.

### <a name="explore-creating-the-indexer"></a>Esaminare la creazione dell'indicizzatore

Il codice imposta ```"maxFailedItems"``` su -1, che indica al motore di indicizzazione di ignorare gli errori durante l'importazione dei dati. Ciò è utile dato che l'origine dati di esempio include pochi documenti. Per un'origine dati più grande sarebbe necessario impostare un valore maggiore di 0.

Si noti inoltre che ```"dataToExtract"``` è impostato su ```"contentAndMetadata"```. Questa istruzione indica all'indicizzatore di estrarre automaticamente il contenuto dai vari formati di file, oltre ai metadati correlati a ogni file.

Quando viene estratto il contenuto, è possibile impostare `imageAction` per estrarre il testo dalle immagini trovate nell'origine dati. L'impostazione di ```"imageAction"``` su ```"generateNormalizedImages"```, unita alla competenza OCR e alla competenza di unione del testo, indica all'indicizzatore di estrarre il testo dalle immagini (ad esempio, la parola "stop" da un segnale stradale di stop) e incorporarlo come parte del campo del contenuto. Questo comportamento si applica sia alle immagini incorporate nei documenti, ad esempio un'immagine all'interno di un PDF, che alle immagini trovate nell'origine dati, ad esempio un file JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Monitorare l'indicizzazione

Dopo averlo definito, l'indicizzatore viene eseguito automaticamente quando si invia la richiesta. A seconda delle competenze cognitive definite, l'indicizzazione può richiedere più tempo del previsto. Per scoprire se l'indicizzatore è ancora in esecuzione, usare il metodo `GetStatus`.

```csharp
private static void CheckIndexerOverallStatus(SearchServiceClient serviceClient, Indexer indexer)
{
    try
    {
        IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Status)
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
    catch (Exception e)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", e.Message);
    }
}
```

`IndexerExecutionInfo` rappresenta lo stato corrente e la cronologia di esecuzione di un indicizzatore.

Gli avvisi sono comuni con alcune combinazioni di file di origine e competenze e non sempre indicano un problema. In questa esercitazione, gli avvisi sono innocui (ad esempio, nessun input di testo dai file JPEG).

Aggiungere le righe seguenti in Main.

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 - Eseguire ricerche

Al termine dell'indicizzazione, è possibile eseguire query che restituiscono il contenuto dei singoli campi. Per impostazione predefinita, Ricerca cognitiva di Azure restituisce i primi 50 risultati. I dati di esempio sono piccoli, quindi l'impostazione predefinita è appropriata. Tuttavia, quando si opera su set di dati più grandi, potrebbe essere necessario includere parametri nella stringa di query per restituire più risultati. Per istruzioni, vedere [Come impaginare i risultati della ricerca in Ricerca cognitiva di Azure](search-pagination-page-layout.md).

Come passaggio di verifica, eseguire una query sull'indice per tutti i campi.

Aggiungere le righe seguenti in Main.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` crea un nuovo oggetto `SearchIndexClient` usando i valori archiviati nel file config dell'applicazione (appsettings.json). Si noti che viene usata la chiave API della query del servizio di ricerca e non la chiave di amministrazione.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Aggiungere il codice seguente alla classe Main. La prima istruzione try-catch restituisce la definizione dell'indice, con il nome, il tipo e gli attributi di ogni campo. La seconda è una query con parametri, in cui `Select` specifica i campi da includere nei risultati, ad esempio `organizations`. Una stringa di ricerca di `"*"` restituisce tutto il contenuto di un singolo campo.

```csharp
//Verify content is returned after indexing is finished
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
    Console.WriteLine("First query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("First query failed\n Exception message: {0}\n", e.Message);
}

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
    Console.WriteLine("Second query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("Second query failed\n Exception message: {0}\n", e.Message);
}
```

Ripetere l'operazione per altri campi: content, languageCode, keyPhrases e organizations in questo esercizio. È possibile restituire più campi tramite la proprietà [Select](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) usando un elenco delimitato da virgole.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Reimpostare ed eseguire di nuovo

Nelle prime fasi sperimentali di sviluppo l'approccio più pratico per le iterazioni di progettazione consiste nell'eliminare gli oggetti da Ricerca cognitiva di Azure e consentire al codice di ricompilarli. I nomi di risorsa sono univoci. L'eliminazione di un oggetto consente di ricrearlo usando lo stesso nome.

Il codice di esempio per questa esercitazione verifica la presenza di oggetti esistenti e li elimina in modo da poter eseguire nuovamente il codice.

È anche possibile usare il portale per eliminare indici, indicizzatori, origini dati e set di competenze.

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
