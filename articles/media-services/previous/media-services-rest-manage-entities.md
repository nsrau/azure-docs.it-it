---
title: Gestione di entità di Servizi multimediali con REST | Documentazione Microsoft
description: Informazioni su come gestire entità di Servizi multimediali con l'API REST.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: ffbf30f2bfdf0a175513a8d2b9182b35c39f6aae
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58292449"
---
# <a name="managing-media-services-entities-with-rest"></a>Gestione di entità di Servizi multimediali con REST  

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Servizi multimediali di Microsoft Azure è un servizio REST basato su OData versione 3. È possibile aggiungere, interrogare, aggiornare ed eliminare entità come in qualsiasi altro servizio OData. Verranno chiamate le eccezioni, quando necessario. Per altre informazioni su OData, vedere la pagina relativa alla [documentazione del protocollo OData](https://www.odata.org/documentation/).

Questo argomento illustra come gestire le entità di Servizi multimediali di Azure con REST.

>[!NOTE]
> A partire dal 1° aprile 2017, tutti i record di processo presenti nell'account e più vecchi di 90 giorni verranno eliminati automaticamente, insieme ai record attività associati, anche se il numero totale di record è inferiore alla quota massima. Ad esempio, il 1° aprile 2017 qualsiasi record di processo nell'account precedente il 31 dicembre 2016 verrà automaticamente eliminato. Se è necessario archiviare le informazioni sul processo o sull'attività, è possibile usare il codice descritto in questo argomento.

## <a name="considerations"></a>Considerazioni  

Quando si accede alle entità in Servizi multimediali, è necessario impostare valori e campi di intestazione specifici nelle richieste HTTP. Per altre informazioni, vedere [Panoramica dell'API REST di Servizi multimediali](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Connettersi a Servizi multimediali

Per informazioni su come connettersi all'API AMS, vedere [Accedere all'API di Servizi multimediali di Azure con l'autenticazione di Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Aggiunta di entità
Ogni entità in Servizi multimediali viene aggiunta a un set di entità, ad esempio asset, mediante una richiesta HTTP POST.

Il seguente esempio mostra come creare un'entità AccessPolicy.

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>Query di entità
L'interrogazione e la visualizzazione delle entità è un'operazione semplice che prevede solo una richiesta HTTP GET e operazioni OData facoltative.
Il seguente esempio recupera un elenco di tutte le entità MediaProcessor.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

È inoltre possibile recuperare un'entità specifica o tutti gli insiemi di entità associati a un'entità specifica, come nei seguenti esempi:

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Il seguente esempio restituisce solo la proprietà State di tutti i processi.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

L'esempio seguente restituisce tutti gli oggetti JobTemplates con nome "SampleTemplate".

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> L'operazione $expand non è supportata in Servizi multimediali così come i metodi LINQ non supportati descritti in Considerazioni su LINQ (WCF Data Services).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Enumerazione di grandi raccolte di entità
Quando si esegue una query di entità, è previsto un limite di 1000 entità restituite in una sola volta perché la versione 2 pubblica di REST limita i risultati della query a 1000 risultati. Usare gli elementi **skip** e **top** per enumerare raccolte di entità di grandi dimensioni. 

L'esempio seguente illustra come usare **skip** e **top** per ignorare i primi 2000 processi e ottenere i 1000 processi successivi.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Aggiornamento di entità
A seconda del tipo di entità e del relativo stato, è possibile aggiornare le proprietà corrispondenti mediante una richiesta HTTP PATCH, PUT o MERGE. Per altre informazioni su queste operazioni, vedere [PATCH/PUT/MERGE](https://msdn.microsoft.com/library/dd541276.aspx).

Il seguente esempio di codice illustra come aggiornare la proprietà Name in un'entità Asset.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>Eliminazione di entità
Le entità possono essere eliminate in Servizi multimediali usando una richiesta HTTP DELETE. A seconda dell'entità, l'ordine con cui vengono eliminate può essere importante. Entità come Asset ad esempio richiedono la revoca o eliminazione di tutti i localizzatori che fanno riferimento a tali entità prima dell'eliminazione.

Il seguente esempio illustra come eliminare un localizzatore usato per caricare un file nella risorsa di archiviazione BLOB.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

