---
title: Informazioni su come usare la documentazione di riferimento di Swagger per Gemelli digitali di Azure | Microsoft Docs
description: Comprendere come usare la documentazione di riferimento di Swagger per Gemelli digitali di Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 6a99693c3f88e94c093910da6ba4a305c26fe39d
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931351"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Documentazione di riferimento di Swagger per Gemelli digitali di Azure

Ogni istanza di Gemelli digitali di Azure di cui è stato effettuato il provisioning include la propria documentazione di riferimento Swagger generata automaticamente.

[Swagger](https://swagger.io/) (o [OpenAPI](https://www.openapis.org/)) raccoglie complesse informazioni sulle API in una risorsa di riferimento interattiva e indipendente dal linguaggio. Swagger fornisce materiale di riferimento di importanza critica sui payload JSON, i metodi HTTP e gli endpoint specifici da usare per eseguire operazioni su un'API.

## <a name="swagger-summary"></a>Riepilogo di Swagger

Swagger fornisce un riepilogo interattivo dell'API, che include:

* Informazioni sull'API e sul modello a oggetti.
* Endpoint API REST che specificano i payload delle richieste, le intestazioni, i parametri, i percorsi di contesto e i metodi HTTP necessari.
* Test delle funzionalità dell'API.
* Informazioni sulla risposta di esempio per la convalida e la conferma delle risposte HTTP.
* Informazioni sui codici di errore.

Swagger è un pratico strumento per semplificare lo sviluppo e i test delle chiamate effettuate all'API Gestione per Gemelli digitali di Azure.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Materiale di riferimento

Il materiale di riferimento di Swagger generato automaticamente offre una rapida panoramica di concetti importanti, endpoint disponibili dell'API Gestione e una descrizione di ogni modello a oggetti per facilitare sviluppo e test.

Un breve riepilogo descrive l'API.

[![spavalderia in alto](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Sono elencati anche i modelli a oggetti dell'API Gestione.

[modelli di ![spavalderia](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

È possibile selezionare ogni modello a oggetti elencato per visualizzare un riepilogo più dettagliato degli attributi principali.

[modello di ![spavalderia](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

I modelli a oggetti di Swagger generati sono utili per visualizzare tutti gli oggetti e le API di [Gemelli digitali di Azure](./concepts-objectmodel-spatialgraph.md) disponibili. Gli sviluppatori possono usare questa risorsa quando compilano soluzioni in Gemelli digitali di Azure.

## <a name="endpoint-summary"></a>Riepilogo degli endpoint

Swagger fornisce anche una panoramica completa di tutti gli endpoint che compongono l'API Gestione.

Ogni endpoint elencato include anche le informazioni obbligatorie sulla richiesta, tra cui:

* I parametri obbligatori.
* I tipi di dati dei parametri obbligatori.
* Metodo HTTP per l'accesso alla risorsa.

[![gli endpoint di spavalderia](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

È possibile fare clic su ogni risorsa per visualizzare una panoramica più dettagliata.

## <a name="use-swagger-to-test-endpoints"></a>Usare Swagger per testare gli endpoint

Una delle funzionalità più potenti offerte da Swagger è la possibilità di testare un endpoint API direttamente dall'interfaccia utente della documentazione.

Dopo aver selezionato un endpoint specifico, verrà visualizzato **Prova**.

[tentativo di ![spavalderia](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Espandere la sezione per visualizzare i campi di input per ogni parametro obbligatorio e facoltativo. Immettere i valori corretti, quindi selezionare **Esegui**.

[tentativo di ![spavalderia](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Dopo l'esecuzione del test, è possibile convalidare i dati della risposta.

## <a name="swagger-response-data"></a>Dati della risposta di Swagger

Ogni endpoint elencato include anche i dati del corpo della risposta per convalidare lo sviluppo e i test. Questi esempi includono il codice JSON e i codici di stato desiderati per le richieste HTTP riuscite.

[risposta spavalderia ![](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Gli esempi includono anche i codici di errore per facilitare il debug o migliorare i test non superati.

## <a name="swagger-oauth-20-authorization"></a>Autorizzazione OAuth 2.0 di Swagger

> [!NOTE]
> * L'entità utente che ha creato la risorsa di Azure Digital Twins avrà un'assegnazione di ruolo di amministratore dello spazio e sarà in grado di creare assegnazioni di ruolo aggiuntive per altri utenti. Tali utenti e i relativi ruoli possono essere autorizzati a chiamare le API.

1. Seguire i passaggi di [questa Guida introduttiva](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) per creare e configurare un'applicazione Azure ad. In alternativa, è possibile riutilizzare una registrazione di app esistente.

1. Aggiungere l'URL di risposta seguente alla registrazione dell'app:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Nome  | Sostituire con | Esempio |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | URL della documentazione dell'API REST di gestione trovato nel portale  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Copiare l'ID dell'app Azure AD.

Al termine della registrazione del Azure Active Directory:

1. Selezionare il pulsante **autorizza** nella pagina spavalderia.

    [![selezionare il pulsante di autorizzazione di spavalderia](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Incollare l'ID applicazione nel campo **client_id** .

    [campo client_id di ![spavalderia](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Si verrà quindi reindirizzati al seguente oggetto modale riuscito.

    [![modale Reindirizzamento di spavalderia](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Per altre informazioni sul test interattivo delle richieste protette tramite OAuth 2.0, vedere la [documentazione ufficiale](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui modelli a oggetti e il grafico di intelligence spaziale di Gemelli digitali di Azure, vedere [Informazioni sui modelli a oggetti di Gemelli digitali di Azure](./concepts-objectmodel-spatialgraph.md).

- Per informazioni su come eseguire l'autenticazione con l'API di gestione, vedere [Eseguire l'autenticazione con le API](./security-authenticating-apis.md).