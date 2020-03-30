---
title: Come usare la documentazione di riferimento Swagger - Azure Digital Twins Documenti Microsoft
description: Comprendere come usare la documentazione di riferimento di Swagger per Gemelli digitali di Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 27874e5731bd6fb9821e7aeda9333adbdbb79099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023294"
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

[![Informazioni generali sul riepilogo Swagger e sull'API](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Sono elencati anche i modelli a oggetti dell'API Gestione.

[![Modelli Swagger elencati nella parte inferiore dell'interfaccia utente di Swagger](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

È possibile selezionare ogni modello a oggetti elencato per visualizzare un riepilogo più dettagliato degli attributi principali.

[![Modelli Swagger ampliati per leggere il contenuto dei modelli](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

I modelli a oggetti Swagger generati sono utili per leggere tutti gli [oggetti e le API](./concepts-objectmodel-spatialgraph.md)di Azure Digital Twins disponibili. Gli sviluppatori possono usare questa risorsa quando compilano soluzioni in Gemelli digitali di Azure.

## <a name="endpoint-summary"></a>Riepilogo degli endpoint

Swagger fornisce anche una panoramica completa di tutti gli endpoint che compongono l'API Gestione.

Ogni endpoint elencato include anche le informazioni obbligatorie sulla richiesta, tra cui:

* I parametri obbligatori.
* I tipi di dati dei parametri obbligatori.
* Metodo HTTP per l'accesso alla risorsa.

[![Swagger endpoints displayed in Swagger UI](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Selezionare ogni risorsa per visualizzarne il contenuto aggiuntivo per ottenere una panoramica più dettagliata.

## <a name="use-swagger-to-test-endpoints"></a>Usare Swagger per testare gli endpoint

Una delle funzionalità più potenti offerte da Swagger è la possibilità di testare un endpoint API direttamente dall'interfaccia utente della documentazione.

Dopo aver selezionato un endpoint specifico, verrà visualizzato il pulsante **Prova.**

[![Swagger Prova il pulsante](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Espandere la sezione per visualizzare i campi di input per ogni parametro obbligatorio e facoltativo. Immettere i valori corretti, quindi selezionare **Esegui**.

[![Swagger Provane l'esempio di risultato](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Dopo l'esecuzione del test, è possibile convalidare i dati della risposta.

## <a name="swagger-response-data"></a>Dati della risposta di Swagger

Ogni endpoint elencato include anche i dati del corpo della risposta per convalidare lo sviluppo e i test. Questi esempi includono i codici di stato e JSON per le richieste HTTP riuscite.

[![Esempio di risposta JSON SwaggerSwagger JSON response example](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Gli esempi includono anche i codici di errore per facilitare il debug o migliorare i test non superati.

## <a name="swagger-oauth-20-authorization"></a>Autorizzazione OAuth 2.0 di Swagger

> [!NOTE]
> * L'entità utente che ha creato la risorsa Azure Digital Twins disporrà di un'assegnazione di ruolo Disserenziata space e sarà in grado di creare assegnazioni di ruolo aggiuntive per altri utenti. Tali utenti e i relativi ruoli possono essere autorizzati a chiamare le API.

1. Seguire i passaggi [della Guida introduttiva](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) per creare e configurare un'applicazione Azure Active Directory.Follow the steps in the Quickstart to create and configure an Azure Active Directory application. In alternativa, puoi riutilizzare una registrazione dell'app esistente.

1. Aggiungere il seguente **URI di reindirizzamento** alla registrazione dell'app Azure Active Directory:

    [![Registrare l'URL di reindirizzamento Swagger in AADRegister Swagger redirect url in AAD](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Nome  | Sostituire con | Esempio |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | URL della documentazione dell'API REST di gestione trovato nel portale  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Selezionare la casella di controllo Token di accesso **concedibili** > **impliciti** per consentire l'utilizzo del flusso di concessione implicita OAuth 2.0. Selezionare **Configura**, quindi **Salva**.

1. Copiare **l'ID client** dell'app Azure Active Directory.

Dopo aver completato la registrazione di Azure Active Directory:After completing the Azure Active Directory registration:

1. Seleziona il pulsante **Autorizza** nella pagina swagger.

    [![Selezionare il pulsante Autorizzazione Swagger](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Incollare l'ID applicazione nel campo **client_id.**

    [![Swagger client_id campo](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Si verrà quindi reindirizzati al seguente modale di esito positivo.

    [![Modalità di reindirizzamento Swagger](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Per ulteriori informazioni sui test interattivi sulle richieste protette da OAuth 2.0, leggere la [documentazione ufficiale](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui modelli a oggetti e il grafico di intelligence spaziale di Gemelli digitali di Azure, vedere [Informazioni sui modelli a oggetti di Gemelli digitali di Azure](./concepts-objectmodel-spatialgraph.md).

- Per informazioni su come eseguire l'autenticazione con l'API di gestione, vedere [Eseguire l'autenticazione con le API](./security-authenticating-apis.md).
