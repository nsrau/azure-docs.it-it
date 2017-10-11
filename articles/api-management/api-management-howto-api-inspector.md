---
title: Tenere traccia delle chiamate con Controllo API in Gestione API di Azure | Documentazione Microsoft
description: Informazioni su come tenere traccia delle chiamate usando Controllo API in Gestione API di Azure.
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 4b222327-c8a4-4f33-9a06-adff2a9834d9
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: a9d4d3be7f046af975f6dc25670070204848588c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-the-api-inspector-to-trace-calls-in-azure-api-management"></a>Come usare Controllo API per tenere traccia delle chiamate in Gestione API di Azure
Gestione API fornisce uno strumento per il controllo delle API per aiutare gli sviluppatori ad eseguire il debug e la risoluzione dei problemi correlati alle API. È possibile usare Controllo API sia a livello di codice sia direttamente dal portale per sviluppatori. 

Oltre alle operazioni di analisi, Controllo API consente anche di tenere traccia delle valutazioni delle [espressioni di criteri](https://msdn.microsoft.com/library/azure/dn910913.aspx) . Per una dimostrazione, vedere il video [Cloud Cover Episode 177: More API Management Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avanzare rapidamente al minuto 21:00.

Questa guida contiene una procedura dettagliata sull'uso di Controllo API.

> [!NOTE]
> Le tracce di Controllo API sono generate e rese disponibili solo per le richieste contenenti chiavi di sottoscrizione che appartengono all'account [amministratore](api-management-howto-create-groups.md) .
> 
> 

## <a name="trace-call"> </a> Usare Controllo API per tenere traccia di una chiamata
Per usare Controllo API, aggiungere un'intestazione di richiesta **ocp-apim-trace: true** alla chiamata di operazioni, quindi scaricare ed esaminare la traccia usando l'URL indicato dall'intestazione di risposta **ocp-apim-trace-location**. Questa operazione può essere eseguita sia a livello di codice che direttamente dal portale per sviluppatori.

Questa esercitazione illustra come usare Controllo API per tenere traccia delle operazioni tramite l'API Basic Calculator configurata nell'esercitazione introduttiva [Gestire la prima API](api-management-get-started.md). Se tale esercitazione non è stata completata, sono sufficienti pochi minuti per importare l'API Basic Calculator oppure è possibile usare un'altra API a scelta, ad esempio l'API Echo. Ogni istanza del servizio Gestione API è preconfigurata con un'API Echo utilizzabile per sperimentare e ottenere altre informazioni su Gestione API. L'API Echo restituisce qualunque input gli venga inviato. Per usarla, è possibile richiamare un qualsiasi verbo HTTP. Il valore restituito sarà semplicemente ciò che si è inviato. 

Per iniziare, fare clic su **Portale per sviluppatori** nel portale di Azure per il servizio Gestione API. È possibile chiamare le operazioni direttamente dal portale per sviluppatori, che consente di visualizzare e testare le operazioni di un'API in tutta comodità.

> Se non è stata ancora creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][Create an API Management service instance] nell'esercitazione [Introduzione a Gestione API di Azure][Get started with Azure API Management].
> 
> 

![Portale per sviluppatori di Gestione API][api-management-developer-portal-menu]

Fare clic su **API** nel menu superiore, quindi su **Basic Calculator** (Calcolatrice di base).

![API Echo][api-management-api]

Fare clic su **Prova** per provare a eseguire l'operazione **Aggiungere due Integer**.

![Prova][api-management-open-console]

Mantenere i valori predefiniti dei parametri e selezionare la chiave di sottoscrizione per il prodotto da usare nell'elenco a discesa **subscription-key** .

Per impostazione predefinita, nel portale per sviluppatori l'intestazione **Ocp-Apim-Trace** è già impostata su **true**. Questa intestazione indica se viene o meno generata una traccia.

![Send][api-management-http-get]

Fare clic su **Send** per richiamare l'operazione.

![Send][api-management-send-results]

Le intestazioni di risposta conterranno un elemento **ocp-apim-trace-location** con un valore simile a quello dell'esempio seguente.

