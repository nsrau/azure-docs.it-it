---
title: 'Avvio rapido: Libreria client di gestione di Azure per .NET'
description: Questo argomento di avvio rapido contiene un'introduzione alla libreria client di gestione di Azure per .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 93ea0ffc94b78e014b30ab1d45d589eba50fe524
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607626"
---
[Documentazione di riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/) | [Esempi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida: [crearne una gratuitamente](https://azure.microsoft.com/free/).
* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-an-azure-service-principal"></a>Creare un'entità servizio di Azure

Per fare in modo che l'applicazione interagisca con l'account Azure, è necessaria un'entità servizio di Azure per gestire le autorizzazioni. Seguire le istruzioni riportate in [Creare un'entità servizio di Azure](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Quando si crea un'entità servizio, si noterà che include un valore segreto, un ID e un ID applicazione. Salvare l'ID applicazione e il segreto in un percorso temporaneo per i passaggi successivi.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per creare una risorsa di Servizi cognitivi, è necessario che l'account includa un gruppo di risorse di Azure in cui contenerla. Se non si ha già un gruppo di risorse, crearne uno nel [portale di Azure](https://ms.portal.azure.com/) prima di continuare.

## <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

Creare una nuova applicazione .NET Core. In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `azure-management-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un unico file di origine: *program.cs*. 

```console
dotnet new console -n azure-management-quickstart
```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

```console
dotnet build
```

L'output di compilazione non deve contenere alcun avviso o errore. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installare la libreria client

Nella directory dell'applicazione installare la libreria client di gestione di Azure per .NET con il comando seguente:

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Se si usa l'ambiente di sviluppo integrato di Visual Studio, la libreria client è disponibile come pacchetto NuGet scaricabile.

### <a name="import-libraries"></a>Importare le librerie

Aprire *program.cs* e aggiungere le istruzioni `using` seguenti all'inizio del file:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Autenticare il client

Aggiungere i campi seguenti alla radice di *program.cs* e inserire i relativi valori usando l'entità servizio creata e le informazioni sull'account Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

Quindi, nel metodo **Main**, usare questi valori per creare un oggetto **CognitiveServicesManagementClient**. Questo oggetto è necessario per tutte le operazioni di gestione di Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>Chiamare i metodi di gestione

Aggiungere il codice seguente alla fine del metodo **Main** per elencare le risorse disponibili, creare una risorsa di esempio, elencare le risorse di cui si è proprietari e quindi eliminare la risorsa di esempio. Questi metodi verranno definiti nei passaggi seguenti.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

### <a name="choose-a-service-and-pricing-tier"></a>Scegliere un servizio e un piano tariffario

Quando si crea una nuova risorsa, è necessario conoscere il tipo di servizio da usare, oltre al [piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/) (o SKU) desiderato. Questa e altre informazioni verranno usate come parametri per la creazione della risorsa. È possibile trovare un elenco di tipi di servizi cognitivi disponibili chiamando il metodo seguente nello script:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

Vedere l'elenco degli SKU e le informazioni sui prezzi di seguito. 

#### <a name="multi-service"></a>Multi-servizio

| Servizio                    | Tipo                      |
|----------------------------|---------------------------|
| Più servizi. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/).            | `CognitiveServices`     |


#### <a name="vision"></a>Visione

| Servizio                    | Tipo                      |
|----------------------------|---------------------------|
| Visione artificiale            | `ComputerVision`          |
| Visione personalizzata - Previsione | `CustomVision.Prediction` |
| Visione personalizzata - Training   | `CustomVision.Training`   |
| Viso                       | `Face`                    |
| Riconoscimento modulo            | `FormRecognizer`          |
| Riconoscimento input penna             | `InkRecognizer`           |

#### <a name="search"></a>Ricerca

| Servizio            | Tipo                  |
|--------------------|-----------------------|
| Suggerimenti automatici Bing   | `Bing.Autosuggest.v7` |
| Ricerca personalizzata Bing | `Bing.CustomSearch`   |
| Ricerca entità Bing | `Bing.EntitySearch`   |
| Ricerca Bing        | `Bing.Search.v7`      |
| Controllo ortografico Bing   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Voce

| Servizio            | Tipo                 |
|--------------------|----------------------|
| Servizi Voce    | `SpeechServices`     |
| Riconoscimento vocale | `SpeakerRecognition` |

#### <a name="language"></a>Linguaggio

| Servizio            | Tipo                |
|--------------------|---------------------|
| Comprensione modulo | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Text Analytics     | `TextAnalytics`     |
| Traduzione testuale   | `TextTranslation`   |

#### <a name="decision"></a>Decisione

| Servizio           | Tipo               |
|-------------------|--------------------|
| Rilevamento anomalie  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizza esperienze      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Piani tariffari e fatturazione

I piani tariffari (e l'importo fatturato) si basano sul numero di transazioni inviate usando le informazioni di autenticazione. Ogni piano tariffario specifica:
* Il numero massimo di transazioni consentite al secondo.
* Le funzionalità del servizio abilitate all'interno del piano tariffario.
* Il costo per un numero predefinito di transazioni. Il superamento di questo numero determinerà un addebito aggiuntivo, come specificato nei [dettagli dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) per il servizio.

> [!NOTE]
> Molti servizi cognitivi prevedono un livello gratuito che è possibile usare per provarli. Per usufruire del livello gratuito, usare `F0` come SKU per la risorsa.

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

Per creare e sottoscrivere una nuova risorsa di Servizi cognitivi, usare il metodo **Crea**. Questo metodo aggiunge una nuova risorsa fatturabile al gruppo di risorse che viene passato. Quando si crea una nuova risorsa, è necessario conoscere il tipo di servizio che si vuole usare, oltre al piano tariffario (o SKU) e a una località di Azure. Il metodo seguente accetta tutti questi argomenti e crea una risorsa.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

## <a name="view-your-resources"></a>Visualizzare le risorse

Per visualizzare tutte le risorse nell'account Azure (in tutti i gruppi di risorse), usare il metodo seguente:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>Eliminare una risorsa

Il metodo seguente elimina la risorsa specificata dal gruppo di risorse specificato.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```dotnet
dotnet run
```

## <a name="see-also"></a>Vedere anche

* [Documentazione di riferimento Azure Management SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet)
* [Informazioni su Servizi cognitivi di Azure](../../Welcome.md)
* [Autenticare le richieste a Servizi cognitivi di Azure](../../authentication.md)
* [Creare una nuova risorsa con il portale di Azure](../../cognitive-services-apis-create-account.md)