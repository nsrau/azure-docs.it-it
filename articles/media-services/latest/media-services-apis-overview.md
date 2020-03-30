---
title: Sviluppare con le API v3Develop with v3 APIs
titleSuffix: Azure Media Services
description: Informazioni sulle regole che si applicano alle entità e alle API durante lo sviluppo con Servizi multimediali v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: eacdfe8211c97e75b6609f5e11b681f84ae55846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472085"
---
# <a name="develop-with-media-services-v3-apis"></a>Sviluppare con le API di Servizi multimediali v3Develop with Media Services v3 APIs

Gli sviluppatori possono usare [l'API REST](https://docs.microsoft.com/rest/api/media/) di Servizi multimediali o le librerie client che consentono di interagire con l'API REST per creare, gestire e gestire facilmente flussi di lavoro multimediali personalizzati. L'API [di Servizi multimediali v3](https://aka.ms/ams-v3-rest-sdk) si basa sulla specifica OpenAPI (precedentemente nota come Swagger).

Questo articolo illustra le regole che si applicano alle entità e alle API quando si sviluppa con Servizi multimediali v3.

## <a name="accessing-the-azure-media-services-api"></a>Accesso all'API di Servizi multimediali di AzureAccessing the Azure Media Services API

Per essere autorizzati ad accedere alle risorse e all'API di Servizi multimediali, è innanzitutto necessario essere autenticati. Servizi multimediali supporta l'autenticazione [basata su Azure Active Directory (Azure AD).](../../active-directory/fundamentals/active-directory-whatis.md) Due opzioni di autenticazione comuni sono:
 
* **Autenticazione dell'entità servizio:** usato per autenticare un servizio (ad esempio: app Web, app per funzioni, app per la logica, API e microservizi). Le applicazioni che in genere usano questo metodo di autenticazione sono app che eseguono servizi daemon, servizi di livello intermedio o processi pianificati. Ad esempio, per le app Web deve essere sempre presente un livello intermedio che si connette a Servizi multimediali con un'entità servizio.
* **Autenticazione utente:** utilizzata per autenticare una persona che usa l'app per interagire con le risorse di Servizi multimediali. L'app interattiva deve prima richiedere all'utente le credenziali dell'utente. Un esempio è un'app della console di gestione usata dagli utenti autorizzati per monitorare i processi di codifica o lo streaming live.

L'API Servizi multimediali richiede che l'utente o l'app che effettua le richieste dell'API REST abbia accesso alla risorsa account di Servizi multimediali e utilizzi un ruolo **Collaboratore** o **Proprietario.** È possibile accedere all'API con il ruolo **Lettore,** ma saranno disponibili solo le operazioni **Get** o **List.**Per ulteriori informazioni, vedere Controllo degli [accessi in base](rbac-overview.md)al ruolo per gli account di Servizi multimediali .

Invece di creare un'entità servizio, è consigliabile usare le identità gestite per le risorse di Azure per accedere all'API di Servizi multimediali tramite Azure Resource Manager.Instead of creating a service principal, consider using managed identities for Azure resources to access the Media Services API through Azure Resource Manager. Per altre informazioni sulle identità gestite per le risorse di Azure, vedere [Che cos'è le identità gestite per le risorse](../../active-directory/managed-identities-azure-resources/overview.md)di Azure.To learn more about managed identities for Azure resources, see What is managed identities for Azure resources.

### <a name="azure-ad-service-principal"></a>Entità servizio di Azure ADAzure AD service principal

Se si sta creando un'app Azure AD e un'entità servizio, l'app deve trovarsi nel proprio tenant. Dopo aver creato l'app, concedere all'app l'accesso al ruolo **Collaboratore** o **Proprietario** all'account Servizi multimediali.

Se non si è certi di disporre delle autorizzazioni per creare un'app Azure AD, vedere [Autorizzazioni necessarie](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Nella figura seguente, i numeri rappresentano il flusso delle richieste in ordine cronologico:

![Autenticazione dell'app di livello intermedio con AAD da un'API Web](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Un'app di livello intermedio richiede un token di accesso di Azure AD con i parametri seguenti:A middle-tier app requests an Azure AD access token that has the following parameters:  

   * Endpoint tenant di Azure AD.
   * URI di risorsa per Servizi multimediali.
   * URI di risorsa per Servizi multimediali REST.
   * Valori dell'app Azure AD: l'ID client e il segreto client.

   Per ottenere tutti i valori necessari, vedere Accedere all'API di Servizi multimediali di [Azure con l'interfaccia della riga di comando](access-api-cli-how-to.md)di Azure.

2. Il token di accesso di Azure AD viene inviato al livello intermedio.
4. Il livello intermedio invia una richiesta all'API REST di Servizi multimediali di Azure con il token di Azure AD.
5. Il livello intermedio ottiene nuovamente i dati da Servizi multimediali.

### <a name="samples"></a>Esempi

Vedere gli esempi seguenti che illustrano come connettersi con l'entità servizio di Azure AD:See the following samples that show how to connect with Azure AD service principal:

* [Connettiti con REST](media-rest-apis-with-postman.md)  
* [Connettersi con Java](configure-connect-java-howto.md)
* [Connettersi con .NET](configure-connect-dotnet-howto.md)
* [Connettersi con Node. js](configure-connect-nodejs-howto.md)
* [Connettersi con Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Convenzioni di denominazione

I nomi delle risorse di Servizi multimediali di Azure v3 (ad esempio, asset, processi e trasformazioni) sono soggetti ai vincoli di denominazione di Azure Resource Manager. In conformità con Azure Resource Manager, i nomi delle risorse sono sempre univoci. Di conseguenza, per i nomi delle risorse è possibile usare qualsiasi stringa di identificatore univoco (ad esempio, GUID).

I nomi delle risorse di Servizi multimediali non possono includere: '<', '>', '%', '&', ':', '&#92;', '?', '/', ''', ''', ''', '.', il carattere virgoletta singola o qualsiasi carattere di controllo. Sono consentiti tutti gli altri caratteri. La lunghezza massima di un nome di risorsa è di 260 caratteri.

Per altre informazioni sulla denominazione di Azure Resource Manager, vedere [Requisiti di denominazione](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e Convenzioni di [denominazione.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

### <a name="names-of-filesblobs-within-an-asset"></a>Nomi di file/BLOB all'interno di una risorsa

I nomi dei file/BLOB all'interno di un asset devono rispettare sia [i requisiti](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) del nome del BLOB che i requisiti dei nomi [NTFS.](https://docs.microsoft.com/windows/win32/fileio/naming-a-file) Il motivo di questi requisiti è che i file possono essere copiati dall'archiviazione BLOB in un disco NTFS locale per l'elaborazione.

## <a name="long-running-operations"></a>Operazioni a esecuzione prolungata

Le operazioni `x-ms-long-running-operation` contrassegnate con nei [file swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) di Servizi multimediali di Azure sono operazioni a esecuzione prolungata. 

Per informazioni dettagliate su come tenere traccia delle operazioni asincrone di Azure, vedere [Operazioni asincrone](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Servizi multimediali ha le seguenti operazioni a esecuzione prolungata:

* [Creazione di eventi live](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Aggiorna eventi live](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Elimina evento dal vivo](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Inizia l'evento dal vivo](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Interrompi LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Utilizzate `removeOutputsOnStop` il parametro per eliminare tutti gli output live associati quando interrompete l'evento.  
* [Reimposta LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Creare LiveOutputCreate LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Elimina LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Creare un'entità StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Aggiorna StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Elimina StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Avviare un'entità StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Interrompi StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [StreamingsuE StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Al termine dell'invio di un'operazione lunga, viene visualizzato un '202 Accepted' ed è necessario eseguire il polling per il completamento dell'operazione utilizzando l'ID dell'operazione restituito.

L'articolo [Tenere traccia delle operazioni asincrone](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) spiega in maniera approfondita come tenere traccia dello stato delle operazioni asincrone di Azure tramite i valori restituiti nella risposta.

È supportata una sola operazione a esecuzione prolungata per un determinato evento live o per uno degli output live associati. Una volta avviata, un'operazione a esecuzione prolungata deve essere completata prima di avviare un'operazione successiva a esecuzione prolungata sullo stesso LiveEvent o su qualsiasi output dinamico associato. Per gli eventi live con più output live, è necessario attendere il completamento di un'operazione a esecuzione prolungata su un'operazione Live Output prima di attivare un'operazione a esecuzione prolungata su un altro Live Output. 

## <a name="sdks"></a>SDK

> [!NOTE]
> Non è garantito che gli SDK di Servizi multimediali di Azure siano thread-safe. Quando si sviluppa un'app multithreading, è necessario aggiungere la propria logica di sincronizzazione dei thread per proteggere il client o usare un nuovo oggetto AzureMediaServicesClient per thread. È anche necessario prestare attenzione ai problemi di multithreading introdotti da oggetti facoltativi forniti dal codice al client (ad esempio, un'istanza di HttpClient in .NET).

|SDK|Riferimento|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[Informazioni di riferimento su .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Informazioni di riferimento su Java](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Informazioni di riferimento su Python](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Informazioni di riferimento su Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Informazioni di riferimento su Go](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Vedere anche

- [EventGrid .NET SDK che include gli eventi per Servizi multimediali](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definizioni degli eventi di Servizi multimediali](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Explorer di Servizi multimediali di Azure

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) è uno strumento disponibile per i clienti di Windows che vogliono informazioni su Servizi multimediali. AMSE è un'applicazione Winforms/C# che esegue operazioni di caricamento, download, codifica, streaming di VOD e contenuti live con Servizi multimediali. Lo strumento AMSE è stato pensato per i clienti che vogliono testare Servizi multimediali senza scrivere codice. Il codice AMSE viene fornito come risorsa per i clienti che vogliono sviluppare con Servizi multimediali.

AMSE è un progetto open source il cui supporto viene fornito dalla community (i problemi possono essere segnalati a https://github.com/Azure/Azure-Media-Services-Explorer/issues). Questo progetto ha adottato il [Codice di comportamento di Microsoft per l'open source](https://opensource.microsoft.com/codeofconduct/). Per ulteriori informazioni, consulta le domande opencode@microsoft.com frequenti sul Codice di [condotta](https://opensource.microsoft.com/codeofconduct/faq/) o contatta altre domande o commenti.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali

Vedere [Filtro, ordinamento, paging delle entità di Servizi multimediali di Azure.](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="see-also"></a>Vedere anche

[Interfaccia della riga di comando di AzureAzure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi a Servizi multimediali con Java](configure-connect-java-howto.md)
* [Connettersi a Servizi multimediali con .NET](configure-connect-dotnet-howto.md)
* [Connettersi a Servizi multimediali con Node.js](configure-connect-nodejs-howto.md)
* [Connettersi a Servizi multimediali con Python](configure-connect-python-howto.md)
