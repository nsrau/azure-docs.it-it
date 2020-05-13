---
title: Personalizzare un endpoint HTTP in funzioni di Azure
description: Informazioni su come personalizzare un endpoint di trigger HTTP in funzioni di Azure
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 5607a737fa4616d4eda3d174144c1717125f4181
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122775"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Personalizzare un endpoint HTTP in funzioni di Azure

Questo articolo illustra come funzioni di Azure consente di creare API altamente scalabili. Funzioni di Azure include una raccolta di trigger HTTP e binding incorporati, che semplificano la creazione di un endpoint in una varietà di linguaggi, tra cui node. js, C# e altro ancora. In questo articolo verrà personalizzato un trigger HTTP per gestire azioni specifiche nella progettazione dell'API. Si prepara anche la crescita dell'API grazie all'integrazione con Proxy di Funzioni di Azure e alla configurazione di API fittizie. Queste attività vengono eseguite sulla base dell'ambiente di calcolo senza server di funzioni, quindi non è necessario preoccuparsi di ridimensionare le risorse. è possibile concentrarsi solo sulla logica dell'API.

## <a name="prerequisites"></a>Prerequisiti 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

La funzione risultante verrà usata per la parte restante di questo articolo.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

## <a name="customize-your-http-function"></a>Personalizzare la funzione HTTP

Per impostazione predefinita, la funzione trigger HTTP è configurata per accettare qualsiasi metodo HTTP. È anche possibile usare l'URL predefinito `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` . In questa sezione si modifica la funzione in modo che risponda solo alle richieste GET con `/api/hello` . 

1. Accedere alla funzione nel portale di Azure. Selezionare **integrazione** nel menu a sinistra e quindi selezionare **http (req)** in **trigger**.

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="Personalizzazione di una funzione HTTP":::

1. Usare le impostazioni del trigger HTTP come specificato nella tabella seguente.

    | Campo | Valore di esempio | Descrizione |
    |---|---|---|
    | Modello di route | /hello | Determina quale route viene usata per chiamare questa funzione |
    | Livello di autorizzazione | Anonima | Facoltativo: rende una funzione accessibile senza una chiave API |
    | Metodi HTTP selezionati | GET | Consente di usare solo i metodi HTTP selezionati per chiamare questa funzione |

    Il prefisso del percorso di base non è `/api` stato incluso nel modello di route, perché è gestito da un'impostazione globale.

1. Selezionare **Salva**.

Per altre informazioni sulla personalizzazione delle funzioni HTTP, vedere [binding HTTP di funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook).

### <a name="test-your-api"></a>Testare l'API

Testare quindi la funzione per verificarne il funzionamento con la nuova superficie dell'API:
1. Nella pagina funzione selezionare **codice + test** dal menu a sinistra.

1. Selezionare **Ottieni URL funzione** dal menu in alto e copiare l'URL. Verificare che ora usi il `/api/hello` percorso.
 
1. Copiare l'URL in una nuova scheda del browser o nel client REST preferito. 

   Per impostazione predefinita, i browser usano GET.
 
1. Aggiungere parametri alla stringa di query nell'URL. 

   Ad esempio, `/api/hello/?name=John`
 
1. Premere INVIO per verificare che funzioni. Dovrebbe essere visualizzata la risposta "*Hello John*".

1. È anche possibile provare a chiamare l'endpoint con un altro metodo HTTP per confermare che la funzione non viene eseguita. A tale scopo, usare un client REST, ad esempio cURL, postazione o Fiddler.

## <a name="proxies-overview"></a>Panoramica dei proxy

Nella sezione successiva verrà illustrata la superficie dell'API tramite un proxy. Proxy di Funzioni di Azure consente di inoltrare le richieste ad altre risorse. Si definisce un endpoint HTTP esattamente come con il trigger HTTP. Tuttavia, anziché scrivere codice da eseguire quando viene chiamato l'endpoint, si fornisce un URL a un'implementazione remota. In questo modo è possibile comporre più origini API in un'unica superficie dell'API, che è facile per l'uso da parte dei client, che risulta utile se si vuole creare l'API come microservizi.

