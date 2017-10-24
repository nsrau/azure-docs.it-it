---
title: Panoramica dei connettori personalizzati - App per la logica di Azure | Microsoft Docs
description: Panoramica della creazione di connettori personalizzati per supportare ed espandere gli scenari di integrazione
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 0515b21bc7c7cc737e14fda016606bbea1aab476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="custom-connectors-overview"></a>Panoramica dei connettori personalizzati

È possibile creare flussi di lavoro o app senza scrivere codice usando [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/), [Microsoft Flow](https://flow.microsoft.com) o [Microsoft PowerApps](https://powerapps.microsoft.com). Per facilitare l'integrazione di dati e processi aziendali, questi servizi offrono [più di 100 connettori](../connectors/apis-list.md), non solo per i servizi e prodotti Microsoft come Azure e SQL Server, ma anche per altri servizi, come GitHub, Salesforce, Twitter e altri. 

In alcuni casi, tuttavia, potrebbe essere necessario chiamare API, servizi e sistemi che non sono disponibili come connettori predefiniti. Per supportare scenari su misura per le esigenze aziendali e di produttività degli utenti, è possibile creare *connettori personalizzati* con trigger e azioni specifici.
I connettori personalizzati consentono di migliorare le opportunità di integrazione, la portata, l'individuabilità e l'uso del servizio o prodotto, quindi potenzialmente di incrementare e promuovere l'adozione di tale servizio o prodotto da parte dei clienti.

Questo diagramma, ad esempio, mostra l'interazione tra un'API Web, un connettore di App per la logica personalizzato creato per tale API e un'app per la logica che interagisce con tale API tramite il connettore personalizzato:

![Panoramica concettuale per un'API Web, un connettore personalizzato e un'app per la logica](./media/custom-connector-overview/custom-connector-conceptual.png)

Questa panoramica illustra le attività di alto livello generali per la creazione, la protezione, la registrazione e l'uso dei connettori, oltre alla loro condivisione o certificazione facoltativa:

![Passaggi per la creazione di connettori personalizzati](./media/custom-connector-overview/authoring-steps.png)

## <a name="prerequisites"></a>Prerequisiti

Per creare un connettore da zero, sono necessari di questi elementi:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iniziare con un [account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, iscriversi per ottenere una [sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

* Un'API RESTful con un tipo di accesso autenticato. Per altre informazioni, vedere [Creare connettori personalizzati dalle API Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Per informazioni sui modelli disponibili per i trigger e le azioni del connettore, vedere [Creare API personalizzate che è possibile chiamare da flussi di lavoro delle app per la logica](../logic-apps/logic-apps-create-api-app.md).

* Qualsiasi elemento tra i seguenti:

  * Un [file OpenAPI 2.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md), precedentemente noto come Swagger
  * L'URL per una definizione OpenAPI
  * Una [raccolta di Postman](../logic-apps/custom-connector-api-postman-collection.md) per l'API 

  Se non si dispone di uno di questi elementi, è possibile fare riferimento alle istruzioni fornite.

* Facoltativo: un'immagine da usare come icona per il connettore personalizzato

## <a name="1-build-your-restful-api"></a>1. Creare l'API RESTful

Tecnicamente, un connettore è un wrapper per un'API REST basata su una specifica OpenAPI (in precedenza Swagger) e consente al servizio sottostante di comunicare con App per la logica, Flow o PowerApps. Prima di poter creare il connettore personalizzato è quindi necessaria un'API completa funzionante. 

Per l'API è possibile usare qualsiasi linguaggio e piattaforma. Per le tecnologie Microsoft, è consigliabile una di queste piattaforme:

* [Funzioni di Azure](https://azure.microsoft.com/services/functions/)
* [App Web di Azure](https://azure.microsoft.com/services/app-service/web/)
* [App per le API di Azure](https://azure.microsoft.com/services/app-service/api/)

Ad esempio, questa esercitazione mostra [come creare un connettore personalizzato da un'API Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Per informazioni sui modelli disponibili per i trigger e le azioni del connettore, vedere [Creare API personalizzate che è possibile chiamare da flussi di lavoro delle app per la logica](../logic-apps/logic-apps-create-api-app.md).

## <a name="2-secure-your-api"></a>2. Proteggere l'API

È possibile usare questi standard di autenticazione per le API e i connettori:

   * [OAuth 2.0](https://oauth.net/2/), inclusi [Azure Active Directory](https://azure.microsoft.com/develop/identity/) o servizi specifici, ad esempio Dropbox, GitHub e a SalesForce
   * OAuth 2.0 generica
   * [Autenticazione di base](https://swagger.io/docs/specification/authentication/basic-authentication/)
   * [Chiave API](https://swagger.io/docs/specification/authentication/api-keys/)

È possibile configurare l'autenticazione di Azure Active Directory (Azure AD) per l'API nel portale di Azure, in modo da non dover implementare l'autenticazione tramite codice. Oppure, è possibile richiedere e applicare l'autenticazione attraverso il codice dell'API. 

Per altre informazioni, seguire le esercitazioni appropriate:

* [App per la logica: Proteggere il connettore personalizzato](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Flow: Proteggere il connettore personalizzato](https://ms.flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/)
* [PowerApps: Proteggere il connettore personalizzato](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)

## <a name="3-describe-your-api"></a>3. Descrivere l'API 

Supponendo che l'API disponga di un tipo di accesso autenticato, è necessario descrivere l'interfaccia e le operazioni dell'API in modo che App per la logica, Flow o PowerApps possano comunicare con l'API.
Usare una di queste definizioni standard del settore:

* Un [file OpenAPI 2.0](https://swagger.io/). È possibile iniziare da un file OpenAPI esistente.

  Se si ha familiarità con OpenAPI, vedere [Getting started with Swagger](http://swagger.io/getting-started/) (Introduzione a Swagger) nel sito swagger.io.
  Per un file OpenAPI di esempio, vedere la [documentazione dell'API Analisi del testo](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/export?DocumentFormat=Swagger&ApiName=Azure). 

* Una raccolta di Postman, che genera automaticamente un file OpenAPI. Se non è già disponibile un file OpenAPI e non si vuole crearne uno, è comunque possibile creare facilmente un connettore personalizzato usando una raccolta di Postman.

  Se non si conosce Postman, [installare l'app Postman](https://www.getpostman.com/apps) dal relativo sito. Per altre informazioni , vedere [Descrivere API e connettori personalizzati con Postman](../logic-apps/custom-connector-api-postman-collection.md).

> [!IMPORTANT]
> Le dimensioni del file devono essere minori di 1 MB.

Dietro le quinte, App per la logica, Flow e PowerApps usano OpenAPI, analizzano una raccolta di Postman e convertono la raccolta in un file di definizione OpenAPI. Anche se OpenAPI 2.0 e le raccolte di Postman usano formati diversi, entrambi sono documenti indipendenti dal linguaggio e leggibili dai computer che descrivono le operazioni e i parametri dell'API. È possibile generare questi documenti da vari strumenti, in base al linguaggio e alla piattaforma usati dall'API. È anche possibile creare un file OpenAPI quando si registra il connettore.

Ad esempio, è possibile creare un file OpenAPI o una raccolta di Postman da qualsiasi endpoint dell'API REST, tra cui:

* Connettori disponibili pubblicamente, ad esempio, [Spotify](https://developer.spotify.com/), [Slack](https://api.slack.com/), [Rackspace](http://docs.rackspace.com/) e così via

* Un'API creata e distribuita in qualsiasi provider di hosting cloud, ad esempio Azure, Heroku, Google Cloud e così via

* Un'API line-of-business personalizzata distribuita nella rete, ma solo se tale API è esposta nella rete Internet pubblica

## <a name="4-register-your-connector"></a>4. Registrare il connettore

Il processo di registrazione consente ad App per la logica, Flow o PowerApps di comprendere le caratteristiche dell'API, inclusi la descrizione, l'autenticazione richiesta e le operazioni, compresi i parametri e gli output per ogni operazione. Quando si avvia il processo di registrazione, è possibile specificare un file OpenAPI o una raccolta di Postman, in modo da popolare automaticamente i campi dei metadati nella procedura guidata di registrazione. È possibile modificare i valori di questi campi in qualsiasi momento.

![Descrivere l'API](./media/custom-connector-overview/choose-api-definition-file.png)

Per registrare il connettore, seguire l'esercitazione appropriata:

* [App per la logica: Registrare il connettore](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow: Registrare il connettore](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps: Registrare il connettore](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)

## <a name="5-use-your-connector-in-a-logic-app-flow-or-app"></a>5. Usare il connettore in un'app per la logica, in un flusso o in un'app 

È possibile usare il connettore nello stesso modo in cui si usano i connettori gestiti da Microsoft. Ad esempio, in un flusso di lavoro di un'app per la logica, aggiungere il connettore personalizzato in modo da creare una connessione all'API e chiamare qualsiasi operazione fornita dall'API nello stesso modo in cui si chiamano le operazioni per i connettori gestiti da Microsoft.

## <a name="6-share-your-connector"></a>6. Condividere il connettore 

È possibile condividere il connettore con gli utenti dell'organizzazione, nello stesso in cui si condividono le risorse in App per la logica, Flow o PowerApps. Benché facoltativo, in alcuni scenari si potrebbe voler condividere i connettori con altri utenti.

I connettori personalizzati registrati ma non certificati funzionano come i connettori gestiti da Microsoft, ma sono visibili e disponibili *solo* all'utente che ha creato il connettore e a quelli che hanno lo stesso tenant di Azure Active Directory e la stessa sottoscrizione di Azure per le app per la logica nell'area in cui queste app vengono distribuite. Il passaggio successivo descrive come è possibile condividere il connettore con utenti esterni senza questi limiti, ad esempio con tutti gli utenti di App per la logica, Flow e PowerApps.

> [!IMPORTANT]
> Se si condivide un connettore, altri potrebbero iniziare a dipendere dal connettore. 
> ***Se si elimina il connettore, vengono eliminate anche tutte le connessioni al connettore.***

* [App per la logica: Condividere il connettore](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow: Condividere il connettore](https://ms.flow.microsoft.com/documentation/register-custom-api/#share-your-custom-connector)
* [PowerApps: Condividere il connettore](https://powerapps.microsoft.com/tutorials/register-custom-api/#share-your-custom-connector)

## <a name="7-certify-your-connector"></a>7. Certificare il connettore

Facoltativamente, per condividere il connettore con tutti gli utenti di App per la logica, Flow e PowerApps, inviare il connettore per la certificazione di Microsoft. Questo processo esamina il connettore, verifica la conformità tecnica e del contenuto, quindi convalida le funzionalità per App per la logica, Flow e PowerApps prima che Microsoft possa pubblicare il connettore. Informazioni su come [Inviare i connettori per la certificazione di Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Supporto

* Per ottenere supporto per onboarding e sviluppo, inviare un messaggio di posta elettronica a [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com). Microsoft monitora con regolarità questo account per esaminare le domande e i problemi degli sviluppatori, per poi inoltrarli al team appropriato. 

* Per informazioni sulle domande frequenti, vedere [Domande frequenti sui connettori personalizzati](../logic-apps/custom-connector-faq.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare un connettore personalizzato da un'API Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Configurare l'autenticazione per i connettori personalizzati](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Descrivere API e connettori personalizzati con Postman](../logic-apps/custom-connector-api-postman-collection.md)
* [Inviare i connettori per la certificazione di Microsoft](../logic-apps/custom-connector-submit-certification.md)
