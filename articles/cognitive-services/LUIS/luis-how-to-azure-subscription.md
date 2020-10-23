---
title: Come usare le chiavi di creazione e di runtime-LUIS
description: Quando si usa per la prima volta Language Understanding (LUIS), non è necessario creare una chiave di creazione. Quando si intende pubblicare l'app, usare l'endpoint di runtime, è necessario creare e assegnare la chiave di runtime all'app.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: adc0c253648ed7ae869a20b92c42e7f6478501b7
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151584"
---
# <a name="create-luis-resources"></a>Creare risorse LUIS

Le risorse di creazione e di runtime di query di stima forniscono l'autenticazione per l'app LUIS e l'endpoint di stima.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>Risorse LUIS

LUIS consente tre tipi di risorse di Azure e una risorsa non di Azure:

|Risorsa|Scopo|Servizio cognitivo `kind`|Servizio cognitivo `type`|
|--|--|--|--|
|Creazione di una risorsa|Consente di creare, gestire, eseguire il training, testare e pubblicare le applicazioni. [Creare una risorsa Luis authoring](luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) se si vuole creare Luis Apps programtically o dal portale Luis. È necessario prima [eseguire la migrazione dell'account Luis](luis-migration-authoring.md#what-is-migration) per poter collegare le risorse Authroring di Azure all'applicazione. È possibile controllare le autorizzazioni per la risorsa di creazione assegnando persone al [ruolo Collaboratore](#contributions-from-other-authors). <br><br> Esiste un avialable di livello per la risorsa LUIS Authoring:<br> * **Risorsa di authoring F0 gratuita** che offre 1 milione di transazioni di creazione gratuite e 1000 richieste di endpoint di stima gratuite per i test mensili. |`LUIS.Authoring`|`Cognitive Services`|
|Risorsa di stima| Dopo la pubblicazione dell'applicazione LUIS, utilizzare la risorsa/chiave di stima per eseguire query sulle richieste dell'endpoint di stima. Creare una risorsa di stima LUIS prima che l'app client richieda le stime oltre le 1.000 richieste fornite dalla risorsa di creazione o di avvio. <br><br> Sono disponibili due livelli di avialble per la risorsa di stima:<br> * **Risorsa di stima F0 gratuita** che fornisce 10.000 richieste di endpoint di stima gratuite mensili<br> * **Risorsa di stima S0 standard** che corrisponde al livello a pagamento. [Ulteriori informazioni sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Risorsa di avvio/versione di valutazione|Consente di creare, gestire, eseguire il training, testare e pubblicare le applicazioni. Questa operazione viene creata da predefinito se si sceglie l'opzione della risorsa di avvio durante la prima iscrizione di TP LUIS. Tuttavia, la chiave di avvio sarà deprecata e tutti gli utenti LUIS dovranno eseguire la [migrazione dei propri account](luis-migration-authoring.md#what-is-migration) e collegare le applicazioni Luis a una risorsa di creazione. Questa risorsa non fornisce le autorizzazioni per il controllo degli accessi in base al ruolo di Azure, come la risorsa di creazione. <br><br> Proprio come la risorsa di creazione, la risorsa iniziale offre 1 milione di transazioni di creazione gratuite e 1000 richieste di endpoint di stima gratuite.|-|Non è una risorsa di Azure|
|[Chiave di risorsa multiservizio di servizi cognitivi](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Richieste di endpoint di stima delle query condivise con LUIS e altri servizi cognitivi supportati.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> Sono disponibili due tipi di risorse F0 (livello gratuito) fornite da LUIS. Uno per la creazione di transazioni e uno per le transazioni di stima. Se si sta esaurendo la quota gratuita per le transazioni di stima, assicurarsi di usare la risorsa di stima F0 che fornisce un 10.000 di transazioni gratuite mensili e non la risorsa di creazione che fornisce le transazioni di stima 1000 mensili.

Al termine del processo di creazione delle risorse di Azure, [assegnare la risorsa](#assign-a-resource-to-an-app) all'app nel portale Luis.

È importante creare app LUIS in [aree](luis-reference-regions.md#publishing-regions) in cui si desidera pubblicare ed eseguire query.

## <a name="resource-ownership"></a>Proprietà delle risorse

Una risorsa di Azure, ad esempio LUIS, appartiene alla sottoscrizione che contiene la risorsa.

Per trasferire la proprietà di una risorsa, è possibile effettuare una delle operazioni seguenti:
* Trasferire la [Proprietà](../../cost-management-billing/manage/billing-subscription-transfer.md) della sottoscrizione
* Esportare l'app LUIS come file e quindi importare l'app in una sottoscrizione diversa. L'esportazione è disponibile nella pagina **app personali** del portale Luis.


## <a name="resource-limits"></a>Limiti delle risorse

### <a name="authoring-key-creation-limits"></a>Creazione di limiti per la creazione di chiavi

È possibile creare fino a 10 chiavi di creazione per ogni area per sottoscrizione.

Vedere [limiti chiave](luis-limits.md#key-limits) e [aree di Azure](luis-reference-regions.md).

Le regioni di pubblicazione sono diverse dalle regioni di creazione. Assicurarsi di creare un'app nell'area di creazione corrispondente all'area di pubblicazione in cui si vuole che venga individuata l'applicazione client.

### <a name="key-usage-limit-errors"></a>Errori limite utilizzo chiave

I limiti di utilizzo sono basati sul piano tariffario.

Se si supera la quota di transazioni al secondo, viene visualizzato un errore HTTP 429. Se si supera la quota di transazioni al mese, viene visualizzato un errore HTTP 403.


### <a name="reset-authoring-key"></a>Reimpostare la chiave di creazione

Per la creazione di app [migrate delle risorse](luis-migration-authoring.md) : se la chiave di creazione è compromessa, reimpostare la chiave nella portale di Azure nella pagina **chiavi** della risorsa di creazione.

Per le app che non sono ancora state migrate: la chiave viene reimpostata su tutte le app nel portale LUIS. Se si creano le app tramite le API di creazione, è necessario modificare il valore di OCP-gestione API-Subscription-Key nella nuova chiave.

### <a name="regenerate-azure-key"></a>Rigenera chiave di Azure

Rigenerare le chiavi di Azure dalla portale di Azure, nella pagina **chiavi** .


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Proprietà, accesso e sicurezza dell'app

Un'app è definita dalle risorse di Azure, che è determinata dalla sottoscrizione del proprietario.

È possibile spostare l'app LUIS. Usare le risorse di documentazione seguenti nell'portale di Azure o nell'interfaccia della riga di comando di Azure:

* [Spostare l'app tra le risorse di authoring LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Spostare una risorsa in un nuovo gruppo di risorse o sottoscrizione](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Spostare una risorsa all'interno della stessa sottoscrizione o tra sottoscrizioni](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Contributi di altri autori

Per la creazione di app [migrate delle risorse](luis-migration-authoring.md) : i _collaboratori_ vengono gestiti nel portale di Azure per la risorsa di creazione, usando la pagina **controllo di accesso (IAM)** . Informazioni [su come aggiungere un utente](luis-how-to-collaborate.md), usando l'indirizzo di posta elettronica del collaboratore e il ruolo _collaboratore_ .

Per le app che non sono state ancora migrate: tutti i _collaboratori_ vengono gestiti nel portale Luis dalla pagina **manage-> collaboratori** .

### <a name="query-prediction-access-for-private-and-public-apps"></a>Accesso alla stima delle query per le app private e pubbliche

Per un'app **privata** , l'accesso al runtime di stima delle query è disponibile per i proprietari e i collaboratori. Per un'app **pubblica** , l'accesso in fase di esecuzione è disponibile per tutti gli utenti che dispongono del proprio [servizio cognitivo](../cognitive-services-apis-create-account.md) di Azure o della risorsa di runtime [Luis](#create-resources-in-the-azure-portal) e l'ID dell'app pubblica.

Attualmente non è disponibile un catalogo di app pubbliche.

### <a name="authoring-permissions-and-access"></a>Creazione di autorizzazioni e accesso
L'accesso all'app dal portale [Luis](luis-reference-regions.md#luis-website) o dalle [API di creazione](https://go.microsoft.com/fwlink/?linkid=2092087) è controllato dalla risorsa di creazione di Azure.

Il proprietario e tutti i collaboratori possono accedere per creare l'app.

|L'accesso in creazione include|Note|
|--|--|
|Aggiungere o rimuovere chiavi endpoint||
|Esportare una versione||
|Esportare i log di endpoint||
|Importare una versione||
|Rendere pubblica un'app|Quando un'app è pubblica, chiunque disponga di una chiave di creazione o endpoint può eseguire una query nell'app.|
|Modificare il modello|
|Pubblica|
|Rivedere le espressioni endpoint per l'[apprendimento attivo](luis-how-to-review-endpoint-utterances.md)|
|Eseguire il training|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Accesso al runtime dell'endpoint di stima

L'accesso per eseguire query sull'endpoint di stima è controllato da un'impostazione nella pagina **informazioni sull'applicazione** nella sezione **Gestisci** .

|[Endpoint privato](#runtime-security-for-private-apps)|[Endpoint pubblico](#runtime-security-for-public-apps)|
|:--|:--|
|Disponibile per proprietari e collaboratori|Disponibile per proprietario, collaboratori e altri utenti che conoscono l'ID app|

È possibile controllare chi vede la chiave del runtime di LUIS chiamandola in un ambiente da server a server. Se si utilizza LUIS da un bot, la connessione tra il bot e LUIS è già protetta. Se si chiama direttamente l'endpoint LUIS, è necessario creare un'API lato server, ad esempio una [funzione](https://azure.microsoft.com/services/functions/) di Azure, con accesso controllato, ad esempio [AAD](https://azure.microsoft.com/services/active-directory/). Quando l'API lato server viene chiamata e autenticata e viene verificata l'autorizzazione, passare la chiamata a LUIS. Anche se questa strategia non impedisce gli attacchi man-in-the-Middle, offusca la chiave e l'URL dell'endpoint dagli utenti, consente di tenere traccia dell'accesso e consente di aggiungere la registrazione delle risposte degli endpoint, ad esempio [Application Insights](https://azure.microsoft.com/services/application-insights/).

### <a name="runtime-security-for-private-apps"></a>Sicurezza del runtime per le app private

Il runtime di un'app privata è disponibile solo per gli elementi seguenti:

|Chiave e utente|Spiegazione|
|--|--|
|Chiave di creazione del proprietario| Fino a 1000 accessi endpoint|
|Chiavi di collaborazione collaboratore/collaboratore| Fino a 1000 accessi endpoint|
|Qualsiasi chiave assegnata a LUIS da un autore o collaboratore/collaboratore|Basato sul livello di uso della chiave|

### <a name="runtime-security-for-public-apps"></a>Sicurezza del runtime per le app pubbliche

Dopo che un'app è stata configurata come pubblica, _qualsiasi_ chiave di creazione LUIS o chiave endpoint LUIS valida può eseguire una query nell'app, purché la chiave non abbia usato l'intera quota endpoint.

Un utente che non è un proprietario o un collaboratore può accedere al runtime di un'app pubblica solo se è stato specificato l'ID app. LUIS non ha un _mercato_ pubblico o un altro modo per cercare un'app pubblica.

Un'app pubblica viene pubblicata in tutte le regioni in modo che un utente con una chiave di risorsa LUIS basata su regione possa accedere all'app in qualsiasi regione associata la chiave di risorsa.


### <a name="securing-the-query-prediction-endpoint"></a>Protezione dell'endpoint di stima della query

È possibile controllare chi può visualizzare la chiave dell'endpoint di runtime di previsione LUIS chiamandola in un ambiente da server a server. Se si utilizza LUIS da un bot, la connessione tra il bot e LUIS è già protetta. Se si chiama direttamente l'endpoint LUIS, è necessario creare un'API lato server, ad esempio una [funzione](https://azure.microsoft.com/services/functions/) di Azure, con accesso controllato, ad esempio [AAD](https://azure.microsoft.com/services/active-directory/). Quando viene chiamata l'API lato server e vengono verificate l'autenticazione e l'autorizzazione, passare la chiamata a LUIS. Anche se questa strategia non impedisce gli attacchi man-in-the-Middle, offusca l'endpoint dagli utenti, consente di tenere traccia dell'accesso e consente di aggiungere la registrazione delle risposte degli endpoint, ad esempio [Application Insights](https://azure.microsoft.com/services/application-insights/).

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Accedere al portale di LUIS e iniziare la creazione

1. Accedere a [Luis Portal](https://www.luis.ai) e accettare le condizioni per l'utilizzo.
1. Avviare l'app LUIS scegliendo la chiave di creazione di Azure LUIS.

   ![Scegliere un tipo di risorsa di creazione Language Understanding](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Al termine del processo di selezione delle risorse, [creare una nuova app](luis-how-to-start-new-app.md#create-new-app-in-luis).


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-azure-cli"></a>Creare risorse nell'interfaccia della riga di comando di Azure

Usare l' [interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) della riga di comando di Azure per creare ogni risorsa singolarmente.

Risorsa `kind` :

* Authoring `LUIS.Authoring`
* Stima `LUIS`

1. Accedere all'interfaccia della riga di comando di Azure:

    ```azurecli
    az login
    ```

    Verrà visualizzato un browser che consente di selezionare l'account corretto e di fornire l'autenticazione.

1. Creare una **risorsa Luis authoring**, di tipo `LUIS.Authoring` , denominata `my-luis-authoring-resource` nel gruppo di risorse _esistente_ denominato `my-resource-group` per l' `westus` area.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Creare una **risorsa dell'endpoint di stima Luis**, di tipo `LUIS` , denominata `my-luis-prediction-resource` nel gruppo di risorse _esistente_ denominato `my-resource-group` per l' `westus` area. Se si desidera una velocità effettiva superiore a quella del livello gratuito, modificare `F0` in `S0` . Altre informazioni sui [piani tariffari e la velocità effettiva](luis-limits.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Queste chiavi **non** vengono usate dal portale Luis fino a quando non vengono assegnate nel portale Luis sulle **risorse di Azure manage->**.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resource-in-the-luis-portal"></a>Assegnare una risorsa nel portale LUIS

È possibile assegnare una risorsa di creazione per una singola app o per tutte le app in LUIS. La procedura seguente consente di assegnare tutte le app a una singola risorsa di creazione.

1. Accedere al [portale LUIS](https://www.luis.ai).
1. Nella barra di spostamento superiore, all'estrema destra, selezionare l'account utente e quindi selezionare **Impostazioni**.
1. Nella pagina **impostazioni utente** selezionare **Aggiungi risorsa di creazione** e quindi selezionare una risorsa di creazione esistente. Selezionare **Salva**.

## <a name="assign-a-resource-to-an-app"></a>Assegnare una risorsa a un'app

Si noti che se non si ha una sottoscrizione di Azure, non sarà possibile assegnare o creare una nuova risorsa. Sarà prima di tutto necessario creare una versione di [valutazione gratuita di Azure](https://azure.microsoft.com/en-us/free/) , quindi tornare a Luis per creare una nuova risorsa dal portale.

È possibile assegnare o creare una risorsa di creazione o di stima a un'applicazione con la seguente procedura:

1. Accedere al [portale Luis](https://www.luis.ai), quindi selezionare un'app dall'elenco **app personali**
1. Passare alla pagina **Gestisci-> risorse di Azure**

    ![Selezionare le risorse di Azure Gestisci > nel portale LUIS per assegnare una risorsa all'app.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Selezionare la scheda stima o Crea risorsa e quindi selezionare il pulsante **Aggiungi risorsa di stima** o **Aggiungi risorsa di creazione**
1. Selezionare i campi nel modulo per trovare la risorsa corretta, quindi selezionare **Salva** .
1. Se non si ha una risorsa esistente, è possibile crearne una selezionando "crea una nuova risorsa LUIS?" dalla parte inferiore della finestra


### <a name="assign-query-prediction-runtime-resource-without-using-luis-portal"></a>Assegnare una risorsa di runtime di stima query senza usare il portale LUIS

Per scopi di automazione, ad esempio una pipeline di integrazione continua/recapito continuo, è possibile automatizzare l'assegnazione di una risorsa di runtime LUIS a un'app LUIS. Per farlo, è necessario seguire questa procedura:

1. Ottenere un token di Azure Resource Manager per questo [sito Web](https://resources.azure.com/api/token?plaintext=true). Il token scade, perciò usarlo immediatamente. La richiesta restituisce un token di Azure Resource Manager.

    ![Richiedere il token di Azure Resource Manager e ricevere il token di Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Usare il token per richiedere le risorse di runtime LUIS tra le sottoscrizioni, dall' [API per ottenere gli account Luis Azure](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)a cui l'account utente ha accesso.

    Questa API POST richiede le impostazioni seguenti:

    |Intestazione|valore|
    |--|--|
    |`Authorization`|Il valore della proprietà `Authorization` è `Bearer {token}`. Si noti che il valore del token deve essere preceduto dalla parola `Bearer` e uno spazio.|
    |`Ocp-Apim-Subscription-Key`|La chiave di creazione.|

    Questa API restituisce una matrice di oggetti JSON delle sottoscrizioni LUIS inclusi ID sottoscrizione, gruppo di risorse e nome della risorsa restituito come nome dell'account. Trovare l'elemento della matrice che rappresenta la risorsa LUIS da assegnare all'app LUIS.

1. Assegnare il token alla risorsa LUIS con l'API [Assign an Luis Azure accounts to an Application](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) .

    Questa API POST richiede le impostazioni seguenti:

    |Type|Impostazione|Valore|
    |--|--|--|
    |Intestazione|`Authorization`|Il valore della proprietà `Authorization` è `Bearer {token}`. Si noti che il valore del token deve essere preceduto dalla parola `Bearer` e uno spazio.|
    |Intestazione|`Ocp-Apim-Subscription-Key`|La chiave di creazione.|
    |Intestazione|`Content-type`|`application/json`|
    |QueryString|`appid`|L'ID dell'app LUIS.
    |Corpo||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Quando l'API ha esito positivo, restituisce 201 - stato creato.

## <a name="unassign-resource"></a>Annullare l'assegnazione di una risorsa

1. Accedere al [portale Luis](https://www.luis.ai), quindi selezionare un'app dall'elenco **app personali** .
1. Passare alla pagina **Gestisci-> risorse di Azure** .
1. Selezionare la scheda stima o Crea risorsa e quindi selezionare il pulsante Annulla **assegnazione risorsa** per la risorsa.

Quando si annulla l'assegnazione di una risorsa, questa non viene eliminata da Azure. Viene soltanto scollegata da LUIS.


## <a name="delete-account"></a>Elimina l'account

Per informazioni sui dati che vengono eliminati quando viene eliminato l'account, vedere [Data storage and removal](luis-concept-data-storage.md#accounts) (Archiviazione e rimozione dei dati).

## <a name="change-pricing-tier"></a>Modificare il piano tariffario

1.  In [Azure](https://portal.azure.com), trova la sottoscrizione LUIS. Selezionare la sottoscrizione LUIS.
    ![Trova la sottoscrizione LUIS](./media/luis-usage-tiers/find.png)
1.  Selezionare **Piano tariffario** per visualizzare i piani tariffari disponibili.
    ![Visualizza piano tariffario](./media/luis-usage-tiers/subscription.png)
1.  Selezionare il piano tariffario e scegliere **Seleziona** per salvare la modifica.
    ![Modifica il piano tariffario LUIS](./media/luis-usage-tiers/plans.png)
1.  Una volta completata la modifica dei prezzi, una finestra popup verifica il nuovo piano tariffario.
    ![Verifica il piano tariffario LUIS](./media/luis-usage-tiers/updated.png)
1. Ricorda di [assegnare questa chiave di endpoint](#assign-a-resource-to-an-app) nella pagina **Pubblica** e di usarla in tutte le query di endpoint.

## <a name="viewing-azure-resource-metrics"></a>Visualizzazione delle metriche delle risorse di Azure

### <a name="viewing-azure-resource-summary-usage"></a>Visualizzazione dell'utilizzo di riepilogo delle risorse di Azure
È possibile visualizzare le informazioni sull'utilizzo di LUIS in Azure. La pagina **Panoramica** contiene informazioni di riepilogo recenti, incluse chiamate ed errori. Se effettui una richiesta LUIS di endpoint, quindi osservi la **pagina Panoramica**, attendi fino a cinque minuti perché compaia l'utilizzo.

![Visualizzazione del riepilogo dell'utilizzo](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Personalizzazione dei grafici di utilizzo delle risorse di Azure
Metrica offre una visualizzazione più dettagliata dei dati.

![Metriche predefinite](./media/luis-usage-tiers/metrics-default.png)

È possibile configurare i grafici delle metriche per periodo di tempo e il tipo di metrica.

![Metriche personalizzate](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Avviso di superamento della soglia delle transazioni totali
Se desideri sapere quando è stata raggiunta una determinata soglia delle transazioni, ad esempio 10.000 transazioni, puoi creare un avviso.

![Avvisi predefiniti](./media/luis-usage-tiers/alert-default.png)

Aggiungi un avviso di metrica per la metrica **Totale chiamate** riferito a un determinato periodo di tempo. Aggiungi gli indirizzi e-mail di tutti gli utenti che devono ricevere l'avviso. Aggiungi webhook per tutti i sistemi che devono ricevere l'avviso. È anche possibile eseguire un'app per la logica quando viene generato l'avviso.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su come usare le versioni](luis-how-to-manage-versions.md) per controllare il ciclo di vita dell'app.
* Eseguire la migrazione alla nuova [risorsa di creazione](luis-migration-authoring.md)
