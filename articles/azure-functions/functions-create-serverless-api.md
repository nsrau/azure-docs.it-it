---
title: Creare un'API senza server mediante Funzioni di Azure | Documentazione Microsoft
description: 'Procedura: creare un''API senza server mediante Funzioni di Azure'
services: functions
author: mattchenderson
manager: cfowler
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: e4fe86b80d8a786da15cdea37619e54e55102e3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-serverless-api-using-azure-functions"></a>Creare un'API senza server mediante Funzioni di Azure

In questa esercitazione si apprenderà come Funzioni di Azure consente di creare API altamente scalabili. Funzioni di Azure include una raccolta di trigger HTTP e binding integrati che semplificano la creazione di un endpoint in una varietà di linguaggi, inclusi Node.JS, C# e altri. In questa esercitazione si personalizzerà un trigger HTTP per gestire azioni specifiche nella progettazione dell'API. Ci si preparerà inoltre a far crescere l'API integrandola con i proxy di Funzioni di Azure e a configurare API fittizie. Tutto ciò avviene nell'ambiente di calcolo senza server di Funzioni di Azure, pertanto non è necessario preoccuparsi della scalabilità delle risorse ma è possibile concentrarsi semplicemente sulla logica di API.

## <a name="prerequisites"></a>Prerequisiti 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

