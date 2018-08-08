---
title: Aggiungere ed eseguire codice personalizzato in App per la logica di Azure con Funzioni di Azure | Microsoft Docs
description: Informazioni su come aggiungere ed eseguire frammenti di codice personalizzato in App per la logica di Azure con Funzioni di Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/25/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 20ad738541554279ff9fd6dd6babe90a38676c00
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263191"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>Aggiungere ed eseguire frammenti di codice personalizzato in App per la logica di Azure con Funzioni di Azure

Quando si vuole creare ed eseguire codice semplicemente per risolvere un problema specifico nelle proprie app per la logica, è possibile creare funzioni personalizzate tramite il servizio [Funzioni di Azure](../azure-functions/functions-overview.md). Questo servizio consente di creare ed eseguire frammenti di codice scritti in Node.js o C# nelle app per la logica senza preoccuparsi di creare un'app intera o l'infrastruttura per eseguire il codice. Funzioni di Azure prevede l'elaborazione serverless nel cloud ed è utile per eseguire attività come quelle indicate di seguito:

* Estendere il comportamento dell'app per la logica con funzioni supportate da Node.js o C#.
* Eseguire i calcoli nel flusso di lavoro dell'app per la logica.
* Applicare formattazione avanzata o campi di elaborazione nelle app per la logica.

È anche possibile [chiamare le app per la logica dall'interno di Funzioni di Azure](#call-logic-app).

## <a name="prerequisites"></a>Prerequisiti

Per seguire questo articolo, ecco gli elementi necessari:

* Se non si dispone ancora di una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">registrarsi per creare un account Azure gratuito</a>. 

* L'app per la logica in cui si intende aggiungere la funzione.

  Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) come primo passo nell'app per la logica 

  Prima di poter aggiungere le azioni per eseguire le funzioni, l'app per la logica deve iniziare con un trigger,

