---
title: Come usare Swagger per Gemelli digitali di Azure| Microsoft Docs
description: Come usare Swagger per Gemelli digitali di Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: adgera
ms.openlocfilehash: dbadc90a206937d4c9f1d7b75a872d93b1a8a587
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323870"
---
# <a name="how-to-use-azure-digital-twins-swagger"></a>Come usare Swagger per Gemelli digitali di Azure

Ogni istanza di Gemelli digitali di Azure di cui è stato effettuato il provisioning include la propria documentazione di riferimento Swagger generata automaticamente.

[Swagger](https://swagger.io/) (o [OpenAPI](https://www.openapis.org/)) raccoglie complesse informazioni sull'API in una risorsa di riferimento interattiva e indipendente dal linguaggio. Nell specifico, Swagger fornisce materiale di riferimento di importanza critica sui payload JSON, i metodi HTTP e gli endpoint specifici da usare per eseguire operazioni su un'API.

## <a name="swagger-summary"></a>Riepilogo di Swagger

Swagger fornisce un riepilogo interattivo dell'API, che include:

* Informazioni sull'API e sul modello a oggetti.
* Endpoint API REST che specificano i payload delle richieste, le intestazioni, i parametri, i percorsi di contesto e i metodi HTTP necessari.
* Test delle funzionalità dell'API.
* Informazioni sulla risposta di esempio per la convalida e la conferma delle risposte HTTP.
* Informazioni sui codici di errore.

Swagger è quindi uno strumento utile per lo sviluppo e test delle chiamate eseguite all'API di gestione.

> [!TIP]
> Come riferimento viene fornita un'anteprima di prova di Swagger per mostrare il set di funzionalità dell'API.
> L'anteprima è ospitata in [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

È possibile accedere alla propria documentazione Swagger generata automaticamente sull'API di gestione all'indirizzo:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Nome attributo personalizzato | Sostituire con |
| --- | --- |
| `yourInstanceName` | Nome dell'istanza di Gemelli digitali di Azure |
| `yourLocation` | Area del server in cui è ospitata l'istanza |

## <a name="reference-material"></a>Materiale di riferimento

Il materiale di riferimento generato automaticamente spiega i concetti più importanti e i modelli a oggetti.

Un breve riepilogo descrive l'API:

![Breve riepilogo di Swagger][1]

Sono elencati anche i modelli a oggetti delle API principali:

![Modelli di Swagger][2]

È possibile fare clic in ognuno dei modelli a oggetti elencati per visualizzare un riepilogo più dettagliato degli attributi chiave:

![Modello di Swagger][3]

I modelli a oggetti di Swagger generati sono utili per visualizzare tutti [gli oggetti e le API](./concepts-objectmodel-spatialgraph.md) di Gemelli digitali di Azure disponibili. Sono un'ottima risorsa che può essere usata dagli sviluppatori per compilare soluzioni in Gemelli digitali di Azure.

## <a name="endpoint-summary"></a>Riepilogo degli endpoint

Swagger fornisce anche una panoramica completa di tutti gli endpoint che compongono l'API.

Ogni endpoint elencato include anche le informazioni sulla richiesta obbligatorie, come:

* I parametri obbligatori.
* I tipi di dati dei parametri obbligatori.
* Il metodo HTTP per l'accesso alla risorsa.

![Endpoint di Swagger][4]

È possibile fare clic su ogni risorsa per visualizzare una panoramica più dettagliata.

## <a name="using-swagger-to-test-endpoints"></a>Uso di Swagger per testare gli endpoint

Una delle più potenti funzionalità offerte da Swagger è la possibilità di **provare** o testare un endpoint API direttamente dall'interfaccia utente della documentazione.

Dopo aver fatto clic su un endpoint specifico, viene visualizzato un pulsante **Prova**:

![Pulsante Prova di Swagger][5]

Espandendo la sezione vengono visualizzati i campi di input per ogni parametro obbligatorio e facoltativo. Immettere i valori appropriati e fare clic su **Esegui**:

![Esecuzione del test in Swagger][6]

Dopo l'esecuzione del test, è possibile convalidare i dati della risposta.

## <a name="swagger-response-data"></a>Dati della risposta di Swagger

Ogni endpoint elencato include anche i dati del corpo della risposta per convalidare lo sviluppo e i test. Questi esempi includono il formato JSON e i codici di stato desiderati per le richieste HTTP riuscite.

![Risposta di Swagger][7]

Gli esempi includono anche i codici di errore per facilitare il debug o migliorare i test non superati.

## <a name="swagger-oauth-20-authorization"></a>Autorizzazione OAuth 2.0 di Swagger

Per testare in modo interattivo le richieste sulle risorse API protette tramite OAuth 2.0, vedere la [documentazione ufficiale](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui modelli a oggetti e i grafici di intelligenza spaziale di Gemelli digitali di Azure, vedere [questo articolo](./concepts-objectmodel-spatialgraph.md).

Per informazioni su come eseguire l'autenticazione con l'API di gestione, vedere [Connettersi alle API ed eseguire l'autenticazione](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.png
[2]: media/how-to-use-swagger/swagger_management_models.png
[3]: media/how-to-use-swagger/swagger_management_model.png
[4]: media/how-to-use-swagger/swagger_management_endpoints.png
[5]: media/how-to-use-swagger/swagger_management_try.png
[6]: media/how-to-use-swagger/swagger_management_tried.png
[7]: media/how-to-use-swagger/swagger_management_response.png
