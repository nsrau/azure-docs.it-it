---
title: Aggiungere e chiamare le funzioni di Azure da App per la logica di Azure
description: Aggiungere ed eseguire funzioni di Azure da App per la logica
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.topic: article
ms.date: 06/04/2019
ms.reviewer: klam, LADocs
ms.openlocfilehash: 524b927ec0966199c51cdee93e920d7b847139ae
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495071"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Chiamare le funzioni di Azure da App per la logica di Azure

Quando si desidera eseguire il codice che esegue un processo specifico nell'App per la logica, è possibile creare una funzione personalizzata usando [funzioni di Azure](../azure-functions/functions-overview.md). Questo servizio consente di creare Node. js, C#, e F# funziona in modo da non dover creare un'app completa o dell'infrastruttura per eseguire il codice. È anche possibile [chiamare le app per la logica dall'interno di Funzioni di Azure](#call-logic-app). Funzioni di Azure prevede l'elaborazione serverless nel cloud ed è utile per eseguire attività come quelle indicate di seguito:

* Estendere il comportamento dell'app per la logica con funzioni in Node.js o C#.
* Eseguire i calcoli nel flusso di lavoro dell'app per la logica.
* Applicare formattazione avanzata o campi di elaborazione nelle app per la logica.

Per eseguire frammenti di codice senza la creazione di funzioni di Azure, informazioni su come [aggiungere ed eseguire il codice inline](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> Integrazione tra App per la logica e funzioni di Azure attualmente non funziona con gli slot abilitati.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un'app funzioni di Azure, che è un contenitore per le funzioni di Azure, con la funzione di Azure. Se non si dispone di un'app per le funzioni, [occorre prima di tutto crearne una](../azure-functions/functions-create-first-azure-function.md). È quindi possibile creare la funzione sia all'esterno di app per la logica nel portale di Azure, oppure [all'interno dell'app per la logica](#create-function-designer) nella finestra di progettazione di App per la logica.

* Quando si lavora con le App per la logica, gli stessi requisiti valgono per le funzioni e App per le funzioni sia nuovo o esistente:

  * L'app per le funzioni e l'app per la logica deve usare la stessa sottoscrizione di Azure.

  * Nuova App per le funzioni è necessario usare .NET o JavaScript come lo stack di runtime. Quando si aggiunge una nuova funzione per App per le funzioni esistente, è possibile selezionare C# o JavaScript.

  * La funzione Usa il **trigger HTTP** modello.

    Il modello di trigger HTTP può accettare contenuto del tipo `application/json` dell'app per la logica. Quando si aggiunge una funzione di Azure per app per la logica, progettazione App per la logica mostra le funzioni personalizzate che vengono create da questo modello nella sottoscrizione di Azure.

  * La funzione non usa le route personalizzate a meno che non sono stati definiti un [definizione OpenAPI](../azure-functions/functions-openapi-definition.md) (precedentemente noti come una [file Swagger](https://swagger.io/)).

  * Se si dispone di una definizione OpenAPI per la funzione, la finestra di progettazione di App per la logica ti offre una più completa esperienza quando si lavora con i parametri di funzione. Prima che la propria app per la logica possa trovare e accedere alle funzioni con definizioni OpenAPI, [impostare l'app per le funzioni seguendo questa procedura](#function-swagger).

* L'app per la logica in cui si intende aggiungere la funzione., incluso un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) come primo passo nell'app per la logica

  Prima di poter aggiungere le azioni che eseguono funzioni, app per la logica deve iniziare con un trigger. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Trovare le funzioni che sono associate descrizioni OpenAPI

Per un'esperienza più completa quando si lavora con i parametri di funzione in Progettazione App per la logica [generare una definizione OpenAPI](../azure-functions/functions-openapi-definition.md), precedentemente noto come una [file Swagger](https://swagger.io/), per la funzione. Per impostare la propria app per le funzioni in modo che l'app per la logica possa trovare e usare funzioni che includono le descrizioni Swagger, eseguire la procedura seguente:

1. Assicurarsi che l'app per le funzioni è attivamente in esecuzione.

1. Nell'app per le funzioni, impostare [Cross-Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) in modo che tutte le origini sono consentite seguendo questa procedura:

   1. Dal **App per le funzioni** selezionare app per le funzioni. Nel riquadro di destra, selezionare **funzionalità della piattaforma** > **CORS**.

      ![Selezionare la propria app per le funzioni > "Funzionalità della piattaforma" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. Sotto **CORS**, aggiungere l'asterisco ( **`*`** ) carattere jolly di caratteri, ma rimuovere tutte le altri origini nell'elenco e scegliere **Salva**.

      ![Impostare "CORS* per il carattere jolly"*"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Accedere ai valori delle proprietà all'interno delle richieste HTTP

Le funzioni webhook possono accettare richieste HTTP come input e passarle ad altre funzioni. Sebbene App per la logica disponga ad esempio di [funzioni che convertono i valori DateTime](../logic-apps/workflow-definition-language-functions-reference.md), questa funzione JavaScript di esempio basilare mostra come accedere a una proprietà all'interno di un oggetto richiesta passato alla funzione ed eseguire le operazioni sul valore di tale proprietà. Per accedere alle proprietà all'interno di oggetti, questo esempio usa l'[operatore punto (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors):

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Ecco cosa accade all'interno di questa funzione:

1. La funzione crea una variabile `data` e le assegna l'oggetto `body` all'interno dell'oggetto `request`. La funzione usa l'operatore punto (.) per fare riferimento all'oggetto `body` all'interno dell'oggetto `request`:

   ```javascript
   var data = request.body;
   ```

1. La funzione può ora accedere alla proprietà `date` attraverso la variabile `data` e convertire il valore della proprietà dal tipo DateTime nel tipo DateString chiamando la funzione `ToDateString()`. La funzione restituisce anche il risultato tramite la proprietà `body` nella risposta della funzione:

   ```javascript
   body: data.date.ToDateString();
   ```

Dopo aver creato la funzione di Azure, seguire la procedura per [aggiungere le funzioni alle app per la logica](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Creare funzioni all'interno delle app per la logica

Prima di poter creare una funzione di Azure a partire da all'interno dell'app per la logica con progettazione App per la logica, è necessario disporre prima di tutto un'app per le funzioni di Azure, che è un contenitore per le funzioni. Se non si dispone di un'app per le funzioni, occorre prima di tutto crearne una. Vedere [Creare la prima funzione nel portale di Azure](../azure-functions/functions-create-first-azure-function.md).

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Per creare e aggiungere la funzione, seguire la procedura applicabile allo scenario specifico:

   * Sotto l'ultimo passaggio nel flusso di lavoro dell'app per la logica, scegliere **Nuovo passaggio**.

   * Tra i passaggi esistenti nel flusso di lavoro dell'app per la logica, spostare il puntatore del mouse sulla freccia, scegliere il segno più (+) e quindi selezionare **Add an action** (Aggiungi un'azione).

1. Nella casella di ricerca immettere "funzioni di Azure" come filtro. Nell'elenco di azioni selezionare questa azione: **Scegliere una funzione di Azure - Funzioni di Azure**

   ![Cercare "funzioni di Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Nell'elenco delle app per le funzioni selezionare l'app per le funzioni desiderata. Dopo aver aperto l'elenco di azioni, selezionare questa azione: **Funzioni di Azure - creare una nuova funzione**

   ![Selezionare l'app per le funzioni desiderata](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. Nell'editor delle definizioni di funzione definire la funzione:

   1. Nella casella **Nome funzione** immettere un nome per la funzione.

   1. Nel **codice** , aggiungere il codice per il modello di funzione, tra cui la risposta e il payload che si desidera restituito all'App per la logica dopo l'esecuzione della funzione.

      ![Definire la funzione](./media/logic-apps-azure-functions/function-definition.png)

      Nel codice del modello, l' *`context`oggetto* fa riferimento ai messaggi inviati dall'app per la logica tramite il campo **Corpo della richiesta** in un passaggio successivo. Per accedere alle proprietà dell'oggetto `context` all'interno della funzione, usare la sintassi seguente:

      `context.body.<property-name>`

      Ad esempio, per fare riferimento alla proprietà `content` all'interno dell'oggetto `context`, usare la sintassi seguente: 

      `context.body.content`

      Il codice del modello include anche una variabile `input`,che archivia il valore dal parametro `data`, in modo che la funzione possa eseguire operazioni su tale valore. All'interno di funzioni JavaScript, la variabile `data` rappresenta anche un collegamento per `context.body`.

      > [!NOTE]
      > La proprietà `body` si applica qui all'oggetto `context` e non corrisponde al token **Corpo** di un'azione di output, che può essere passato anch'esso alla funzione.

   1. Al termine dell'operazione, scegliere **Crea**.

1. Nella casella **Corpo della richiesta**, specificare l'input della funzione, che deve essere formattato in JSON (JavaScript Object Notation).

   Questo input descrive il*contesto di ambiente* o il messaggio che l'app per la logica invia alla funzione. Quando si fa clic nel campo **Corpo della richiesta**, viene visualizzato l'elenco del contenuto dinamico per consentire la selezione dei token per gli output disponibili dai passaggi precedenti. In questo esempio viene specificato che il payload di contesto contiene una proprietà denominata `content` con valore del token **Da** estratto dal trigger di posta elettronica:

   ![Esempio di "Corpo della richiesta" - payload dell'oggetto contesto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Qui non viene eseguito il cast dell'oggetto di contesto come stringa, quindi il contenuto dell'oggetto viene aggiunto direttamente al payload JSON. Se tuttavia il contesto di ambiente non è un token JSON che passa una stringa, un oggetto JSON o una matrice JSON, viene visualizzato un errore. Pertanto, se in questo esempio viene usato invece il token **Ora di ricezione**, è possibile eseguire il cast del contesto di ambiente sotto forma di stringa mediante l'aggiunta di virgolette doppie:  

   ![Cast dell'oggetto come stringa](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Per specificare altri dettagli, ad esempio il metodo da utilizzare, le intestazioni di richiesta o parametri di query, aprire il **Aggiungi nuovo parametro** elencare e selezionare le opzioni desiderate.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Aggiungere funzioni esistenti alle app per la logica

Per chiamare funzioni di Azure esistenti dalle proprie app per la logica, è possibile aggiungere le funzioni di Azure come qualsiasi altra azione in Progettazione app per la logica.

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Sotto il passaggio in cui si desidera aggiungere la funzione, scegliere **nuovo passaggio**e selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere "funzioni di Azure" come filtro. Nell'elenco di azioni selezionare questa azione: **Scegliere una funzione di Azure - Funzioni di Azure**

   ![Cercare "funzioni di Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Nell'elenco delle app per le funzioni selezionare l'app per le funzioni desiderata. Nell'elenco di funzioni che viene visualizzato selezionare la funzione desiderata.

   ![Selezionare l'app per le funzioni e la funzione di Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Per le funzioni che dispongono di definizioni API (descrizioni Swagger) e che sono [impostate in modo che l'app per la logica le trovi e vi acceda](#function-swagger), è possibile selezionare **azioni Swagger**:

   ![Selezionare l'app per le funzioni, le "azioni Swagger" e la funzione di Azure desiderata](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Nella casella **Corpo della richiesta**, specificare l'input della funzione, che deve essere formattato in JSON (JavaScript Object Notation).

   Questo input descrive il*contesto di ambiente* o il messaggio che l'app per la logica invia alla funzione. Quando si sceglie la **corpo della richiesta** viene visualizzato l'elenco di contenuto dinamico campo, in modo che è possibile selezionare i token per gli output dai passaggi precedenti. In questo esempio viene specificato che il payload di contesto contiene una proprietà denominata `content` con valore del token **Da** estratto dal trigger di posta elettronica:

   ![Esempio di "Corpo della richiesta" - payload dell'oggetto contesto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Qui non viene eseguito il cast dell'oggetto di contesto come stringa, quindi il contenuto dell'oggetto viene aggiunto direttamente al payload JSON. Se tuttavia il contesto di ambiente non è un token JSON che passa una stringa, un oggetto JSON o una matrice JSON, viene visualizzato un errore. Pertanto, se in questo esempio viene usato invece il token **Ora di ricezione**, è possibile eseguire il cast del contesto di ambiente sotto forma di stringa mediante l'aggiunta di virgolette doppie:

   ![Cast dell'oggetto come stringa](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Per specificare altri dettagli, ad esempio il metodo da utilizzare, le intestazioni di richiesta o parametri di query, aprire il **Aggiungi nuovo parametro** elencare e selezionare le opzioni desiderate.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Chiamare l'App per la logica da funzioni di Azure

Per attivare un'app per la logica dall'interno di una funzione di Azure, l'app per la logica deve essere avviata con un trigger che disponga di un endpoint richiamabile. Ad esempio, è possibile avviare l'app per la logica con il trigger **HTTP**, **Richiesta**, **Code di Azure** o **Griglia di eventi**. All'interno della funzione, inviare una richiesta HTTP POST all'URL del trigger e includere il payload che deve essere elaborato dall'app per la logica. Per altre informazioni, vedere [Chiamare, attivare o annidare app per la logica](../logic-apps/logic-apps-http-endpoint.md).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [connettori di App per la logica](../connectors/apis-list.md)