La funzione risultante sarà utilizzata per il resto dell'esercitazione.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Aprire il Portale di Azure. Accedere a tale scopo all'indirizzo [https://portal.azure.com](https://portal.azure.com) con il proprio account Azure.

## <a name="customize-your-http-function"></a>Personalizzare la funzione HTTP

Per impostazione predefinita, la funzione attivata da HTTP è configurata per accettare qualsiasi metodo HTTP. È inoltre disponibile un URL predefinito nel formato `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Se è stata seguita la guida introduttiva, `<funcname>` probabilmente ha un aspetto simile a "HttpTriggerJS1". In questa sezione si modificherà la funzione in modo che risponda solo alle richieste GET indirizzate verso la route `/api/hello`. 

1. Accedere alla funzione nel portale di Azure. Selezionare **Integra** nel pannello di navigazione a sinistra.

    ![Personalizzazione di una funzione HTTP](./media/functions-create-serverless-api/customizing-http.png)

1. Usare le impostazioni di trigger HTTP come specificato nella tabella.

    | Campo | Valore di esempio | Descrizione |
    |---|---|---|
    | Metodi HTTP consentiti | Metodi selezionati | Determina quali metodi HTTP possono essere usati per chiamare questa funzione |
    | Metodi HTTP selezionati | GET | Consente di usare solo i metodi HTTP selezionati per chiamare questa funzione |
    | Modello di route | /hello | Determina quale route viene usata per chiamare questa funzione |
    | Livello di autorizzazione | Anonima | Facoltativo: rende una funzione accessibile senza una chiave API |

    > [!NOTE] 
    > Si noti che non è stato incluso il prefisso del percorso base `/api` nel modello di route e l'operazione viene gestita da un'impostazione globale.

1. Fare clic su **Salva**.

Altre informazioni sulla personalizzazione delle funzioni HTTP sono riportate in [Binding Azure HTTP e webhook di Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#customizing-the-http-endpoint).

### <a name="test-your-api"></a>Testare l'API

Testare quindi la funzione per verificarne il funzionamento con la nuova superficie dell'API.
1. Passare alla pagina di sviluppo facendo clic sul nome della funzione nel riquadro di spostamento sinistro.
1. Fare clic su **Recupera URL della funzione** e copiare l'URL. Si dovrebbe vedere che a questo punto usa la route `/api/hello`.
1. Copiare l'URL in una nuova scheda del browser o nel client REST preferito. I browser useranno GET per impostazione predefinita.
1. Eseguire la funzione e verificare che operi correttamente. Potrebbe essere necessario fornire il parametro "name" come stringa di query per soddisfare il codice di avvio rapido.
1. È anche possibile chiamare l'endpoint con un altro metodo HTTP per confermare che non viene eseguita la funzione. A tale scopo è necessario usare un client REST, ad esempio cURL, Postman o Fiddler.

## <a name="proxies-overview"></a>Panoramica dei proxy

Nella sezione successiva si esporrà l'API tramite un proxy. I proxy di Funzioni di Azure sono una funzionalità di anteprima che consente di inoltrare le richieste ad altre risorse. Si definisce un endpoint HTTP come con il trigger HTTP, ma anziché scrivere codice da eseguire quando viene chiamato tale endpoint si fornisce un URL a un'implementazione remota. Ciò consente di comporre più origini di API in una singola superficie API facile da consumare per i client. Questo è particolarmente utile se si desidera compilare le API come microservizi.

Un proxy può puntare a qualsiasi risorsa HTTP, ad esempio:
- Funzioni di Azure 
- App per le API in [Servizio app di Azure](https://docs.microsoft.com/azure/app-service/app-service-web-overview)
- Contenitori docker in [Servizio App in Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)
- Qualsiasi altra API in hosting

Per altre informazioni sui proxy, vedere [Uso di proxy in Funzioni di Azure (anteprima)].

## <a name="create-your-first-proxy"></a>Creare il primo proxy

In questa sezione si creerà un nuovo proxy che funge da front-end per l'API complessiva. 

### <a name="setting-up-the-frontend-environment"></a>Configurazione dell'ambiente front-end

Ripetere i passaggi per [creare un'app per le funzioni](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app), creando una nuova app per le funzioni in cui verrà creato il proxy. L'URL della nuova app servirà come front-end per l'API e l'app per le funzioni modificata in precedenza servirà come back-end.

1. Passare alla nuova app per le funzioni front-end nel portale.
1. Selezionare **Impostazioni**. Quindi attivare **Abilita Proxy di Funzioni di Azure (anteprima)**.
1. Selezionare **Impostazioni piattaforma** e scegliere **Impostazioni applicazione**.
1. Scorrere verso il basso fino a **Impostazioni app** e creare una nuova impostazione con chiave "HELLO_HOST". Impostare il suo valore all'host dell'app per le funzioni di back-end, ad esempio `<YourBackendApp>.azurewebsites.net`. Fa parte dell'URL copiato in precedenza durante il test della funzione HTTP. Si farà riferimento a questa impostazione nella configurazione in un secondo momento.

    > [!NOTE] 
    > Le impostazioni di app sono consigliate perché la configurazione dell'host eviti una dipendenza di ambiente hard-coded per il proxy. Usare le impostazioni dell'app significa che è possibile spostare la configurazione del proxy tra ambienti e saranno applicate le impostazioni dell'app specifiche dell'ambiente.

1. Fare clic su **Salva**.

### <a name="creating-a-proxy-on-the-frontend"></a>Creazione di un proxy nel front-end

1. Passare all'app per le funzioni front-end nel portale.
1. Nel riquadro di spostamento sinistro fare clic sul segno "+" accanto a "Proxy (anteprima)".
    ![Creazione di un proxy](./media/functions-create-serverless-api/creating-proxy.png)
1. Usare le impostazioni specificate nella tabella. 

    | Campo | Valore di esempio | Descrizione |
    |---|---|---|
    | Nome | HelloProxy | Nome descrittivo utilizzato solo per la gestione |
    | Modello di route | /api/hello | Determina quale route viene usata per chiamare questo proxy |
    | URL back-end | https://%HELLO_HOST%/api/hello | Specifica l'endpoint a cui la richiesta deve essere trasmessa tramite proxy |
    
1. Si noti che Proxy non fornisce il prefisso del percorso di base `/api` e questo deve essere incluso nel modello di route.
1. La sintassi `%HELLO_HOST%` fa riferimento all'impostazione di app creata in precedenza. L'URL risolto punterà alla funzione originale.
1. Fare clic su **Crea**.
1. È possibile provare il nuovo proxy copiando l'URL del proxy e testandolo nel browser o con il proprio client HTTP preferito.
    1. Per usare una funzione anonima:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?name="Proxies"`
    1. Per usare una funzione con autorizzazione:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>Creare un'API fittizia

Quindi si userà un proxy per creare un'API fittizia per la soluzione. Questo consente allo sviluppo client di procedere, senza bisogno della completa implementazione del back-end. In un secondo momento della fase di sviluppo è possibile creare una nuova app per le funzioni che supporta questa logica e reindirizzare il proxy a essa.

Per creare questa API fittizia, si creerà un nuovo proxy, questa volta usando l'[Editor del servizio app](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Per iniziare passare all'app per le funzioni nel portale. Selezionare **Funzionalità della piattaforma** e trovare **Editor del servizio app**. Facendo clic su questa opzione si apre l'Editor del servizio app in una nuova scheda.

Selezionare `proxies.json` nel riquadro di spostamento sinistro. Si tratta del file che memorizza la configurazione per tutti i proxy. Se si usa uno dei [metodi di distribuzione delle funzioni](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment), questo è il file che si manterrà nel controllo sorgente. Per altre informazioni su questo file, vedere [Configurazione avanzata](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Se la procedura è stata completata fino a questo punto, il file proxies.json dovrebbe essere simile al seguente:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Ora si aggiungerà l'API fittizia. Sostituire il file proxies.json con il seguente:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
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

In questo modo viene aggiunto un nuovo proxy, "GetUserByName", senza la proprietà backendUri. Invece di chiamare un'altra risorsa, viene modificata la risposta predefinita di Proxy usando un override della risposta. È possibile anche usare gli override di richiesta e risposta in combinazione con un URL di back-end. Ciò è particolarmente utile quando si esegue l'inoltro tramite proxy dei dati di un sistema legacy, in cui potrebbe essere necessario modificare le intestazioni, eseguire una query dei parametri e così via. Per altre informazioni sugli override di richiesta e risposta, vedere [Modifica delle richieste e delle risposte](https://docs.microsoft.com/azure/azure-functions/functions-proxies#a-namemodify-requests-responsesamodifying-requests-and-responses).

Testare l'API fittizia chiamando l'endpoint `/api/users/{username}` mediante un browser o il client REST preferito. Assicurarsi di sostituire _{username}_ con un valore stringa che rappresenta un nome utente.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come creare e personalizzare un'API in Funzioni di Azure. È stato inoltre illustrato come mettere insieme più API, incluse quelle fittizie, in una sola superficie API unificata. È possibile usare queste tecniche per creare API di qualsiasi complessità, il tutto durante l'esecuzione del modello di calcolo senza server fornito da Funzioni di Azure.

I riferimenti seguenti possono essere utili quando si sviluppa ulteriormente l'API:

- [Binding HTTP e webhook di Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Uso di proxy in Funzioni di Azure (anteprima)]
- [Documentazione di un'API di Funzioni di Azure (anteprima)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Uso di proxy in Funzioni di Azure (anteprima)]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
