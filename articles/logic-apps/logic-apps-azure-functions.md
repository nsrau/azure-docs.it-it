---
title: Aggiungere e chiamare Funzioni di Azure da App per la logica di AzureAdd and call Azure Functions from Azure Logic Apps
description: Chiamare ed eseguire codice personalizzato in Funzioni di Azure da attività e flussi di lavoro automatizzati in App per la logica di AzureCall and run custom code in your Azure Functions from automated tasks and workflows in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.openlocfilehash: 68975f21ab810398da969384db4d3bddd22f1bd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284122"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Chiamare le funzioni di Azure dalle app per la logica di AzureCall Azure functions from Azure Logic Apps

Quando si vuole eseguire codice che esegue un processo specifico nelle app per la logica, è possibile creare una funzione personalizzata usando Funzioni di [Azure](../azure-functions/functions-overview.md). Questo servizio consente di creare le funzioni Node.js, C , e F , in modo da non dover compilare un'app completa o un'infrastruttura per eseguire il codice. È anche possibile [chiamare le app per la logica dall'interno di Funzioni di Azure](#call-logic-app). Funzioni di Azure prevede l'elaborazione serverless nel cloud ed è utile per eseguire attività come quelle indicate di seguito:

* Estendere il comportamento dell'app per la logica con funzioni in Node.js o C#.
* Eseguire i calcoli nel flusso di lavoro dell'app per la logica.
* Applicare formattazione avanzata o campi di elaborazione nelle app per la logica.

Per eseguire frammenti di codice senza creare funzioni di Azure, vedere come [aggiungere ed eseguire codice inline.](../logic-apps/logic-apps-add-run-inline-code.md)

> [!NOTE]
> L'integrazione tra app per la logica e funzioni di Azure attualmente non funziona con gli slot abilitati.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un'app per le funzioni di Azure, che è un contenitore per le funzioni di Azure, insieme alla funzione di Azure.An Azure function app, which is a container for Azure functions, along with your Azure function. Se non si dispone di un'app per le funzioni, [occorre prima di tutto crearne una](../azure-functions/functions-create-first-azure-function.md). È quindi possibile creare la funzione all'esterno dell'app per la logica nel portale di Azure o [dall'interno dell'app per](#create-function-designer) la logica in Progettazione app per la logica.

* Quando si usano app per la logica, gli stessi requisiti si applicano alle app e alle funzioni di funzione, indipendentemente dal fatto che siano esistenti o nuove:When working with logic apps, the same requirements apply to function apps and functions whether they are existing or new:

  * L'app per le funzioni e l'app per la logica devono usare la stessa sottoscrizione di Azure.Your function app and logic app must use the same Azure subscription.

  * Le nuove app per le funzioni devono usare .NET o JavaScript come stack di runtime. Quando si aggiunge una nuova funzione alle app per le funzioni esistenti, è possibile selezionare C 'NET o JavaScript.When you add a new function to existing function apps, you can select either C'è or JavaScript.

  * La funzione utilizza il modello di **trigger HTTP.**

    Il modello di trigger HTTP può accettare contenuto del tipo `application/json` dell'app per la logica. Quando si aggiunge una funzione di Azure all'app per la logica, Progettazione app per la logica mostra le funzioni personalizzate create da questo modello all'interno della sottoscrizione di Azure.When you add an Azure function to your logic app, the Logic App Designer shows custom functions that are created from this template within your Azure subscription.

  * La funzione non utilizza route personalizzate a meno che non sia stata definita una [definizione OpenAPI](../azure-functions/functions-openapi-definition.md) (precedentemente nota come [file Swagger](https://swagger.io/)).

  * Se si dispone di una definizione OpenAPI per la funzione, Progettazione applicazioni logiche offre un'esperienza più completa quando il lavoro con i parametri di funzione. Prima che la propria app per la logica possa trovare e accedere alle funzioni con definizioni OpenAPI, [impostare l'app per le funzioni seguendo questa procedura](#function-swagger).

* L'app per la logica in cui si intende aggiungere la funzione., incluso un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) come primo passo nell'app per la logica

  Prima di poter aggiungere azioni che eseguono funzioni, l'app per la logica deve iniziare con un trigger. Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Trovare funzioni con descrizioni OpenAPIFind functions that have OpenAPI descriptions

Per un'esperienza più completa quando si lavora con i parametri di funzione in Progettazione applicazioni logiche, generare una [definizione OpenAPI](../azure-functions/functions-openapi-definition.md), precedentemente nota come [file Swagger](https://swagger.io/), per la funzione. Per impostare la propria app per le funzioni in modo che l'app per la logica possa trovare e usare funzioni che includono le descrizioni Swagger, eseguire la procedura seguente:

1. Assicurarsi che l'app per le funzioni sia attivamente in esecuzione.

1. Nell'app per le funzioni, configura [Condivisione risorse cross-Origin (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) in modo che tutte le origini siano consentite seguendo questi passaggi:

   1. Nell'elenco **App** per le funzioni selezionare l'app per le funzioni. Nel riquadro di destra, selezionare **Funzionalità della piattaforma** > **CORS**.

      ![Selezionare la propria app per le funzioni > "Funzionalità della piattaforma" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. In **CORS**aggiungere il**`*`** carattere jolly asterisco ( ), ma rimuovere tutte le altre origini nell'elenco e selezionare **Salva**.

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

Prima di poter creare una funzione di Azure a partire dall'interno dell'app per la logica usando la finestra di progettazione app per la logica, è necessario disporre di un'app per le funzioni di Azure, che è un contenitore per le funzioni. Se non si dispone di un'app per le funzioni, occorre prima di tutto crearne una. Vedere [Creare la prima funzione nel portale di Azure](../azure-functions/functions-create-first-azure-function.md).

1. Nel [portale di Azure](https://portal.azure.com)aprire l'app per la logica in Progettazione app per la logica.

1. Per creare e aggiungere la funzione, seguire la procedura applicabile allo scenario specifico:

   * Nell'ultimo passaggio del flusso di lavoro dell'app per la logica selezionare **Nuovo passaggio**.

   * Tra i passaggi esistenti nel flusso di lavoro dell'app per la logica, spostare il puntatore del mouse sulla freccia, selezionare il segno più (-) e quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere "funzioni di Azure" come filtro. Nell'elenco delle azioni selezionare l'azione **Scegli una funzione Azure,** ad esempio:From the actions list, select the Choose an Azure function action, for example:

   ![Cercare "funzioni di Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Nell'elenco delle app per le funzioni selezionare l'app per le funzioni desiderata. Dopo l'apertura dell'elenco delle azioni, selezionare questa azione: **Crea nuova funzione**

   ![Selezionare l'app per le funzioni desiderata](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. Nell'editor delle definizioni di funzione definire la funzione:

   1. Nella casella **Nome funzione** immettere un nome per la funzione.

   1. Nella casella **Codice** aggiungere il codice al modello di funzione, inclusi la risposta e il payload che si desidera vengano restituiti all'app per la logica al termine dell'esecuzione della funzione. Al termine, selezionare **Crea**.

   Ad esempio:

   ![Definire la funzione](./media/logic-apps-azure-functions/add-code-function-definition.png)

   Nel codice del modello, * `context` l'oggetto* fa riferimento al messaggio inviato dall'app per la logica tramite il campo **Corpo richiesta** in un passaggio successivo. Per accedere alle proprietà dell'oggetto `context` all'interno della funzione, usare la sintassi seguente:

   `context.body.<property-name>`

   Ad esempio, per fare riferimento alla proprietà `content` all'interno dell'oggetto `context`, usare la sintassi seguente:

   `context.body.content`

   Il codice del modello include anche una variabile `input`,che archivia il valore dal parametro `data`, in modo che la funzione possa eseguire operazioni su tale valore. All'interno di funzioni JavaScript, la variabile `data` rappresenta anche un collegamento per `context.body`.

   > [!NOTE]
   > La proprietà `body` si applica qui all'oggetto `context` e non corrisponde al token **Corpo** di un'azione di output, che può essere passato anch'esso alla funzione.

1. Nella casella **Corpo della richiesta**, specificare l'input della funzione, che deve essere formattato in JSON (JavaScript Object Notation).

   Questo input descrive il*contesto di ambiente* o il messaggio che l'app per la logica invia alla funzione. Quando si fa clic nel campo **Corpo della richiesta**, viene visualizzato l'elenco del contenuto dinamico per consentire la selezione dei token per gli output disponibili dai passaggi precedenti. Questo esempio specifica che il payload `content` di contesto contiene una proprietà denominata con il valore del token **From** dal trigger di posta elettronica.

   ![Esempio di "Corpo della richiesta" - payload dell'oggetto contesto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Qui non viene eseguito il cast dell'oggetto di contesto come stringa, quindi il contenuto dell'oggetto viene aggiunto direttamente al payload JSON. Se tuttavia il contesto di ambiente non è un token JSON che passa una stringa, un oggetto JSON o una matrice JSON, viene visualizzato un errore. Pertanto, se in questo esempio è stato usato il token **Received Time,** è possibile eseguire il cast dell'oggetto di contesto come stringa aggiungendo virgolette doppie.

   ![Cast dell'oggetto come stringa](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Per specificare altri dettagli, ad esempio il metodo da utilizzare, le intestazioni di richiesta o i parametri di query oppure l'autenticazione, aprire l'elenco **Aggiungi nuovo parametro** e selezionare le opzioni desiderate. Per l'autenticazione, le opzioni disponibili variano in base alla funzione selezionata. Vedere [Abilitare l'autenticazione per](#enable-authentication-functions)le funzioni di Azure.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Aggiungere funzioni esistenti alle app per la logica

Per chiamare funzioni di Azure esistenti dalle proprie app per la logica, è possibile aggiungere le funzioni di Azure come qualsiasi altra azione in Progettazione app per la logica.

1. Nel [portale di Azure](https://portal.azure.com)aprire l'app per la logica in Progettazione app per la logica.

1. Nel passaggio in cui si desidera aggiungere la funzione selezionare **Nuovo passaggio**.

1. In **Scegliere un'azione**immettere "funzioni azzurre" nella casella di ricerca. Nell'elenco delle azioni selezionare l'azione **Scegli una funzione Azure.From the actions** list, select the Choose an Azure function action.

   ![Cercare "funzioni di Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Nell'elenco delle app per le funzioni selezionare l'app per le funzioni desiderata. Nell'elenco di funzioni che viene visualizzato selezionare la funzione desiderata.

   ![Selezionare l'app per le funzioni e la funzione di Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Per le funzioni con definizioni API (descrizioni Swagger) e [configurate in modo che l'app per](#function-swagger)la logica possa trovare e accedere a tali funzioni, è possibile selezionare **azioni Swagger**.

   ![Selezionare l'app per le funzioni, le "azioni Swagger" e la funzione di AzureSelect your function app, "Swagger actions" and your Azure function](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Nella casella **Corpo della richiesta**, specificare l'input della funzione, che deve essere formattato in JSON (JavaScript Object Notation).

   Questo input descrive il*contesto di ambiente* o il messaggio che l'app per la logica invia alla funzione. Quando si fa clic nel campo **Corpo richiesta,** viene visualizzato l'elenco del contenuto dinamico in modo che sia possibile selezionare i token per gli output dei passaggi precedenti. Questo esempio specifica che il payload `content` di contesto contiene una proprietà denominata con il valore del token **From** dal trigger di posta elettronica.

   ![Esempio di "Corpo della richiesta" - payload dell'oggetto contesto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Qui non viene eseguito il cast dell'oggetto di contesto come stringa, quindi il contenuto dell'oggetto viene aggiunto direttamente al payload JSON. Se tuttavia il contesto di ambiente non è un token JSON che passa una stringa, un oggetto JSON o una matrice JSON, viene visualizzato un errore. Pertanto, se in questo esempio viene usato invece il token **Ora di ricezione**, è possibile eseguire il cast del contesto di ambiente sotto forma di stringa mediante l'aggiunta di virgolette doppie:

   ![Cast dell'oggetto come stringa](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Per specificare altri dettagli, ad esempio il metodo da utilizzare, le intestazioni delle richieste, i parametri di query o l'autenticazione, aprire l'elenco **Aggiungi nuovo parametro** e selezionare le opzioni desiderate. Per l'autenticazione, le opzioni disponibili variano in base alla funzione selezionata. Vedere [Abilitare l'autenticazione nelle funzioni](#enable-authentication-functions)di Azure .See Enable authentication in Azure functions .

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Chiamare app per la logica dalle funzioni di AzureCall logic apps from Azure functions

Per attivare un'app per la logica dall'interno di una funzione di Azure, l'app per la logica deve essere avviata con un trigger che disponga di un endpoint richiamabile. Ad esempio, è possibile avviare l'app per la logica con il trigger **HTTP**, **Richiesta**, **Code di Azure** o **Griglia di eventi**. All'interno della funzione, inviare una richiesta HTTP POST all'URL del trigger e includere il payload che deve essere elaborato dall'app per la logica. Per altre informazioni, vedere [Chiamare, attivare o annidare app per la logica](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Abilitare l'autenticazione per le funzioni di AzureEnable authentication for Azure functions

Per autenticare l'accesso alle risorse in altri tenant di Azure Active Directory (Azure AD) senza dover accedere e fornire credenziali o segreti, l'app per la logica può usare [un'identità gestita](../active-directory/managed-identities-azure-resources/overview.md) (precedentemente nota come identità del servizio gestito o MSI). Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti. Altre informazioni sui servizi di [Azure che supportano le identità gestite per l'autenticazione](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)di Azure AD.

Se configuri l'app per la logica per usare l'identità assegnata dal sistema o un'identità assegnata dall'utente creata manualmente, le funzioni di Azure nell'app per la logica possono usare anche la stessa identità per l'autenticazione. Per altre informazioni sul supporto dell'autenticazione per le funzioni di Azure nelle app per la logica, vedere [Aggiungere l'autenticazione alle chiamate in uscita.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

Per impostare e utilizzare l'identità gestita con la funzione, attenersi alla seguente procedura:

1. Abilitare l'identità gestita nell'app per la logica e configurare l'accesso dell'identità alla risorsa di destinazione. Vedere [Autenticare l'accesso alle risorse di Azure usando le identità gestite nelle app per la logica di Azure.See Authenticate access to Azure resources by using managed identities in Azure Logic Apps](../logic-apps/create-managed-service-identity.md).

1. Abilitare l'autenticazione nell'app per le funzioni e le funzioni di Azure eseguendo la procedura seguente:Enable authentication in your Azure function and function app by following these steps:

   * [Impostare l'autenticazione anonima nella funzioneSet up anonymous authentication in your function](#set-authentication-function-app)
   * [Configurare l'autenticazione di Azure AD nell'app per le funzioniSet up Azure AD authentication in your function app](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Impostare l'autenticazione anonima nella funzioneSet up anonymous authentication in your function

Per usare l'identità gestita dell'app per la logica nella funzione di Azure, il livello di autenticazione della funzione è stato impostato su anonimo. In caso contrario, l'app per la logica genera un errore "BadRequest".

1. Nel [portale di Azure](https://portal.azure.com)individuare e selezionare l'app per le funzioni. Questi passaggi usano "FabrikamFunctionApp" come app per le funzioni di esempio.

1. Nel riquadro dell'app per le funzioni selezionare **Funzionalità della piattaforma**. In Strumenti di **sviluppo**selezionare Strumenti **avanzati (Kudu)**.

   ![Aprire gli strumenti avanzati per Kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Nella barra del titolo del sito Web Kudu, dal menu **Console di debug,** selezionare **CMD**.

   ![Dal menu della console di debug, selezionare l'opzione "CMD"](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Dopo aver visualizzato la pagina successiva, dall'elenco delle cartelle selezionare **il sito** > **wwwroot** > *your-function*. Questi passaggi usano "FabrikamAzureFunction" come funzione di esempio.

   ![Seleziona "sito" > "wwwroot" > funzione](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Aprire `function.json` il file per la modifica.

   ![Fare clic su modifica per il file "function.json"](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. Nell'oggetto `bindings` verificare `authLevel` se la proprietà esiste. Se la proprietà esiste, impostare il valore della proprietà su `anonymous`. In caso contrario, aggiungere tale proprietà e impostare il valore.

   ![Aggiungere la proprietà "authLevel" e impostarla su "anonymous"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Al termine, salvare le impostazioni e quindi passare alla sezione successiva.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Configurare l'autenticazione di Azure AD per l'app per le funzioni

Prima di iniziare questa attività, individuare e mettere da parte questi valori per un utilizzo successivo:Before you start this task, find and put these values toside for later use:

* ID oggetto generato per l'identità assegnata dal sistema che rappresenta l'app per la logica

  * Per generare questo ID oggetto, [abilitare l'identità assegnata dal sistema dell'app per la logica.](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app)

  * In caso contrario, per trovare questo ID oggetto, aprire l'app per la logica in Progettazione app per la logica. Nel menu dell'app per la logica, in **Impostazioni,** selezionare **Identità** > **assegnata dal sistema**.

* ID di directory per il tenant in Azure Active Directory (Azure AD)The directory ID for your tenant in Azure Active Directory (Azure AD)

  Per ottenere l'ID di directory del [`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) tenant, è possibile eseguire il comando Powershell.To get your tenant's directory ID, you can run the Powershell command. In alternativa, nel portale di Azure, eseguire la procedura seguente:Or, in the Azure portal, follow these steps:

  1. Nel [portale di Azure](https://portal.azure.com)individuare e selezionare l'app per le funzioni.

  1. Trovare e selezionare il tenant di Azure AD. Questi passaggi usano "Fabrikam" come tenant di esempio.

  1. Nel menu del tenant, in **Gestisci,** selezionare **Proprietà**.

  1. Copiare, ad esempio, l'ID di directory del tenant e salvarlo per un utilizzo successivo.

     ![Trovare e copiare l'ID di directory del tenant di Azure ADFind and copy Azure AD tenant's directory ID](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* ID risorsa per la risorsa di destinazione a cui si desidera accedere

  * Per trovare questi ID risorsa, esaminare i servizi di [Azure che supportano Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

  > [!IMPORTANT]
  > Questo ID risorsa deve corrispondere esattamente al valore previsto da Azure AD, incluse eventuali barre finali necessarie.

  Questo ID risorsa è anche lo stesso valore utilizzato successivamente nella proprietà **Audience** quando si [imposta l'azione funzione per l'utilizzo dell'identità assegnata dal sistema.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

A questo punto è possibile configurare l'autenticazione di Azure AD per l'app per le funzioni.

1. Nel [portale di Azure](https://portal.azure.com)individuare e selezionare l'app per le funzioni.

1. Nel riquadro dell'app per le funzioni selezionare **Funzionalità della piattaforma**. In **Rete**selezionare **Autenticazione/Autorizzazione**.

   ![Visualizzare le impostazioni di autenticazione e autorizzazione](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Modificare **l'impostazione Autenticazione servizio app** su **Attivato**. Nell'elenco **Azione da eseguire quando la richiesta non è autenticata** selezionare Accedi con Azure Active **Directory**. In **Provider di autenticazione** fare clic su **Azure Active Directory**.

   ![Attivare l'autenticazione con Azure ADTurn on authentication with Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. Nel riquadro Impostazioni di Azure Active Directory eseguire la procedura seguente:On the **Azure Active Directory Settings** pane, follow these steps:

   1. Impostare **la modalità di gestione** su **Avanzate**.

   1. Nella proprietà **ID client** immettere l'ID oggetto per l'identità assegnata dal sistema dell'app per la logica.

   1. Nella proprietà **URL autorità emittente** immettere l'URL `https://sts.windows.net/` e aggiungere l'ID di directory del tenant di Azure AD.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. Nella proprietà Gruppi di **destinatari token consentiti** immettere l'ID risorsa per la risorsa di destinazione a cui si desidera accedere.

      Questo ID risorsa è lo stesso valore utilizzato successivamente nella proprietà **Audience** quando si [imposta l'azione della funzione per l'utilizzo dell'identità assegnata dal sistema.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

   A questo punto, la versione è simile a questo esempio:At this point, your version looks similar to this example:

   ![Impostazioni di autenticazione di Azure Active DirectoryAzure Active Directory authentication settings](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Al termine, fare clic su **OK**.

1. Tornare a Progettazione app per la logica e seguire i [passaggi per autenticare l'accesso con l'identità gestita.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [connettori di App per la logica](../connectors/apis-list.md)
