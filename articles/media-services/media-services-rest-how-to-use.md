---
title: Informazioni generali sull'API REST di Servizi multimediali | Microsoft Docs
description: Informazioni generali sull'API REST di Servizi multimediali
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/05/2017
ms.author: juliako;johndeu
ms.openlocfilehash: 066959058576af830103aa98a12f0c36acfdbb14
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="media-services-operations-rest-api-overview"></a>Informazioni generali sull'API REST di Servizi multimediali
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Il **REST operazioni di servizi multimediali** API viene usata per la creazione di processi, asset, i canali Live e altre risorse in un account di servizi multimediali. Per altre informazioni, vedere le [informazioni di riferimento sull'API REST di Servizi multimediali](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Servizi multimediali fornisce un'API REST che accetta sia JSON o atom + pub formato XML. API REST di servizi multimediali richiede le intestazioni HTTP specifiche che ogni client deve inviare quando la connessione a servizi multimediali, nonché un set di intestazioni facoltative. Le seguenti sezioni descrivono le intestazioni e i verbi HTTP che è possibile usare quando si creano richieste e si ricevono risposte da Servizi multimediali.

L'autenticazione per l'API REST di servizi multimediali viene eseguita tramite l'autenticazione di Azure Active Directory in cui è descritto nell'articolo [autenticazione usano Azure AD per accedere all'API di servizi multimediali di Azure con REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Considerazioni

Quando si usa REST, si applicano le considerazioni seguenti:

* Quando si esegue una query di entità, è previsto un limite di 1000 entità restituite in una sola volta perché la versione 2 pubblica di REST limita i risultati della query a 1000 risultati. È necessario usare **Skip** e **Take** (.NET)/**top** (REST) come descritto in [questo esempio .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e in [questo esempio di API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Quando usando JSON e specificando per l'utilizzo di **Metadata** (parola chiave) nella richiesta (ad esempio, per i riferimenti di un oggetto collegato) è necessario impostare il **Accept** intestazione [formato JSON dettagliato](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)(vedere l'esempio seguente). OData non riconosce la proprietà **__metadata** nella richiesta, a meno che non venga impostata su verbose.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.17
        Authorization: Bearer <token> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Intestazioni delle richieste HTTP standard supportate da Servizi multimediali
Per ogni chiamata effettuata in Servizi multimediali, è necessario includere nella richiesta un set di intestazioni obbligatorie ed eventualmente un set di intestazioni facoltative. Nella tabella seguente sono elencate le intestazioni obbligatorie:

| Intestazione | type | Valore |
| --- | --- | --- |
| Authorization |Bearer |Bearer è l'unico meccanismo di autorizzazione accettato. Il valore deve includere anche il token di accesso fornito da Azure Active Directory. |
| x-ms-version |Decimal |2.17 (o versione più recente)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Poiché servizi multimediali Usa OData per esporre le relative API REST, le intestazioni DataServiceVersion e MaxDataServiceVersion devono essere incluse in tutte le richieste. Tuttavia, se non lo sono, quindi attualmente servizi multimediali suppone che il valore di DataServiceVersion in uso sia 3.0.
> 
> 

Nella seguente tabella è riportato un set di intestazioni facoltative:

| Intestazione | type | Valore |
| --- | --- | --- |
| Data |Data RFC 1123 |Timestamp della richiesta. |
| Accept |Tipo di contenuto |Tipo di contenuto richiesto per la risposta, ad esempio:<p> -application/json;odata=verbose<p> - application/atom+xml<p> Le risposte potrebbero essere un tipo di contenuto diversi, ad esempio un'operazione di recupero di blob, in cui una risposta corretta contiene il flusso di dati blob come payload. |
| Accept-Encoding |Gzip, deflate |Codifica GZIP e DEFLATE, se applicabile. Nota: in caso di risorse di grandi dimensioni, Servizi multimediali può ignorare questa intestazione e restituire dati non compressi. |
| Accept-Language |"en", "es" e così via. |Lingua preferita per la risposta. |
| Accept-Charset |Tipo di set di caratteri, ad esempio "UTF-8" |L'impostazione predefinita è UTF-8. |
| X-HTTP-Method |Metodo HTTP |Consente ai client o ai firewall che non supportano metodi HTTP come PUT o DELETE di usarli, con tunneling tramite una chiamata GET. |
| Content-Type |Tipo di contenuto |Tipo di contenuto del corpo delle richieste PUT o POST. |
| client-request-id |string |Valore definito dal chiamante che identifica la richiesta fornita. Se specificato, questo valore viene incluso nel messaggio di risposta per consentire il mapping della richiesta. <p><p>**Importante**<p>Le dimensioni di questi valori dovrebbero essere limitate a 2096 b (2 k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Intestazioni delle risposte HTTP standard supportate da Servizi multimediali
Nella seguente tabella è riportato un set di intestazioni che possono essere restituite a seconda della risorsa richiesta e dell'azione che si intende eseguire.

| Intestazione | type | Valore |
| --- | --- | --- |
| request-id |string |Identificatore univoco per l'operazione corrente, generato dal servizio. |
| client-request-id |string |Identificatore specificato dal chiamante nella richiesta originale, se presente. |
| Data |Data RFC 1123 |Data e ora che è stata elaborata la richiesta. |
| Content-Type |Variabile |Tipo di contenuto del corpo della risposta. |
| Content-Encoding |Variabile |Gzip o deflate, a seconda delle esigenze. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Verbi HTTP standard supportati da Servizi multimediali
Nella seguente tabella è riportato l'elenco completo dei verbi HTTP che è possibile usare per la creazione di richieste HTTP:

| Verbo | DESCRIZIONE |
| --- | --- |
| GET |Restituisce il valore corrente di un oggetto. |
| POST |Crea un oggetto in base ai dati forniti o invia un comando. |
| PUT |Sostituisce un oggetto o ne crea uno nuovo con nome, se applicabile. |
| DELETE |Elimina un oggetto. |
| MERGE |Aggiorna un oggetto esistente con le modifiche alle proprietà denominate. |
| HEAD |Restituisce i metadati di un oggetto per una risposta GET. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Individuare e selezionare il modello di entità di servizi multimediali
Per rendere più facilmente individuabili le entità di Servizi multimediali, è possibile usare l'operazione $metadata, che consente di recuperare tutti i tipi validi di entità, le proprietà di entità, le associazioni, le funzioni, le azioni e così via. Aggiungendo l'operazione $metadata alla fine dell'endpoint API REST di servizi multimediali, è possibile accedere a questo servizio di individuazione.

 /API/$ metadata.

Aggiungere "?api-version=2.x" alla fine dell'URI se si desidera visualizzare i metadati in un browser o se nella richiesta non è stata inclusa l'intestazione x-ms-version.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>L'autenticazione con REST di servizi multimediali tramite Azure Active Directory

L'autenticazione sull'API REST viene eseguita tramite Directory(AAD) attiva di Azure.
Per informazioni dettagliate su come ottenere i dettagli di autenticazione richiesto per l'account di servizi multimediali dal portale di Azure, vedere [accedere all'API di servizi multimediali di Azure con autenticazione di Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

Per informazioni su come scrivere codice che si connette all'API REST di utilizzo dell'autenticazione di Azure AD, vedere l'articolo [autenticazione usano Azure AD per accedere all'API di servizi multimediali di Azure con REST](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come utilizzare l'autenticazione di Azure AD con API REST di servizi multimediali, vedere [autenticazione usano Azure AD per accedere all'API di servizi multimediali di Azure con REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

