---
title: Introduzione ai metadati OpenAPI in Funzioni di Azure | Documentazione Microsoft
description: Introduzione al supporto per OpenAPI in Funzioni di Azure
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: e066cc2ee99b14d13c5238266513edf61ecbe3e1
ms.lasthandoff: 04/15/2017


---
# <a name="creating-openapi-20-swagger-metadata-for-a-function-app-preview"></a>Creazione di metadati OpenAPI 2.0 (Swagger) per un'app per le funzioni (anteprima)

Questo documento contiene la procedura dettagliata per la creazione di una definizione OpenAPI per una semplice API ospitata in Funzioni di Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-swagger"></a>Definizione di OpenAPI (Swagger)
[Swagger Metadata](http://swagger.io/) è un file che definisce le funzionalità e le modalità di funzionamento dell'API e consente a una funzione che ospita un'API REST di essere usata con un'ampia gamma di altri prodotti software. Offerte Microsoft quali PowerApps e [App per le API](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier), nonché strumenti di sviluppatori di terze parti come [Postman](https://www.getpostman.com/docs/importing_swagger) e [altri pacchetti](http://swagger.io/tools/) consentono di usare l'API con una definizione Swagger.

## <a name="prepare-function"></a>Creazione di una funzione con una API semplice
  Per creare una definizione OpenAPI, è innanzitutto necessario creare una funzione con un'API semplice. Se si dispone già di un'API ospitata in un'app per le funzioni, è possibile passare direttamente alla sezione successiva.
1. Creare una nuova app per le funzioni.
  1. [Portale di Azure](https://portal.azure.com)  >  `+ New` > Eseguire una ricerca per "App per le funzioni"
1. Creare una nuova funzione trigger HTTP all'interno della nuova app per le funzioni
  1. La funzione è già popolata con codice che definisce un'API REST semplice.
  1. Ogni stringa passata alla funzione come parametro di query o nel corpo viene restituita come "Hello {input}"
1. Passare alla scheda `Integrate` della nuova funzione trigger HTTP
  1. Attivare/disattivare `Allowed HTTP methods` su `Selected methods`
  1. In `Selected HTTP methods` deselezionare ogni verbo tranne POST.
    ![Metodi HTTP selezionati](./media/functions-api-definition-getting-started/selectedHTTPmethods.png)
  1. Questo passaggio semplifica la definizione dell'API in un secondo momento.

## <a name="enable"></a>Abilitazione del supporto per la definizione API
1. Passare a `your function name` > `API Definition (preview)`
1. Impostare `API Definition Source` su `Internal`
  1. Questo passaggio consente a una suite di opzioni di OpenAPI per l'app per le funzioni, incluso un endpoint, di ospitare un file OpenAPI dal dominio dell'app, una copia inline dell'[editor di OpenAPI](http://editor.swagger.io)e un generatore di definizioni di avvio rapido.
![Definizioni attivate](./media/functions-api-definition-getting-started/enabledefinition.png)

## <a name="create-definition"></a>Creazione della definizione dell'API da un modello
1. Fare clic su`Generate API Definition template`.
  1. Questo passaggio esegue la scansione dell'app per le funzioni per le funzioni HTTP trigger e usa le informazioni contenute in functions.json per generare un documento OpenAPI.
2. Aggiungere un oggetto operation a `paths: /api/yourfunctionroute post:`
  1. Il documento OpenAPI di avvio rapido è una struttura di un documento OpenAPI completo. Sono tuttavia necessari altri metadati affinché si tratti di una definizione OpenAPI completa, ad esempio oggetti operazione e modelli di risposta.
  1. L'oggetto operazione di esempio include una sezione produce/usa, un oggetto parametro e un oggetto risposta.
  
  ```yaml
      post:
        operationId: /api/yourfunctionroute/post
        consumes: [application/json]
        produces: [application/json]
        parameters:
          - name: name
            in: body
            description: Your name
            x-ms-summary: Your name
            required: true
            schema:
              type: object
              properties:
                name:
                  type: string
        description: >-
          Replace with Operation Object
          #http://swagger.io/specification/#operationObject
        responses:
          '200':
            description: A Greeting
            x-ms-summary: Your name
            schema:
              type: string
        security:
          - apikeyQuery: []
  ```

> [!NOTE]
>  x-ms-summary indica il nome visualizzato in App per la logica, Flow e PowerApps.
>
> Per altre informazioni, leggere [Personalizzare la definizione Swagger per PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

3. Fare clic su `save` per salvare le modifiche ![Aggiunta di definizioni del modello](./media/functions-api-definition-getting-started/addingtemplate.png)

## <a name="use-definition"></a>Utilizzo della definizione dell'API
1. Copiare l'`API definition URL` e incollarlo in una nuova scheda del browser per visualizzare il documento OpenAPI non elaborato.
1. Tale URL consente di importare il documento OpenAPI in qualsiasi strumento per finalità di test e integrazione.
  1. Molte risorse di Azure sono in grado di importare automaticamente il documento OpenAPI usando l'URL di definizione API salvato nelle impostazioni dell'app per le funzioni. Nel codice sorgente della definizione API `Function`, tale URL è già stato aggiornato.


## <a name="test-definition"></a>Usare l'interfaccia utente di Swagger per testare la definizione dell'API
Esistono strumenti di test integrati nell'interfaccia utente dell'editor della definizione API. Aggiungere la chiave API e quindi usare il pulsante `Try this operation` in ogni metodo. Lo strumento usa la definizione API per formattare le richieste; se le risposte conseguenti hanno esito positivo, la definizione è corretta.

### <a name="steps"></a>Passaggi:

1. Copiare la chiave API della funzione
  1. La chiave API è reperibile nella funzione trigger HTTP in `function name`  >  `Keys`  >  `Function Keys` 
   ![tasto funzione](./media/functions-api-definition-getting-started/functionkey.png)
1. Passare alla pagina `API Definition (preview)`.
  1. Fare clic su `Authenticate` e aggiungere la chiave API della funzione all'oggetto security, nella parte superiore.
  ![Chiave OpenAPI](./media/functions-api-definition-getting-started/definitionTest auth.png)
1. Selezionare `/api/yourfunctionroute` > `POST`
1. Fare clic su `Try it out` e immettere un nome per il test
1. Nel 
 ![test di definizione dell'API](./media/functions-api-definition-getting-started/definitionTest.png) di `Pretty` verrà visualizzato l'esito positivo

## <a name="next-steps"></a>Passaggi successivi
* [Definizione di OpenAPI in Panoramica di Funzioni di Azure](functions-api-definition.md)
  * Leggere la documentazione completa per altre informazioni sul supporto per OpenAPI.
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)  
  * Informazioni di riferimento per programmatori in merito alla codifica delle funzioni e alla definizione di trigger e associazioni.
* [Repository GitHub di Funzioni di Azure](https://github.com/Azure/Azure-Functions/)
  * In Github di Funzioni di Azure è possibile inviare commenti e suggerimenti relativi all'anteprima del supporto della definizione API. La pagina consente inoltre di inviare suggerimenti su eventuali elementi che si desidera vedere aggiornati.

