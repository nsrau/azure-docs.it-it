---
title: Aggiungere e chiamare funzioni di Azure da app per la logica di Azure
description: Aggiungere ed eseguire funzioni di Azure da app per la logica
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.topic: article
ms.date: 10/01/2019
ms.reviewer: klam, LADocs
ms.openlocfilehash: 5b946e36c5da9f122adce1f8e3b99523a789a66f
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73901102"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Chiamare funzioni di Azure da app per la logica di Azure

Quando si vuole eseguire codice che esegue un processo specifico nelle app per la logica, è possibile creare una funzione personalizzata usando [funzioni di Azure](../azure-functions/functions-overview.md). Questo servizio consente di creare funzioni node. js C#, e F# , in modo da non dover compilare un'applicazione o un'infrastruttura completa per eseguire il codice. È anche possibile [chiamare le app per la logica dall'interno di Funzioni di Azure](#call-logic-app). Funzioni di Azure prevede l'elaborazione serverless nel cloud ed è utile per eseguire attività come quelle indicate di seguito:

* Estendere il comportamento dell'app per la logica con funzioni in Node.js o C#.
* Eseguire i calcoli nel flusso di lavoro dell'app per la logica.
* Applicare formattazione avanzata o campi di elaborazione nelle app per la logica.

Per eseguire frammenti di codice senza creare funzioni di Azure, imparare a [aggiungere ed eseguire codice inline](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> L'integrazione tra app per la logica e funzioni di Azure attualmente non funziona con gli slot abilitati.

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* App per le funzioni di Azure, un contenitore per funzioni di Azure, insieme alla funzione di Azure. Se non si dispone di un'app per le funzioni, [occorre prima di tutto crearne una](../azure-functions/functions-create-first-azure-function.md). È quindi possibile creare la funzione all'esterno dell'app per la logica nella portale di Azure o [dall'app](#create-function-designer) per la logica nella finestra di progettazione dell'app per la logica.

* Quando si lavora con le app per la logica, gli stessi requisiti si applicano alle app per le funzioni e alle funzioni, che siano nuove o esistenti:

  * L'app per le funzioni e l'app per la logica devono usare la stessa sottoscrizione di Azure.

  * Le nuove app per le funzioni devono usare .NET o JavaScript come stack di Runtime. Quando si aggiunge una nuova funzione alle app per le funzioni esistenti, è possibile C# selezionare o JavaScript.

  * La funzione usa il modello di **trigger http** .

    Il modello di trigger HTTP può accettare contenuto del tipo `application/json` dell'app per la logica. Quando si aggiunge una funzione di Azure all'app per la logica, la finestra di progettazione dell'app per la logica Mostra le funzioni personalizzate create da questo modello nella sottoscrizione di Azure.

  * La funzione non usa le route personalizzate a meno che non sia stata definita una [definizione openapi](../azure-functions/functions-openapi-definition.md) (precedentemente nota come [file di spavalderia](https://swagger.io/)).

  * Se si ha una definizione OpenAPI per la funzione, progettazione app per la logica offre un'esperienza più completa quando si lavora con i parametri di funzione. Prima che la propria app per la logica possa trovare e accedere alle funzioni con definizioni OpenAPI, [impostare l'app per le funzioni seguendo questa procedura](#function-swagger).

* L'app per la logica in cui si intende aggiungere la funzione., incluso un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) come primo passo nell'app per la logica

  Prima di poter aggiungere azioni che eseguono funzioni, l'app per la logica deve iniziare con un trigger. Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Trova funzioni con descrizioni OpenAPI

Per un'esperienza più completa quando si lavora con i parametri di funzione nella finestra di progettazione di app per la logica, [generare una definizione openapi](../azure-functions/functions-openapi-definition.md), precedentemente nota come [file di spavalderia](https://swagger.io/), per la funzione. Per impostare la propria app per le funzioni in modo che l'app per la logica possa trovare e usare funzioni che includono le descrizioni Swagger, eseguire la procedura seguente:

1. Assicurarsi che l'app per le funzioni sia in esecuzione attivamente.

1. Nell'app per le funzioni configurare la [condivisione di risorse tra le origini (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) in modo che tutte le origini siano consentite attenendosi alla procedura seguente:

   1. Dall'elenco **app** per le funzioni selezionare l'app per le funzioni. Nel riquadro di destra selezionare **funzionalità della piattaforma** > **CORS**.

      ![Selezionare la propria app per le funzioni > "Funzionalità della piattaforma" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. In **CORS**aggiungere il carattere jolly asterisco ( **`*`** ), ma rimuovere tutte le altre origini nell'elenco e selezionare **Salva**.

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

Prima di poter creare una funzione di Azure a partire dall'interno dell'app per la logica usando la finestra di progettazione dell'app per la logica, è necessario avere prima di tutto un'app per le funzioni di Azure, che è un contenitore per le funzioni. Se non si dispone di un'app per le funzioni, occorre prima di tutto crearne una. Vedere [Creare la prima funzione nel portale di Azure](../azure-functions/functions-create-first-azure-function.md).

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Per creare e aggiungere la funzione, seguire la procedura applicabile allo scenario specifico:

   * Nell'ultimo passaggio del flusso di lavoro dell'app per la logica selezionare **nuovo passaggio**.

   * Tra i passaggi esistenti nel flusso di lavoro dell'app per la logica, spostare il puntatore del mouse sulla freccia, selezionare il segno più (+) e quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere "funzioni di Azure" come filtro. Nell'elenco azioni selezionare l'azione **scegliere una funzione di Azure** , ad esempio:

   ![Cercare "funzioni di Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Nell'elenco delle app per le funzioni selezionare l'app per le funzioni desiderata. Dopo l'apertura dell'elenco di azioni, selezionare questa azione: **Crea nuova funzione** .

   ![Selezionare l'app per le funzioni desiderata](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. Nell'editor delle definizioni di funzione definire la funzione:

   1. Nella casella **Nome funzione** immettere un nome per la funzione.

   1. Nella casella **codice** aggiungere il codice al modello di funzione, incluse la risposta e il payload che si vuole restituire all'app per la logica al termine dell'esecuzione della funzione. Al termine, selezionare **Crea**.

   Ad esempio:

   ![Definire la funzione](./media/logic-apps-azure-functions/add-code-function-definition.png)

   Nel codice del modello, l' *`context`oggetto* fa riferimento ai messaggi inviati dall'app per la logica tramite il campo **Corpo della richiesta** in un passaggio successivo. Per accedere alle proprietà dell'oggetto `context` all'interno della funzione, usare la sintassi seguente:

   `context.body.<property-name>`

   Ad esempio, per fare riferimento alla proprietà `content` all'interno dell'oggetto `context`, usare la sintassi seguente:

   `context.body.content`

   Il codice del modello include anche una variabile `input`,che archivia il valore dal parametro `data`, in modo che la funzione possa eseguire operazioni su tale valore. All'interno di funzioni JavaScript, la variabile `data` rappresenta anche un collegamento per `context.body`.

   > [!NOTE]
   > La proprietà `body` si applica qui all'oggetto `context` e non corrisponde al token **Corpo** di un'azione di output, che può essere passato anch'esso alla funzione.

1. Nella casella **Corpo della richiesta**, specificare l'input della funzione, che deve essere formattato in JSON (JavaScript Object Notation).

   Questo input descrive il*contesto di ambiente* o il messaggio che l'app per la logica invia alla funzione. Quando si fa clic nel campo **Corpo della richiesta**, viene visualizzato l'elenco del contenuto dinamico per consentire la selezione dei token per gli output disponibili dai passaggi precedenti. Questo esempio specifica che il payload del contesto contiene una proprietà denominata `content` con il valore del token **from** del trigger di posta elettronica.

   ![Esempio di "Corpo della richiesta" - payload dell'oggetto contesto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Qui non viene eseguito il cast dell'oggetto di contesto come stringa, quindi il contenuto dell'oggetto viene aggiunto direttamente al payload JSON. Se tuttavia il contesto di ambiente non è un token JSON che passa una stringa, un oggetto JSON o una matrice JSON, viene visualizzato un errore. Se, pertanto, in questo esempio è stato utilizzato il token di **tempo ricevuto** , è possibile eseguire il cast dell'oggetto context come stringa aggiungendo virgolette doppie.

   ![Cast dell'oggetto come stringa](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Per specificare altri dettagli, ad esempio il metodo da usare, le intestazioni delle richieste o i parametri di query oppure l'autenticazione, aprire l'elenco **Aggiungi nuovo parametro** e selezionare le opzioni desiderate. Per l'autenticazione, le opzioni variano in base alla funzione selezionata. Vedere [abilitare l'autenticazione per funzioni di Azure](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Aggiungere funzioni esistenti alle app per la logica

Per chiamare funzioni di Azure esistenti dalle proprie app per la logica, è possibile aggiungere le funzioni di Azure come qualsiasi altra azione in Progettazione app per la logica.

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Nel passaggio in cui si vuole aggiungere la funzione selezionare **nuovo passaggio**.

1. In **scegliere un'azione**, nella casella di ricerca, immettere "funzioni di Azure" come filtro. Nell'elenco azioni selezionare l'azione **scegliere una funzione di Azure** .

   ![Cercare "funzioni di Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Nell'elenco delle app per le funzioni selezionare l'app per le funzioni desiderata. Nell'elenco di funzioni che viene visualizzato selezionare la funzione desiderata.

   ![Selezionare l'app per le funzioni e la funzione di Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Per le funzioni con definizioni di API (descrizioni di spavalderia) e sono [configurate in modo che l'app per la logica possa trovare e accedere a tali funzioni](#function-swagger), è possibile selezionare **azioni di spavalderia**.

   ![Selezionare l'app per le funzioni, "azioni spavalderia" e la funzione di Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Nella casella **Corpo della richiesta**, specificare l'input della funzione, che deve essere formattato in JSON (JavaScript Object Notation).

   Questo input descrive il*contesto di ambiente* o il messaggio che l'app per la logica invia alla funzione. Quando si fa clic nel campo **corpo della richiesta** , viene visualizzato l'elenco contenuto dinamico, in modo che sia possibile selezionare i token per gli output dei passaggi precedenti. Questo esempio specifica che il payload del contesto contiene una proprietà denominata `content` con il valore del token **from** del trigger di posta elettronica.

   ![Esempio di "Corpo della richiesta" - payload dell'oggetto contesto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Qui non viene eseguito il cast dell'oggetto di contesto come stringa, quindi il contenuto dell'oggetto viene aggiunto direttamente al payload JSON. Se tuttavia il contesto di ambiente non è un token JSON che passa una stringa, un oggetto JSON o una matrice JSON, viene visualizzato un errore. Pertanto, se in questo esempio viene usato invece il token **Ora di ricezione**, è possibile eseguire il cast del contesto di ambiente sotto forma di stringa mediante l'aggiunta di virgolette doppie:

   ![Cast dell'oggetto come stringa](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Per specificare altri dettagli, ad esempio il metodo da usare, le intestazioni delle richieste, i parametri di query o l'autenticazione, aprire l'elenco **Aggiungi nuovo parametro** e selezionare le opzioni desiderate. Per l'autenticazione, le opzioni variano in base alla funzione selezionata. Vedere [abilitare l'autenticazione in funzioni di Azure](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Chiamare app per la logica da funzioni di Azure

Per attivare un'app per la logica dall'interno di una funzione di Azure, l'app per la logica deve essere avviata con un trigger che disponga di un endpoint richiamabile. Ad esempio, è possibile avviare l'app per la logica con il trigger **HTTP**, **Richiesta**, **Code di Azure** o **Griglia di eventi**. All'interno della funzione, inviare una richiesta HTTP POST all'URL del trigger e includere il payload che deve essere elaborato dall'app per la logica. Per altre informazioni, vedere [Chiamare, attivare o annidare app per la logica](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Abilitare l'autenticazione per funzioni di Azure

Per autenticare l'accesso alle risorse in altri tenant di Azure Active Directory (Azure AD) senza dover accedere e fornire credenziali o segreti, l'app per la logica può usare un' [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) (precedentemente nota come identità del servizio gestita o MSI). Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti. Scopri di più sui [servizi di Azure che supportano identità gestite per l'autenticazione Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Se si configura l'app per la logica per l'uso dell'identità gestita assegnata dal sistema, le funzioni di Azure nell'app per la logica possono usare anche la stessa identità per l'autenticazione. Per altre informazioni sul supporto dell'autenticazione per funzioni di Azure nelle app per la logica, vedere [aggiungere l'autenticazione alle chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

Per configurare e usare l'identità assegnata dal sistema con la funzione, seguire questa procedura:

1. Abilitare l'identità assegnata dal sistema nell'app per la logica e impostare l'accesso dell'identità sulla risorsa di destinazione. Vedere [autenticare l'accesso alle risorse di Azure usando identità gestite in app per la logica di Azure](../logic-apps/create-managed-service-identity.md).

1. Abilitare l'autenticazione nella funzione di Azure e nell'app per le funzioni attenendosi alla procedura seguente:

   * [Configurare l'autenticazione anonima nella funzione](#set-authentication-function-app)
   * [Configurare Azure AD autenticazione nell'app per le funzioni](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Configurare l'autenticazione anonima nella funzione

Per usare l'identità assegnata dal sistema dell'app per la logica nella funzione di Azure, il livello di autenticazione della funzione è stato impostato su anonimo. In caso contrario, l'app per la logica genera un errore "richiesta non valida".

1. Nella [portale di Azure](https://portal.azure.com)trovare e selezionare l'app per le funzioni. Questa procedura USA "FabrikamFunctionApp" come app per le funzioni di esempio.

1. Nel riquadro app per le funzioni selezionare **funzionalità della piattaforma**. In **strumenti di sviluppo**selezionare **strumenti avanzati (kudu)** .

   ![Apri strumenti avanzati per Kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Nella barra del titolo del sito Web Kudu scegliere **cmd**dal menu **console di debug** .

   ![Dal menu della console di debug selezionare l'opzione "CMD"](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Quando viene visualizzata la pagina successiva, dall'elenco cartella selezionare **sito** > **wwwroot** > *la funzione-* . Questa procedura USA "FabrikamAzureFunction" come funzione di esempio.

   ![Selezionare "site" > "wwwroot" > funzione](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Aprire il file di `function.json` per la modifica.

   ![Fare clic su modifica per il file "function. JSON"](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. Nell'oggetto `bindings` controllare se la proprietà `authLevel` esiste. Se la proprietà esiste, impostare il valore della proprietà su `anonymous`. In caso contrario, aggiungere la proprietà e impostare il valore.

   ![Aggiungere la proprietà "authLevel" e impostarla su "Anonymous"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Al termine, salvare le impostazioni e quindi continuare con la sezione successiva.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Configurare l'autenticazione Azure AD per l'app per le funzioni

Prima di iniziare questa attività, trovare e mettere a disposizione questi valori per un uso successivo:

* ID oggetto generato per l'identità assegnata dal sistema che rappresenta l'app per la logica

  * Per generare questo ID oggetto, [abilitare l'identità assegnata dal sistema dell'app per la logica](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app).

  * In caso contrario, per trovare questo ID oggetto, aprire l'app per la logica nella finestra di progettazione dell'app per la logica. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Identity** > **System assegnato**.

* ID directory per il tenant in Azure Active Directory (Azure AD)

  Per ottenere l'ID directory del tenant, è possibile eseguire il comando [`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) PowerShell. In alternativa, nella portale di Azure seguire questa procedura:

  1. Nella [portale di Azure](https://portal.azure.com)trovare e selezionare l'app per le funzioni.

  1. Trovare e selezionare il tenant del Azure AD. Questi passaggi usano "Fabrikam" come tenant di esempio.

  1. Nel menu del tenant, in **Gestisci**, selezionare **Proprietà**.

  1. Copiare l'ID directory del tenant, ad esempio, e salvarlo per un uso successivo.

     ![Trovare e copiare Azure AD ID directory del tenant](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* ID risorsa per la risorsa di destinazione a cui si vuole accedere

  * Per trovare questi ID di risorsa, esaminare i [servizi di Azure che supportano Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

  > [!IMPORTANT]
  > Questo ID risorsa deve corrispondere esattamente al valore previsto da Azure AD, incluse le barre finali obbligatorie.

  Questo ID di risorsa è anche lo stesso valore usato in un secondo momento nella proprietà **audience** quando si [Configura l'azione della funzione per usare l'identità assegnata dal sistema](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

A questo punto si è pronti per configurare Azure AD autenticazione per l'app per le funzioni.

1. Nella [portale di Azure](https://portal.azure.com)trovare e selezionare l'app per le funzioni.

1. Nel riquadro app per le funzioni selezionare **funzionalità della piattaforma**. In **rete**selezionare **autenticazione/autorizzazione**.

   ![Visualizzare le impostazioni di autenticazione e autorizzazione](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Modificare l'impostazione di **autenticazione del servizio app** **su on**. Nell'elenco **azione da eseguire quando la richiesta non è autenticato** selezionare **Accedi con Azure Active Directory**. In **Provider di autenticazione** fare clic su **Azure Active Directory**.

   ![Attivare l'autenticazione con Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. Nel riquadro **impostazioni Azure Active Directory** seguire questa procedura:

   1. Impostare la **modalità di gestione** su **avanzata**.

   1. Nella proprietà **ID client** immettere l'ID oggetto per l'identità assegnata dal sistema dell'app per la logica.

   1. Nella proprietà **URL autorità di certificazione** immettere l'url del `https://sts.windows.net/` e aggiungere l'ID directory del tenant di Azure ad.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. Nella proprietà **allowed token Audiences** immettere l'ID risorsa per la risorsa di destinazione a cui si vuole accedere.

      Questo ID risorsa è lo stesso valore usato in un secondo momento nella proprietà **audience** quando si [Configura l'azione della funzione per usare l'identità assegnata dal sistema](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   A questo punto, la versione ha un aspetto simile all'esempio seguente:

   ![Impostazioni di autenticazione Azure Active Directory](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Al termine, fare clic su **OK**.

1. Tornare alla finestra di progettazione dell'app per la logica e seguire i [passaggi per autenticare l'accesso con l'identità gestita](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [connettori di App per la logica](../connectors/apis-list.md)
