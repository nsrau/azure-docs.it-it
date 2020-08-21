---
title: Sviluppare con le API di v3
titleSuffix: Azure Media Services
description: Informazioni sulle regole applicabili a entità e API durante le attività di sviluppo con Servizi multimediali v3.
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
ms.openlocfilehash: dcec4d0bc93433c768faf10e4520131aeecc5e77
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719190"
---
# <a name="develop-with-media-services-v3-apis"></a>Sviluppare con le API di Servizi multimediali v3

Gli sviluppatori possono usare l'[API REST](/rest/api/media/) Servizi multimediali o le librerie client che consentono di interagire con l'API REST per creare, gestire e mantenere facilmente flussi di lavoro multimediali personalizzati. L'API [Servizi multimediali v3](https://aka.ms/ams-v3-rest-sdk) è basata sulla specifica OpenAPI (in precedenza nota come Swagger).

Questo articolo descrive le regole applicabili alle entità e alle API quando vengono eseguite attività di sviluppo con Servizi multimediali v3.

## <a name="accessing-the-azure-media-services-api"></a>Accesso all'API di Servizi multimediali di Azure

Per essere autorizzati ad accedere alle risorse e all'API di Servizi multimediali, è innanzitutto necessario essere autenticati. Servizi multimediali supporta l'autenticazione [basata su Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md). Esistono due opzioni di autenticazione comuni:
 
* **Autenticazione tramite entità servizio**: usata per autenticare un servizio, ad esempio app Web, app per le funzioni, app per la logica, API e microservizi. Le applicazioni che in genere usano questo metodo di autenticazione sono app che eseguono servizi daemon, servizi di livello intermedio o processi pianificati. Per le app Web, ad esempio, deve essere sempre presente un livello intermedio che si connette a Servizi multimediali con un'entità servizio.
* **Autenticazione utente**: usata per autenticare una persona che usa l'app per interagire con le risorse di Servizi multimediali. L'app interattiva deve prima richiedere all'utente le credenziali. Un esempio è un'app della console di gestione usata dagli utenti autorizzati per monitorare i processi di codifica o lo streaming live.

Per l'API di Servizi multimediali è necessario che l'utente o l'app che effettua le richieste all'API REST possa accedere alla risorsa dell'account di Servizi multimediali e usi un ruolo **Collaboratore** o **Proprietario**. È possibile accedere all'API con il ruolo **Lettore**, ma saranno disponibili solo operazioni **Get** o **List**. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo per gli account di Servizi multimediali](rbac-overview.md).

