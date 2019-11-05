---
title: Installare ed eseguire contenitori-Traduzione testuale
titleSuffix: Azure Cognitive Services
description: Come scaricare, installare ed eseguire contenitori per Traduzione testuale Analytics in questa esercitazione della procedura dettagliata.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7b2b07f71d00e6da66062d1826f8c5c88bb6be7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507880"
---
# <a name="install-and-run-translator-text-containers"></a>Installare ed eseguire Traduzione testuale contenitori

<!--
    ACOM forward link:
    https://docs.microsoft.com/azure/cognitive-services/translator/howto-install-containers
-->

I contenitori consentono di eseguire le API Traduzione testuale nel proprio ambiente e sono ottime per requisiti specifici di sicurezza e governance dei dati.

## <a name="prerequisites"></a>Prerequisiti

Prima di usare i contenitori di Traduzione testuale, è necessario soddisfare i prerequisiti seguenti:

|Obbligatorio|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.|

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

È necessario prima completare e inviare il [modulo di richiesta di contenitori cognitivi traduzione testuale](https://aka.ms/translatorcontainerform) per richiedere l'accesso al contenitore.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

### <a name="the-host-computer"></a>Computer host

L'host è un computer basato su x64 con un sistema operativo Linux che esegue il contenitore docker. Può essere un computer dell'ambiente locale o un servizio di hosting Docker in Azure, tra cui:

* Servizio Azure Kubernetes.
* Istanze di contenitore di Azure.
* Un cluster [Kubernetes](https://kubernetes.io/) distribuito per Azure stack.

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti dei contenitori

La tabella seguente descrive i core CPU minimi e consigliati, almeno 2,6 gigahertz (GHz) o più veloce, e la memoria, in gigabyte (GB), da allocare per ogni contenitore Traduzione testuale.

| Contenitore | Minima | Language pair (Coppia di lingue) |
|-----------|---------|---------------|
| Traduzione testuale | 4 core, 4 GB di memoria | 4 |

Per ogni coppia di lingue, è consigliabile disporre di 1 GB di memoria. Per impostazione predefinita, il contenitore Traduzione testuale dispone di coppie di lingue di 3 o 4, a seconda del `<image-tag>` in esecuzione. Per informazioni dettagliate, vedere le [lingue supportate e la traduzione](#supported-languages-and-translation) . Il nucleo e la memoria corrispondono alle impostazioni `--cpus` e `--memory`, che vengono usate come parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Le immagini del contenitore per Traduzione testuale sono disponibili nel repository contenitore seguente. La tabella mappa inoltre i tag dell'immagine del contenitore e le lingue supportate corrispondenti.

| Contenitore | Tag immagine | Lingue supportate |
|-----------|-----------|---------------------|
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `ar_de_en_ru_zh_1.0.0` | `ar-SA`, `zh-CN`, `de-DE` e `ru-RU` |
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `de_en_es_fr_1.0.0` | `de-DE`, `fr-FR` e `es-ES` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-translator-text-container"></a>Pull Docker per il contenitore di Traduzione testuale

Per eseguire il comando di [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , è necessario prima di tutto accedere al registro contenitori. Dall'interfaccia della riga di comando di Azure è possibile accedere al Container Registry di Azure usando il comando [`az acr login`](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-login) .

```azureinteractive
az acr login --name containerpreview.azurecr.io
```

Questo comando autentica l'utente corrente con la Container Registry di Azure corrispondente. A questo punto, è possibile eseguire il comando `docker pull`.

> [!NOTE]
> A seconda del supporto linguistico necessario, fornire la `<image-tag>`corrispondente.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:<image-tag>
```

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run). Sono disponibili altri [esempi](translator-text-container-config.md#example-docker-run-commands) del comando `docker run`.
1. [Eseguire una query sull'endpoint di traduzione del contenitore](#query-the-containers-translate-endpoint).

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire uno qualsiasi dei tre contenitori. Sono disponibili [esempi](translator-text-container-config.md#example-docker-run-commands) del comando `docker run`.

### <a name="run-container-example-of-docker-run-command"></a>Esempio di esecuzione del contenitore del comando Docker Run

```Docker
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:ar_de_en_ru_zh_1.0.0 \
Eula=accept
```

Questo comando:

* Esegue un Traduzione testuale contenitore dall'immagine del contenitore
* Alloca 4 core CPU e 4 gigabyte (GB) di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
* Accetta il contratto di licenza con l'utente finale (EULA)
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore è ancora disponibile nel computer host

### <a name="how-to-collect-docker-logs"></a>Come raccogliere i log di Docker

Ai fini della risoluzione dei problemi, può essere utile visualizzare i log di Docker dall'esecuzione del contenitore. Per prima cosa, eseguire il comando [Docker PS](https://docs.docker.com/engine/reference/commandline/ps/) con il flag di formattazione per limitare i dettagli visualizzati per tutti i contenitori.

```Docker
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}"

CONTAINER ID    IMAGE                                                                       STATUS
ed6f115697f3    containerpreview.azurecr.io/microsoft/cognitive-services-translator-text    Up 4 minutes
```

Eseguire quindi il comando [Docker logs](https://docs.docker.com/engine/reference/commandline/logs/) con la `<Container ID>` per il contenitore corrispondente in questione per visualizzare i relativi log.

```Docker
docker logs <Container ID> --timestamps --since=4h | grep Error
```

Il comando Docker logs riportato sopra raccoglierà i log "Error" per le ultime quattro ore.

## <a name="supported-languages-and-translation"></a>Linguaggi e Traduzioni supportati

Il metodo `POST /translate` supporta le conversioni di linguaggi seguenti, che passano dall' *inglese* alla lingua di destinazione e viceversa. Si noti che, anche se è possibile passare da e verso l'inglese con una delle lingue elencate *, non è possibile passare da* una lingua *non* in *lingua inglese a un'altra* .

> [!NOTE]
> Per una qualità ottimale, i consumer devono inviare una sola frase per ogni richiesta.

| Conversione della lingua | Conversione ISO della lingua | Tag immagine |
|--|--|:--|
| Inglese: left_right_arrow: cinese | `en-US`: left_right_arrow: `zh-CN` | `ar_de_en_ru_zh_1.0.0` |
| Inglese: left_right_arrow: russo | `en-US`: left_right_arrow: `ru-RU` | `ar_de_en_ru_zh_1.0.0` |
| Inglese: left_right_arrow: arabo | `en-US`: left_right_arrow: `ar-SA` | `ar_de_en_ru_zh_1.0.0` |
| Inglese: left_right_arrow: tedesco | `en-US`: left_right_arrow: `de-DE` | `ar_de_en_ru_zh_1.0.0` e `de_en_es_fr_1.0.0` |
| Inglese: left_right_arrow: Spagnolo | `en-US`: left_right_arrow: `es-ES` | `de_en_es_fr_1.0.0` |
| Inglese: left_right_arrow: francese | `en-US`: left_right_arrow: `fr-FR` | `de_en_es_fr_1.0.0` |

> [!IMPORTANT]
> È necessario specificare il `Eula` per eseguire il contenitore; in caso contrario, il contenitore non verrà avviato.

## <a name="query-the-containers-translate-endpoint"></a>Eseguire una query sull'endpoint di traduzione del contenitore

Il contenitore fornisce un'API dell'endpoint di traduzione basata su REST. Di seguito sono riportati alcuni esempi di utilizzo di questo endpoint:

# <a name="curltabcurl"></a>[cURL](#tab/curl)


Eseguire il comando cURL seguente dall'interfaccia della riga di comando desiderata.

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en-US&to=de-DE"
    -H "Content-Type: application/json" -d "[{'Text':'hello, how are you'}]"
```

> [!TIP]
> Se si tenta questo POST cURL prima che il contenitore sia pronto, si otterrà una risposta "servizio non disponibile temporaneamente". È sufficiente attendere finché il contenitore non è pronto, quindi riprovare.

L'output seguente verrà stampato nella console.

```console
"translations": [
    {
        "text": "hallo, wie geht es dir",
        "to": "de-DE"
    }
]
```

# <a name="swaggertabswagger"></a>[Swagger](#tab/Swagger)

Passare alla pagina di spavalderia http://localhost:5000/swagger/index.html

1. Selezionare **post/translate**
1. Selezionare **prova**
1. Immettere il parametro **from** come `en-US`
1. Immettere il parametro **to** come `de-DE`
1. Immettere il parametro **API-Version** come `3.0`
1. In **testi**sostituire `string` con il codice JSON seguente
    ```json
    [
        {
            "text": "hello, how are you"
        }
    ]
    ```
1. Selezionare **Esegui**. le conversioni risultanti vengono restituite nel **corpo della risposta**. Si dovrebbe prevedere un risultato simile al seguente:
    ```json
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

1. Avviare Visual Studio e creare una nuova applicazione console.
1. Modificare il file di `*.csproj` per aggiungere il nodo `<LangVersion>7.1</LangVersion>`, specificando C# 7,1.
1. Aggiungere il pacchetto NuGet [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) , versione 11.0.2.
1. In `Program.cs`sostituire tutto il codice esistente con il codice seguente:
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    namespace TranslateContainer
    {
        class Program
        {
            const string ApiHostEndpoint = "http://localhost:5000";
            const string TranslateApi = "/translate?api-version=3.0&from=en-US&to=de-DE";

            static async Task Main(string[] args)
            {
                var textToTranslate = "hello, how are you";
                var result = await TranslateTextAsync(textToTranslate);

                Console.WriteLine(result);
                Console.ReadLine();
            }

            static async Task<string> TranslateTextAsync(string textToTranslate)
            {
                var body = new object[] { new { Text = textToTranslate } };
                var requestBody = JsonConvert.SerializeObject(body);

                var client = new HttpClient();
                using (var request =
                    new HttpRequestMessage
                    {
                        Method = HttpMethod.Post,
                        RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                        Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                    })
                {
                    // Send the request and await a response.
                    var response = await client.SendAsync(request);

                    return await response.Content.ReadAsStringAsync();
                }
            }
        }
    }
    ```
1. Premere **F5** per eseguire il programma.
1. L'output seguente verrà stampato nella console.
    ```console
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

***

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si esegue il contenitore con un punto di [montaggio](translator-text-container-config.md#mount-settings) di output e la registrazione attivata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Riepilogo

In questo articolo sono stati appresi concetti e flussi di lavoro per il download, l'installazione e l'esecuzione di contenitori di Traduzione testuale. In breve:

* Traduzione testuale offre più contenitori Linux per Docker, incapsulando varie coppie di linguaggi.
* Le immagini del contenitore vengono scaricate dal registro di sistema "anteprima contenitore".
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori di Traduzione testuale specificando l'URI host del contenitore.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](translator-text-container-config.md) per le impostazioni di configurazione
* Per risolvere i problemi relativi alla funzionalità, vedere Domande frequenti sul [contenitore](../containers/container-faq.md) .