```
ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742
```

È possibile scaricare la traccia dal percorso specificato e quindi analizzarla, come illustrato nel passaggio seguente. Si noti che vengono archiviate solo le ultime 100 voci di log e i percorsi del log vengono riutilizzati a rotazione. Pertanto se si eseguono più di 100 chiamate con la traccia attivata, si finirà per sovrascrivere le prime tracce.

## <a name="inspect-trace"> </a>Esaminare la traccia
Per rivedere i valori nella traccia, scaricare il file di traccia dell'URL **ocp-apim-trace-location** . Si tratta di un file di testo in formato JSON, contenente voci simili a quelle illustrate nell'esempio seguente.

```json
{
    "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
    "traceEntries": {
        "inbound": [
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0725926",
                "data": {
                    "request": {
                        "method": "GET",
                        "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                        "headers": [
                            {
                                "name": "Ocp-Apim-Subscription-Key",
                                "value": "5d7c41af64a44a68a2ea46580d271a59"
                            },
                            {
                                "name": "Connection",
                                "value": "Keep-Alive"
                            },
                            {
                                "name": "Host",
                                "value": "contoso5.azure-api.net"
                            }
                        ]
                    }
                }
            },
            {
                "source": "mapper",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0726213",
                "data": {
                    "configuration": {
                        "api": {
                            "from": "/calc",
                            "to": {
                                "scheme": "http",
                                "host": "calcapi.cloudapp.net",
                                "port": 80,
                                "path": "/api",
                                "queryString": "",
                                "query": {},
                                "isDefaultPort": true
                            }
                        },
                        "operation": {
                            "method": "GET",
                            "uriTemplate": "/add?a={a}&b={b}"
                        },
                        "user": {
                            "id": 1,
                            "groups": [
                                "Administrators",
                                "Developers"
                            ]
                        },
                        "product": {
                            "id": 1
                        }
                    }
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0727522",
                "data": {
                    "message": "Request is being forwarded to the backend service.",
                    "request": {
                        "method": "GET",
                        "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                        "headers": [
                            {
                                "name": "Ocp-Apim-Subscription-Key",
                                "value": "5d7c41af64a44a68a2ea46580d271a59"
                            },
                            {
                                "name": "X-Forwarded-For",
                                "value": "33.52.215.35"
                            }
                        ]
                    }
                }
            }
        ],
        "outbound": [
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1960601",
                "data": {
                    "response": {
                        "status": {
                            "code": 200,
                            "reason": "OK"
                        },
                        "headers": [
                            {
                                "name": "Pragma",
                                "value": "no-cache"
                            },
                            {
                                "name": "Content-Length",
                                "value": "124"
                            },
                            {
                                "name": "Cache-Control",
                                "value": "no-cache"
                            },
                            {
                                "name": "Content-Type",
                                "value": "application/xml; charset=utf-8"
                            },
                            {
                                "name": "Date",
                                "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                            },
                            {
                                "name": "Expires",
                                "value": "-1"
                            },
                            {
                                "name": "Server",
                                "value": "Microsoft-IIS/8.5"
                            },
                            {
                                "name": "X-AspNet-Version",
                                "value": "4.0.30319"
                            },
                            {
                                "name": "X-Powered-By",
                                "value": "ASP.NET"
                            }
                        ]
                    }
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1961112",
                "data": {
                    "message": "Response headers have been sent to the caller. Starting to stream the response body."
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1963155",
                "data": {
                    "message": "Response body streaming to the caller is complete."
                }
            }
        ]
    }
}
```

## <a name="next-steps"> </a>Passaggi successivi
* Per una demo sulla traccia delle espressioni di criteri, vedere il video [Cloud Cover Episode 177: More API Management Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Avanzare rapidamente al minuto 21:00 per vedere la demo.

> [!VIDEO https://channel9.msdn.com/Shows/Cloud+Cover/Episode-177-More-API-Management-Features-with-Vlad-Vinogradsky/player]
> 
> 

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png