Invece di creare un'entità servizio, è consigliabile usare identità gestite per le risorse di Azure per accedere all'API di Servizi multimediali tramite Azure Resource Manager. Per altre informazioni sulle identità gestite per le risorse di Azure, vedere [Informazioni sulle identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Entità servizio di Azure AD

Per creare un'app e un'entità servizio di Azure AD, l'app deve trovarsi nel proprio tenant. Dopo aver creato l'app, concedere al ruolo **Collaboratore** o **Proprietario** dell'app l'accesso all'account di Servizi multimediali.

Se non si è certi di avere le autorizzazioni necessarie per creare un'app Azure AD, vedere [Autorizzazioni necessarie](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Nella figura seguente i numeri rappresentano il flusso delle richieste in ordine cronologico:

![Autenticazione di app di livello intermedio con AAD da un'API Web](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Un'app di livello intermedio richiede un token di accesso di Azure AD con i parametri seguenti:  

   * Endpoint tenant di Azure AD.
   * URI di risorsa per Servizi multimediali.
   * URI di risorsa per Servizi multimediali REST.
   * Valori dell'app Azure AD: ID client e segreto client.

   Per ottenere tutti i valori necessari, vedere [Accedere all'API di Servizi multimediali di Azure](./access-api-howto.md).

2. Il token di accesso di Azure AD viene inviato al livello intermedio.
4. Il livello intermedio invia una richiesta all'API REST di Servizi multimediali di Azure con il token di Azure AD.
5. Il livello intermedio ottiene nuovamente i dati da Servizi multimediali.

### <a name="samples"></a>Esempi

Vedere gli esempi seguenti che illustrano come connettersi con l'entità servizio di Azure AD:

* [Connettersi con REST](media-rest-apis-with-postman.md)  
* [Connettersi con Java](configure-connect-java-howto.md)
* [Connettersi con .NET](configure-connect-dotnet-howto.md)
* [Connettersi con Node.js](configure-connect-nodejs-howto.md)
* [Connettersi con Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Convenzioni di denominazione

I nomi delle risorse di Servizi multimediali di Azure v3 (ad esempio, asset, processi e trasformazioni) sono soggetti ai vincoli di denominazione di Azure Resource Manager. In conformità con Azure Resource Manager, i nomi delle risorse sono sempre univoci. Di conseguenza, per i nomi delle risorse è possibile usare qualsiasi stringa di identificatore univoco (ad esempio, GUID).

I nomi delle risorse di Servizi multimediali non possono includere "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", virgolette singole o caratteri di controllo. Sono consentiti tutti gli altri caratteri. La lunghezza massima di un nome di risorsa è di 260 caratteri.

Per altre informazioni sulla denominazione di Azure Resource Manager, vedere [Requisiti di denominazione](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [Convenzioni di denominazione](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Nomi di file/BLOB all'interno di un asset

I nomi di file/BLOB all'interno di un asset devono rispettare i [requisiti del nome del BLOB](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) e i [requisiti del nome NTFS](/windows/win32/fileio/naming-a-file). Questi requisiti sono necessari perché i file possano essere copiati dall'archiviazione BLOB in un disco NTFS locale per l'elaborazione.

## <a name="long-running-operations"></a>Operazioni a esecuzione prolungata

Le operazioni contrassegnate con `x-ms-long-running-operation` nei [file Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) di Servizi multimediali di Azure sono a esecuzione prolungata. 

Per informazioni dettagliate su come tenere traccia delle operazioni asincrone di Azure, vedere [Operazioni asincrone](../../azure-resource-manager/management/async-operations.md).

Servizi multimediali include le seguenti operazioni a esecuzione prolungata:

* [Creare un evento live](/rest/api/media/liveevents/create)
* [Aggiornare un evento live](/rest/api/media/liveevents/update)
* [Eliminare un evento live](/rest/api/media/liveevents/delete)
* [Avviare un evento live](/rest/api/media/liveevents/start)
* [Arrestare un evento live](/rest/api/media/liveevents/stop)

  Usare il parametro `removeOutputsOnStop` per eliminare tutti gli output live associati quando si arresta l'evento.  
* [Reimpostare un evento live](/rest/api/media/liveevents/reset)
* [Creare un output live](/rest/api/media/liveevents/create)
* [Eliminare un output live](/rest/api/media/liveevents/delete)
* [Creare un'entità StreamingEndpoint](/rest/api/media/streamingendpoints/create)
* [Aggiornare un'entità StreamingEndpoint](/rest/api/media/streamingendpoints/update)
* [Eliminare un'entità StreamingEndpoint](/rest/api/media/streamingendpoints/delete)
* [Avviare un'entità StreamingEndpoint](/rest/api/media/streamingendpoints/start)
* [Arrestare un'entità StreamingEndpoint](/rest/api/media/streamingendpoints/stop)
* [Dimensionare un'entità StreamingEndpoint](/rest/api/media/streamingendpoints/scale)

Dopo l'invio di un'operazione di lunga durata, viene visualizzato un messaggio "202 Accettato" ed è necessario eseguire il polling dell'operazione completata usando l'ID operazione restituito.

L'articolo [Tenere traccia delle operazioni asincrone](../../azure-resource-manager/management/async-operations.md) spiega in maniera approfondita come tenere traccia dello stato delle operazioni asincrone di Azure tramite i valori restituiti nella risposta.

Per un determinato evento live o per qualsiasi output live associato è supportata una sola operazione a esecuzione prolungata. Dopo l'avvio, un'operazione a esecuzione prolungata deve essere completata prima di avviare una successiva operazione a esecuzione prolungata per lo stesso evento live o per qualsiasi output live associato. Per gli eventi live con più output live, è necessario attendere il completamento di un'operazione a esecuzione prolungata per un output live prima di attivare un'operazione a esecuzione prolungata per un altro output live. 

## <a name="sdks"></a>SDK

> [!NOTE]
> Gli SDK di Servizi multimediali di Azure v3 non sono garantiti come thread-safe. Quando si sviluppa un'app multithreading, è necessario aggiungere la propria logica di sincronizzazione thread per proteggere il client oppure usare un nuovo oggetto AzureMediaServicesClient per ogni thread. È anche necessario prestare attenzione ai problemi di multithreading introdotti da oggetti facoltativi forniti dal codice al client (ad esempio, un'istanza di HttpClient in .NET).

|SDK|Informazioni di riferimento|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[Informazioni di riferimento su .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[SDK per Java](https://aka.ms/ams-v3-java-sdk)|[Informazioni di riferimento su Java](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Informazioni di riferimento su Python](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Informazioni di riferimento su Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Informazioni di riferimento su Go](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Vedere anche

- [EventGrid .NET SDK che include gli eventi per Servizi multimediali](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definizioni degli eventi di Servizi multimediali](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Explorer di Servizi multimediali di Azure

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) è uno strumento disponibile per i clienti di Windows che vogliono informazioni su Servizi multimediali. AMSE è un'applicazione Winforms/C# che esegue operazioni di caricamento, download, codifica, streaming di VOD e contenuti live con Servizi multimediali. Lo strumento AMSE è stato pensato per i clienti che vogliono testare Servizi multimediali senza scrivere codice. Il codice AMSE viene fornito come risorsa per i clienti che vogliono sviluppare con Servizi multimediali.

AMSE è un progetto open source il cui supporto viene fornito dalla community (i problemi possono essere segnalati a https://github.com/Azure/Azure-Media-Services-Explorer/issues). Questo progetto ha adottato il [Codice di comportamento di Microsoft per l'open source](https://opensource.microsoft.com/codeofconduct/). Per altre informazioni, vedere [Domande frequenti sul codice di comportamento](https://opensource.microsoft.com/codeofconduct/faq/) oppure scrivere a opencode@microsoft.com per qualsiasi altro commento o domanda.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali

Vedere [Applicazione di filtri, ordinamento e paging delle entità di Servizi multimediali di Azure](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="see-also"></a>Vedere anche

Per ottenere tutti i valori necessari, vedere [Accedere all'API di Servizi multimediali di Azure](./access-api-howto.md).

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi a Servizi multimediali con Java](configure-connect-java-howto.md)
* [Connettersi a Servizi multimediali con .NET](configure-connect-dotnet-howto.md)
* [Connettersi a Servizi multimediali con Node.js](configure-connect-nodejs-howto.md)
* [Connettersi a Servizi multimediali con Python](configure-connect-python-howto.md)