Un proxy può puntare a qualsiasi risorsa HTTP, ad esempio:
- Funzioni di Azure 
- App per le API in [Servizio app di Azure](https://docs.microsoft.com/azure/app-service/overview)
- Contenitori docker in [Servizio App in Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)
- Qualsiasi altra API in hosting

Per altre informazioni sui proxy, vedere [Uso dei proxy di Funzioni di Azure].

## <a name="create-your-first-proxy"></a>Creare il primo proxy

In questa sezione viene creato un nuovo proxy, che funge da front-end per l'API globale. 

### <a name="setting-up-the-frontend-environment"></a>Configurazione dell'ambiente front-end

Ripetere i passaggi per [creare un'app per le funzioni](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app), creando una nuova app per le funzioni in cui verrà creato il proxy. Questo nuovo URL dell'app funge da front-end per l'API e l'app per le funzioni che in precedenza veniva modificata funge da back-end.

1. Passare alla nuova app per le funzioni front-end nel portale.
1. Selezionare **Funzionalità della piattaforma** e scegliere **Impostazioni applicazione**.
1. Scorrere verso il basso fino a **Impostazioni applicazione**, in cui vengono archiviate le coppie chiave/valore e creare una nuova impostazione con la chiave `HELLO_HOST` . Impostare il suo valore all'host dell'app per le funzioni di back-end, ad esempio `<YourBackendApp>.azurewebsites.net`. Questo valore fa parte dell'URL copiato in precedenza durante il test della funzione HTTP. Si farà riferimento a questa impostazione nella configurazione in un secondo momento.

    > [!NOTE] 
    > Le impostazioni di app sono consigliate perché la configurazione dell'host eviti una dipendenza di ambiente hard-coded per il proxy. Usare le impostazioni dell'app significa che è possibile spostare la configurazione del proxy tra ambienti e saranno applicate le impostazioni dell'app specifiche dell'ambiente.

1. Selezionare **Salva**.

### <a name="creating-a-proxy-on-the-frontend"></a>Creazione di un proxy nel front-end

1. Tornare all'app per le funzioni front-end nel portale.

1. Nel menu a sinistra selezionare **proxy**e quindi selezionare **Aggiungi**. 

1. Nella pagina **nuovo proxy** usare le impostazioni riportate nella tabella seguente e quindi selezionare **Crea**.

    | Campo | Valore di esempio | Descrizione |
    |---|---|---|
    | Nome | HelloProxy | Nome descrittivo utilizzato solo per la gestione |
    | Modello di route | /api/remotehello | Determina quale route viene usata per chiamare questo proxy |
    | URL back-end | https://%HELLO_HOST%/api/hello | Specifica l'endpoint a cui la richiesta deve essere trasmessa tramite proxy |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="Creazione di un proxy":::

    Proxy di Funzioni di Azure non fornisce il `/api` prefisso del percorso di base, che deve essere incluso nel modello di route. La `%HELLO_HOST%` sintassi fa riferimento all'impostazione dell'app creata in precedenza. L'URL risolto punterà alla funzione originale.

1. Provare il nuovo proxy copiando l'URL del proxy e testarlo nel browser o con il client HTTP preferito:
    - Per una funzione anonima, usare: `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"` .
    - Per una funzione con autorizzazione use: `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"` .

## <a name="create-a-mock-api"></a>Creare un'API fittizia

Si userà quindi un proxy per creare un'API fittizia per la soluzione. Questo proxy consente lo sviluppo dei client, senza la necessità che il back-end sia completamente implementato. Successivamente, è possibile creare una nuova app per le funzioni, che supporta questa logica e reindirizza il proxy.

Per creare questa API fittizia, si creerà un nuovo proxy, questa volta usando il [Editor del servizio app](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Per iniziare passare all'app per le funzioni nel portale. Selezionare **funzionalità della piattaforma**e in **strumenti di sviluppo** trova **Editor del servizio app**. Il editor del servizio app viene aperto in una nuova scheda.

Selezionare `proxies.json` nel riquadro di spostamento sinistro. Questo file archivia la configurazione per tutti i proxy. Se si usa uno dei [metodi di distribuzione delle funzioni](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment), questo file viene mantenuto nel controllo del codice sorgente. Per altre informazioni su questo file, vedere [Configurazione avanzata](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Se la procedura è stata eseguita fino a questo punto, il file Proxys. JSON avrà un aspetto simile al seguente:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Successivamente, si aggiungerà l'API fittizia. Sostituire il file Proxys. JSON con il codice seguente:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Questo codice aggiunge un nuovo proxy, `GetUserByName` , senza la `backendUri` Proprietà. Invece di chiamare un'altra risorsa, viene modificata la risposta predefinita di Proxy usando un override della risposta. È possibile anche usare gli override di richiesta e risposta in combinazione con un URL di back-end. Questa tecnica è particolarmente utile quando si esegue l'inoltro a un sistema legacy, in cui potrebbe essere necessario modificare intestazioni, parametri di query e così via. Per altre informazioni sugli override di richiesta e risposta, vedere [Modifica delle richieste e delle risposte](https://docs.microsoft.com/azure/azure-functions/functions-proxies).

Testare l'API fittizia chiamando l'endpoint `<YourProxyApp>.azurewebsites.net/api/users/{username}` mediante un browser o il client REST preferito. Assicurarsi di sostituire _{username}_ con un valore stringa che rappresenta un nome utente.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare e personalizzare un'API in funzioni di Azure. È stato inoltre illustrato come mettere insieme più API, incluse quelle fittizie, in una sola superficie API unificata. È possibile usare queste tecniche per creare API di qualsiasi complessità, il tutto durante l'esecuzione del modello di calcolo senza server fornito da Funzioni di Azure.

I riferimenti seguenti possono essere utili quando si sviluppa ulteriormente l'API:

- [Binding HTTP di Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Uso dei proxy di Funzioni di Azure]
- [Documentazione di un'API di Funzioni di Azure (anteprima)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Uso dei proxy di Funzioni di Azure]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