* un'app per le funzioni di Azure, che è un contenitore di funzioni di Azure, e la funzione di Azure personalizzata. Se non si dispone di un'app per le funzioni, è necessario [creare la prima app per le funzioni](../azure-functions/functions-create-first-azure-function.md). È quindi possibile creare la funzione [separatamente all'esterno dell'app per la logica](#create-function-external) o [dall'interno dell'app per la logica](#create-function-designer) nella finestra Progettazione app per la logica.

  Sia le funzioni e le app per le funzioni di Azure esistenti che quelle nuove presentano gli stessi requisiti per l'utilizzo con le app per la logica:

  * L'app per le funzioni deve appartenere alla stessa sottoscrizione di Azure a cui appartiene l'app per la logica.

  * La funzione deve usare il modello di funzione **webhook generico** per **JavaScript** o **C#**. Questo modello può accettare contenuto del tipo `application/json` dell'app per la logica. Questi modelli consentono anche a Progettazione app per la logica di trovare e visualizzare le funzioni personalizzate create con questi modelli e aggiunte all'app per la logica.

  * Verificare che la proprietà **Modalità** del modello di funzione sia impostata su **Webhook** e che la proprietà **Tipo di webhook** sia impostata su **Generic JSON** (JSON generico).

    1. Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a>.
    2. Nel menu principale di Azure selezionare **App per le funzioni**. 
    3. Nell'elenco **App per le funzioni** selezionare la propria app per le funzioni, espandere la funzione e selezionare **Integrazione**. 
    4. Verificare che la proprietà **Modalità** del modello sia impostata su **Webhook** e che la proprietà **Tipo di webhook** sia impostata su **Generic JSON** (JSON generico). 

  * Se la funzione include una [definizione API](../azure-functions/functions-openapi-definition.md), precedentemente nota come [file Swagger](http://swagger.io/), Progettazione app per la logica offre un'esperienza più completa per lavorare con i parametri di funzione. 
  Prima che la propria app per la logica possa trovare e accedere alle funzioni con descrizioni Swagger, [impostare l'app per le funzioni seguendo questa procedura](#function-swagger).

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>Creare funzioni all'esterno delle app per la logica

Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> creare l'app per le funzioni di Azure, che deve appartenere alla stessa sottoscrizione di Azure dell'app per la logica, quindi creare la funzione di Azure. Se non si ha familiarità con Funzioni di Azure, leggere come [creare la prima funzione nel portale di Azure](../azure-functions/functions-create-first-azure-function.md), ma prendere nota di questi requisiti per creare funzioni di Azure che è possibile aggiungere e chiamare dalle app per la logica.

* Assicurarsi di avere selezionato il modello di funzione **webhook generico** per **JavaScript** o **C#**.

  ![Webhook generico - JavaScript o C#](./media/logic-apps-azure-functions/generic-webhook.png)

* Dopo aver creato la funzione di Azure, verificare che le proprietà **Modalità** e **Tipo di webhook** del modello siano impostate nel modo appropriato.

  1. Nell'elenco **App per le funzioni** espandere la funzione e selezionare **Integrazione**. 

  2. Verificare che la proprietà **Modalità** del modello sia impostata su **Webhook** e che la proprietà **Tipo di webhook** sia impostata su **Generic JSON** (JSON generico). 

     ![Proprietà di "Integrazione" del modello di funzione](./media/logic-apps-azure-functions/function-integrate-properties.png)

<a name="function-swagger"></a>

* Se si sceglie di [generare una definizione API](../azure-functions/functions-openapi-definition.md), precedentemente nota come [file Swagger](http://swagger.io/), per la propria funzione, è possibile ottenere un'esperienza più completa nell'utilizzo dei parametri di funzione in Progettazione app per la logica. Per impostare la propria app per le funzioni in modo che l'app per la logica possa trovare e accedere a funzioni che includono le descrizioni Swagger:

  * Assicurarsi che l'app per le funzioni sia attivamente in esecuzione.

  * Nell'app per le funzioni impostare la [Condivisione di risorse tra le origini (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) in modo che tutte le origini siano consentite:

    1. Nell'elenco **App per le funzioni** selezionare la propria app per le funzioni > **Funzionalità della piattaforma** > **CORS**.

       ![Selezionare la propria app per le funzioni > "Funzionalità della piattaforma" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

    2. In **CORS** aggiungere il carattere jolly `*`, ma rimuovere tutte le altre origini nell'elenco e scegliere **Salva**.

       ![Selezionare la propria app per le funzioni > "Funzionalità della piattaforma" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### <a name="access-property-values-inside-http-requests"></a>Accedere ai valori delle proprietà all'interno delle richieste HTTP

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

2. La funzione può ora accedere alla proprietà `date` attraverso la variabile `data` e convertire il valore della proprietà dal tipo DateTime nel tipo DateString chiamando la funzione `ToDateString()`. La funzione restituisce anche il risultato tramite la proprietà `body` nella risposta della funzione: 

   ```javascript
   body: data.date.ToDateString();
   ```

Dopo aver creato la funzione di Azure, seguire la procedura per [aggiungere le funzioni alle app per la logica](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Creare funzioni all'interno delle app per la logica

Prima di poter creare una funzione di Azure dall'interno dell'app per la logica in Progettazione app per la logica, è necessario disporre di un'app per le funzioni di Azure, che fungerà da contenitore per le funzioni che si creano. Se non si dispone di un'app per le funzioni, occorre prima di tutto crearne una. Vedere [Creare la prima funzione nel portale di Azure](../azure-functions/functions-create-first-azure-function.md). 

1. Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> aprire l'app per la logica in Progettazione app per la logica. 

2. Nel passaggio in cui si vuole creare e aggiungere la funzione scegliere **Nuovo passaggio** > **Aggiungi un'azione**. 

3. Nella casella di ricerca immettere "funzioni di Azure" come filtro.
Nell'elenco delle azioni selezionare l'azione: **Choose an Azure function - Azure Functions** (Scegliere una funzione di Azure - Funzioni di Azure) 

   ![Cercare "funzioni di Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Nell'elenco delle app per le funzioni selezionare l'app per le funzioni desiderata. Nell'elenco delle azioni che viene visualizzato selezionare l'azione: **Funzioni di Azure - Crea nuova funzione**

   ![Selezionare l'app per le funzioni desiderata](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. Nell'editor delle definizioni di funzione definire la funzione:

   1. Nella casella **Nome funzione** immettere un nome per la funzione. 

   2. Nella casella **Codice** aggiungere il codice della funzione al modello, inclusi la risposta e il payload che si vuole che vengano restituiti all'app per la logica al termine dell'esecuzione della funzione. 
   L'oggetto contesto nel codice del modello descrive il messaggio e il contenuto che l'app per la logica passa alla funzione, ad esempio:

      ![Definire la funzione](./media/logic-apps-azure-functions/function-definition.png)

      All'interno della funzione è possibile fare riferimento alle proprietà nell'oggetto contesto usando questa sintassi:

      ```text
      context.<token-name>.<property-name>
      ```
      Per questo esempio ecco la sintassi che si deve usare:

      ```text
      context.body.content
      ```

   3. Al termine dell'operazione, scegliere **Crea**.

6. Nella casella **Corpo della richiesta** specificare l'oggetto contesto da passare come input della funzione, che deve essere formattato in JSON (JavaScript Object Notation). Quando si fa clic nella casella **Corpo della richiesta**, viene visualizzato l'elenco del contenuto dinamico per consentire la selezione dei token per le proprietà disponibili dai passaggi precedenti. 

   Questo esempio passa l'oggetto nel token **Corpo** dal trigger di posta elettronica:  

   ![Esempio di "Corpo della richiesta" - payload dell'oggetto contesto](./media/logic-apps-azure-functions/function-request-body-example.png)

   In base al contenuto dell'oggetto contesto, Progettazione app per la logica genera un modello di funzione che è successivamente possibile modificare inline. 
   App per la logica crea anche le variabili in base all'oggetto contesto di input.

   In questo esempio non viene eseguito il cast dell'oggetto di contesto come stringa, quindi il contenuto viene aggiunto direttamente al payload JSON. 
   Se tuttavia l'oggetto non è un token JSON, che deve essere una stringa, un oggetto JSON o una matrice JSON, viene visualizzato un errore. 
   Per eseguire il cast dell'oggetto contesto come stringa, aggiungere le virgolette doppie, ad esempio:

   ![Cast dell'oggetto come stringa](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. Per specificare altri dettagli, ad esempio il metodo da usare, le intestazioni di richiesta o i parametri di query, scegliere **Mostra opzioni avanzate**.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Aggiungere funzioni esistenti alle app per la logica

Per chiamare funzioni di Azure esistenti dalle proprie app per la logica, è possibile aggiungere le funzioni di Azure come qualsiasi altra azione in Progettazione app per la logica. 

1. Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> aprire l'app per la logica in Progettazione app per la logica. 

2. Nel passaggio in cui si vuole aggiungere la funzione scegliere **Nuovo passaggio** > **Aggiungi un'azione**. 

3. Nella casella di ricerca immettere "funzioni di Azure" come filtro.
Nell'elenco delle azioni selezionare l'azione: **Choose an Azure function - Azure Functions** (Scegliere una funzione di Azure - Funzioni di Azure) 

   ![Cercare "funzioni di Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Nell'elenco delle app per le funzioni selezionare l'app per le funzioni desiderata. Nell'elenco di funzioni che viene visualizzato selezionare la funzione desiderata. 

   ![Selezionare l'app per le funzioni e la funzione di Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Per le funzioni che dispongono di definizioni API (descrizioni Swagger) e che sono [impostate in modo che l'app per la logica le trovi e vi acceda](#function-swagger), è possibile selezionare **azioni Swagger**:

   ![Selezionare l'app per le funzioni, le "azioni Swagger" e la funzione di Azure desiderata](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. Nella casella **Corpo della richiesta** specificare l'oggetto contesto da passare come input della funzione, che deve essere formattato in JSON (JavaScript Object Notation). Questo oggetto contesto descrive il messaggio e il contenuto che l'app per la logica invia alla funzione. 

   Quando si fa clic nella casella **Corpo della richiesta**, viene visualizzato l'elenco del contenuto dinamico per consentire la selezione dei token per le proprietà disponibili dai passaggi precedenti. 
   Questo esempio passa l'oggetto nel token **Corpo** dal trigger di posta elettronica:

   ![Esempio di "Corpo della richiesta" - payload dell'oggetto contesto](./media/logic-apps-azure-functions/function-request-body-example.png)

   In questo esempio non viene eseguito il cast dell'oggetto di contesto come stringa, quindi il contenuto viene aggiunto direttamente al payload JSON. 
   Se tuttavia l'oggetto non è un token JSON, che deve essere una stringa, un oggetto JSON o una matrice JSON, viene visualizzato un errore. 
   Per eseguire il cast dell'oggetto contesto come stringa, aggiungere le virgolette doppie, ad esempio:

   ![Cast dell'oggetto come stringa](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. Per specificare altri dettagli, ad esempio il metodo da usare, le intestazioni di richiesta o i parametri di query, scegliere **Mostra opzioni avanzate**.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Chiamare le app per la logica da funzioni

Per attivare un'app per la logica dall'interno di una funzione di Azure, l'app per la logica deve disporre di un endpoint richiamabile, più specificamente, un trigger **Richiesta**. Dall'interno della funzione inviare quindi una richiesta HTTP POST all'URL per il trigger **Richiesta** e includere il payload che deve essere elaborato dall'app per la logica. Per altre informazioni, vedere [Chiamare, attivare o annidare app per la logica](../logic-apps/logic-apps-http-endpoint.md). 

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [connettori di App per la logica](../connectors/apis-list.md)
