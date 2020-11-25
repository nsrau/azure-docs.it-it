---
title: Uso delle chiavi di creazione e di runtime-LUIS
description: Quando si usa LUIS per la prima volta, non è necessario creare una chiave di creazione. Quando si vuole pubblicare l'app e quindi usare l'endpoint di runtime, è necessario creare e assegnare la chiave di runtime all'app.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a71c1a0df1a72e3831fa54a041539f62b38a0aca
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95999910"
---
# <a name="create-luis-resources"></a>Creare risorse LUIS

Le risorse di runtime di stima e di creazione di query consentono di eseguire l'autenticazione all'app Language Understanding (LUIS) e all'endpoint di stima.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>Risorse LUIS

LUIS consente tre tipi di risorse di Azure e una risorsa non di Azure:

|Risorsa|Scopo|Servizio cognitivo `kind`|Servizio cognitivo `type`|
|--|--|--|--|
|Creazione di una risorsa|Consente di creare, gestire, eseguire il training, testare e pubblicare le applicazioni. [Creare una risorsa Luis authoring](luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) se si desidera creare app Luis a livello di codice o dal portale Luis. È necessario [eseguire la migrazione dell'account Luis](luis-migration-authoring.md#what-is-migration) prima di collegare le risorse di creazione di Azure all'applicazione. È possibile controllare le autorizzazioni per la risorsa di creazione assegnando [agli utenti il ruolo Collaboratore](#contributions-from-other-authors). <br><br> È disponibile un livello per la risorsa LUIS Authoring:<br> <ul> <li>**Risorsa di authoring F0 gratuita**, che offre 1 milione transazioni gratuite per la creazione e 1.000 test gratuiti per le richieste di endpoint di stima mensili. |`LUIS.Authoring`|`Cognitive Services`|
|Risorsa Previsione| Dopo la pubblicazione dell'applicazione LUIS, utilizzare la risorsa/chiave di stima per eseguire query sulle richieste dell'endpoint di stima. Creare una risorsa di stima LUIS prima che l'app client richieda le stime oltre le 1.000 richieste fornite dalla risorsa di creazione o di avvio. <br><br> Per la risorsa di stima sono disponibili due livelli:<br><ul> <li> **Risorsa di stima F0 gratuita**, che fornisce 10.000 richieste di endpoint di stima gratuite mensili.<br> <li> **Risorsa di stima S0 standard**, che corrisponde al livello a pagamento. [Altre informazioni sui prezzi.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Risorsa di avvio/versione di valutazione|Consente di creare, gestire, eseguire il training, testare e pubblicare le applicazioni. Questa risorsa viene creata per impostazione predefinita se si sceglie l'opzione risorsa di avvio quando si accede per la prima volta a LUIS. La chiave di avvio sarà deprecata. Tutti gli utenti di LUIS dovranno [eseguire la migrazione dei propri account](luis-migration-authoring.md#what-is-migration) e collegare le applicazioni Luis a una risorsa di creazione. Diversamente dalla risorsa di creazione, questa risorsa non fornisce le autorizzazioni per il controllo degli accessi in base al ruolo di Azure. <br><br> Analogamente a quanto avviene con la risorsa di creazione, la risorsa iniziale offre 1 milione transazioni di creazione gratuite e 1.000 test delle richieste di endpoint di stima gratuite.|-|Non è una risorsa di Azure.|
|[Chiave di risorsa multiservizio di servizi cognitivi](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Richieste di endpoint di stima delle query condivise con LUIS e altri servizi cognitivi supportati.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> LUIS fornisce due tipi di risorse F0 (livello gratuito), una per la creazione di transazioni e una per le transazioni di stima. Se si esauriscono le quote gratuite per le transazioni di stima, assicurarsi di usare la risorsa di stima F0, che fornisce un 10.000 di transazioni gratuite mensili e non la risorsa di creazione, che fornisce le transazioni di stima 1.000 mensili.

Al termine del processo di creazione delle risorse di Azure, [assegnare la risorsa](#assign-a-resource-to-an-app) all'app nel portale Luis.

> [!important]
> È necessario creare app LUIS nelle [aree](luis-reference-regions.md#publishing-regions) in cui si vuole pubblicare ed eseguire query.

## <a name="resource-ownership"></a>Proprietà delle risorse

Una risorsa di Azure, ad esempio una risorsa LUIS, appartiene alla sottoscrizione che contiene la risorsa.

Per modificare la proprietà di una risorsa, è possibile eseguire una di queste operazioni:
* Trasferire la [Proprietà](../../cost-management-billing/manage/billing-subscription-transfer.md) della sottoscrizione.
* Esportare l'app LUIS come file e quindi importare l'app in una sottoscrizione diversa. L'esportazione è disponibile nella pagina **app personali** del portale Luis.


## <a name="resource-limits"></a>Limiti delle risorse

### <a name="authoring-key-creation-limits"></a>Creazione di limiti per la creazione di chiavi

È possibile creare fino a 10 chiavi di creazione per ogni area, per sottoscrizione.

Per altre informazioni, vedere [limiti chiave](luis-limits.md#key-limits) e [aree di Azure](luis-reference-regions.md).

Le regioni di pubblicazione sono diverse dalle regioni di creazione. Assicurarsi di creare un'app nell'area di creazione che corrisponde all'area di pubblicazione in cui si desidera che venga individuata l'applicazione client.

### <a name="errors-for-key-usage-limits"></a>Errori per i limiti di utilizzo della chiave

I limiti di utilizzo sono basati sul piano tariffario.

Se si supera la quota di transazioni al secondo, viene visualizzato un errore HTTP 429. Se si supera la quota di transazioni per mese (TPM), viene visualizzato un errore HTTP 403.


### <a name="reset-an-authoring-key"></a>Reimpostare una chiave di creazione

Per le app per la [creazione di risorse di creazione migrate](luis-migration-authoring.md) : se la chiave di creazione è compromessa, reimpostare la chiave nella portale di Azure nella pagina **chiavi** della risorsa di creazione.

Per le app di cui non è stata eseguita la migrazione: la chiave viene reimpostata su tutte le app nel portale LUIS. Se le app vengono create mediante le API di creazione, è necessario modificare il valore di `Ocp-Apim-Subscription-Key` con la nuova chiave.

### <a name="regenerate-an-azure-key"></a>Rigenerare una chiave di Azure

È possibile rigenerare una chiave di Azure dalla pagina **chiavi** nel portale di Azure.


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Proprietà, accesso e sicurezza dell'app

Un'app è definita dalle risorse di Azure, determinate dalla sottoscrizione del proprietario.

È possibile spostare l'app LUIS. Usare le risorse seguenti per eseguire questa operazione usando il portale di Azure o l'interfaccia della riga di comando di Azure:

* [Spostare un'app tra le risorse di authoring LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Spostare una risorsa in un nuovo gruppo di risorse o una nuova sottoscrizione](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Spostare una risorsa all'interno della stessa sottoscrizione o tra sottoscrizioni](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Contributi di altri autori

Per le app per la [creazione di risorse di creazione migrate](luis-migration-authoring.md) : è possibile gestire i _collaboratori_ per una risorsa di creazione nel portale di Azure usando la pagina **controllo di accesso (IAM)** . Informazioni [su come aggiungere un utente](luis-how-to-collaborate.md) usando l'indirizzo di posta elettronica del collaboratore e il ruolo Collaboratore.

Per le app di cui non è ancora stata eseguita la migrazione: è possibile gestire tutti i _collaboratori_ nella pagina **manage-> collaboratori** del portale Luis.

### <a name="query-prediction-access-for-private-and-public-apps"></a>Accesso alla stima delle query per le app private e pubbliche

Per le app private, l'accesso al runtime di stima delle query è disponibile per i proprietari e i collaboratori. Per le app pubbliche, l'accesso in fase di esecuzione è disponibile per gli utenti che hanno il proprio [servizio cognitivo](../cognitive-services-apis-create-account.md) di Azure o la risorsa di runtime [Luis](#create-resources-in-the-azure-portal) e l'ID dell'app pubblica.

Attualmente non è disponibile un catalogo di app pubbliche.

### <a name="authoring-permissions-and-access"></a>Creazione di autorizzazioni e accesso
L'accesso a un'app dal portale [Luis](luis-reference-regions.md#luis-website) o dalle [API di creazione](https://go.microsoft.com/fwlink/?linkid=2092087) è controllato dalla risorsa di creazione di Azure.

Il proprietario e tutti i collaboratori possono accedere per creare l'app.

|La creazione dell'accesso include:|Note|
|--|--|
|Aggiungere o rimuovere chiavi endpoint||
|Esportare una versione||
|Esportare i log di endpoint||
|Importare la versione||
|Rendere pubblica un'app|Quando un'app è pubblica, chiunque disponga di una chiave di creazione o di endpoint può eseguire una query sull'app.|
|Modificare il modello|
|Pubblica|
|Rivedere le espressioni endpoint per l'[apprendimento attivo](luis-how-to-review-endpoint-utterances.md)|
|Eseguire il training|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Accesso al runtime dell'endpoint di stima

L'accesso per l'esecuzione di query sull'endpoint di stima è controllato da un'impostazione nella pagina **informazioni sull'applicazione** nella sezione **Gestisci** .

|[Endpoint privato](#runtime-security-for-private-apps)|[Endpoint pubblico](#runtime-security-for-public-apps)|
|:--|:--|
|Disponibile per proprietari e collaboratori|Disponibile per proprietario, collaboratori e altri utenti che conoscono l'ID app|

È possibile controllare chi vede la chiave del runtime di LUIS chiamandola in un ambiente da server a server. Se usi LUIS da un bot, la connessione tra il bot e LUIS è già più sicura. Se si chiama l'endpoint LUIS direttamente, è necessario creare un'API sul lato server, ad esempio una [funzione](https://azure.microsoft.com/services/functions/)di Azure, con accesso controllato (tramite un elemento come [Azure ad](https://azure.microsoft.com/services/active-directory/)). Quando l'API lato server viene chiamata e autenticata e viene verificata l'autorizzazione, passare la chiamata a LUIS. Questa strategia non impedisce attacchi man-in-the-Middle. Ma offusca la chiave e l'URL dell'endpoint dagli utenti, consente di tenere traccia dell'accesso e consente di aggiungere la registrazione delle risposte degli endpoint, ad esempio [Application Insights](https://azure.microsoft.com/services/application-insights/).

### <a name="runtime-security-for-private-apps"></a>Sicurezza del runtime per le app private

Il runtime di un'app privata è disponibile solo per le chiavi seguenti:

|Chiave e utente|Spiegazione|
|--|--|
|Chiave di creazione del proprietario| Fino a 1.000 riscontri endpoint|
|Chiavi di collaborazione collaboratore/collaboratore| Fino a 1.000 riscontri endpoint|
|Qualsiasi chiave assegnata a LUIS da un autore o collaboratore/collaboratore|Basato sul livello di uso della chiave|

### <a name="runtime-security-for-public-apps"></a>Sicurezza del runtime per le app pubbliche

Quando l'app è configurata come pubblica, _qualsiasi_ chiave di authoring Luis valida o chiave dell'endpoint Luis può eseguire query su di essa, purché la chiave non abbia usato l'intera quota dell'endpoint.

Un utente che non è un proprietario o un collaboratore può accedere al runtime di un'app pubblica solo se è stato specificato l'ID app. LUIS non dispone di un mercato pubblico o di un altro modo per consentire agli utenti di cercare un'app pubblica.

Un'app pubblica viene pubblicata in tutte le aree geografiche. Un utente con una chiave di risorsa LUIS basata sull'area può quindi accedere all'app in qualsiasi area associata alla chiave di risorsa.


### <a name="control-access-to-your-query-prediction-endpoint"></a>Controllare l'accesso all'endpoint di stima della query

È possibile controllare chi può visualizzare la chiave dell'endpoint di runtime di previsione LUIS chiamandola in un ambiente da server a server. Se usi LUIS da un bot, la connessione tra il bot e LUIS è già più sicura. Se si chiama l'endpoint LUIS direttamente, è necessario creare un'API sul lato server, ad esempio una [funzione](https://azure.microsoft.com/services/functions/)di Azure, con accesso controllato (tramite un elemento come [Azure ad](https://azure.microsoft.com/services/active-directory/)). Quando viene chiamata l'API lato server e vengono verificate l'autenticazione e l'autorizzazione, passare la chiamata a LUIS. Questa strategia non impedisce attacchi man-in-the-Middle. Tuttavia, l'endpoint viene offuscato dagli utenti, consentendo di tenere traccia dell'accesso e consentendo di aggiungere la registrazione delle risposte degli endpoint, ad esempio [Application Insights](https://azure.microsoft.com/services/application-insights/).

<a name="starter-key"></a>

## <a name="sign-in-to-the-luis-portal-and-begin-authoring"></a>Accedere al portale LUIS e iniziare la creazione

1. Accedere al [portale Luis](https://www.luis.ai) e accettare le condizioni per l'utilizzo.
1. Per iniziare a creare l'app LUIS, scegliere la chiave di authoring di Azure LUIS:

   ![Screenshot che mostra la schermata iniziale.](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Al termine del processo di selezione delle risorse, [creare una nuova app](luis-how-to-start-new-app.md#create-new-app-in-luis).


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-the-azure-cli"></a>Creare risorse nell'interfaccia della riga di comando di Azure

Usare l' [interfaccia](/cli/azure/install-azure-cli?view=azure-cli-latest) della riga di comando di Azure per creare ogni risorsa singolarmente.

Risorsa `kind` :

* Authoring `LUIS.Authoring`
* Stima `LUIS`

1. Accedere all'interfaccia della riga di comando di Azure:

    ```azurecli
    az login
    ```

    Questo comando apre un browser in modo da poter selezionare l'account corretto e fornire l'autenticazione.

1. Creare una risorsa LUIS authoring di tipo `LUIS.Authoring` , denominata `my-luis-authoring-resource` . Crearlo nel gruppo di risorse _esistente_ denominato `my-resource-group` per l' `westus` area.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Creare una risorsa dell'endpoint di stima LUIS di tipo `LUIS` , denominata `my-luis-prediction-resource` . Crearlo nel gruppo di risorse _esistente_ denominato `my-resource-group` per l' `westus` area. Se si desidera una velocità effettiva superiore a quella fornita dal livello gratuito, modificare `F0` in `S0` . [Altre informazioni sui piani tariffari e la velocità effettiva.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Queste chiavi non vengono usate dal portale Luis fino a quando non vengono assegnate nella pagina **Gestisci**  >  **risorse di Azure** nel portale Luis.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resources-in-the-luis-portal"></a>Assegnare le risorse nel portale LUIS

È possibile assegnare una risorsa di creazione per una singola app o per tutte le app in LUIS. La procedura seguente consente di assegnare tutte le app a una singola risorsa di creazione.

1. Accedere al [portale LUIS](https://www.luis.ai).
1. Nell'angolo in alto a destra selezionare l'account utente e quindi selezionare **Impostazioni**.
1. Nella pagina **impostazioni utente** selezionare **Aggiungi risorsa di creazione**, quindi selezionare una risorsa di creazione esistente. Selezionare **Salva**.

## <a name="assign-a-resource-to-an-app"></a>Assegnare una risorsa a un'app

>[!NOTE]
>Se non si ha una sottoscrizione di Azure, non sarà possibile assegnare o creare una nuova risorsa. È necessario creare un [account Azure gratuito](https://azure.microsoft.com/en-us/free/) e quindi tornare a Luis per creare una nuova risorsa dal portale.

È possibile utilizzare questa procedura per creare una risorsa di creazione o di stima o assegnarne una a un'applicazione: 

1. Accedere al [portale LUIS](https://www.luis.ai). Selezionare un'app nell'elenco **My apps** (App personali).
1. Passare a **gestire**  >  **le risorse di Azure**:

    ![Screenshot che mostra la pagina delle risorse di Azure.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Nella scheda risorsa di **stima** o **creazione e modifica** della risorsa selezionare il pulsante **Aggiungi risorsa di stima** o **Aggiungi risorsa di creazione** .
1. Usare i campi nel modulo per trovare la risorsa corretta, quindi selezionare **Salva**.
1. Se non si dispone di una risorsa esistente, è possibile crearne una selezionando **Crea una nuova risorsa Luis?** nella parte inferiore della finestra.


### <a name="assign-a-query-prediction-runtime-resource-without-using-the-luis-portal"></a>Assegnare una risorsa di runtime per la stima delle query senza usare il portale LUIS

Per i processi automatizzati come le pipeline CI/CD, è possibile automatizzare l'assegnazione di una risorsa di runtime di LUIS a un'app LUIS. A tale scopo, completare i passaggi seguenti:

1. Ottenere un token di Azure Resource Manager da [questo sito Web](https://resources.azure.com/api/token?plaintext=true). Questo token scade, quindi utilizzarlo immediatamente. La richiesta restituisce un token di Azure Resource Manager.

    ![Screenshot che mostra il sito Web per la richiesta di un token di Azure Resource Manager.](./media/luis-manage-keys/get-arm-token.png)

1. Usare il token per richiedere le risorse di runtime LUIS tra le sottoscrizioni. Usare l' [API per ottenere gli account Luis Azure](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)a cui l'account utente può accedere.

    Per questa API POST sono necessari i valori seguenti:

    |Intestazione|valore|
    |--|--|
    |`Authorization`|Il valore della proprietà `Authorization` è `Bearer {token}`. Il valore del token deve essere preceduto dalla parola `Bearer` e da uno spazio.|
    |`Ocp-Apim-Subscription-Key`|La chiave di creazione.|

    L'API restituisce una matrice di oggetti JSON che rappresentano le sottoscrizioni LUIS. I valori restituiti includono l'ID sottoscrizione, il gruppo di risorse e il nome della risorsa restituiti come `AccountName` . Trovare l'elemento nella matrice che è la risorsa LUIS che si vuole assegnare all'app LUIS.

1. Assegnare il token alla risorsa LUIS usando l'API [Assign an Luis Azure accounts to an Application](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) .

    Per questa API POST sono necessari i valori seguenti:

    |Tipo|Impostazione|Valore|
    |--|--|--|
    |Intestazione|`Authorization`|Il valore della proprietà `Authorization` è `Bearer {token}`. Il valore del token deve essere preceduto dalla parola `Bearer` e da uno spazio.|
    |Intestazione|`Ocp-Apim-Subscription-Key`|La chiave di creazione.|
    |Intestazione|`Content-type`|`application/json`|
    |QueryString|`appid`|L'ID dell'app LUIS.
    |Corpo||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Quando l'API ha esito positivo, restituisce `201 - created status` .

## <a name="unassign-a-resource"></a>Annulla l'assegnazione di una risorsa

1. Accedere al [portale Luis](https://www.luis.ai), quindi selezionare un'app dall'elenco **app personali** .
1. Passare a **Gestisci**  >  **risorse di Azure**.
1. Nella scheda risorsa di **stima** o **creazione e modifica** della risorsa selezionare il pulsante Annulla **assegnazione risorsa** per la risorsa.

Quando si annulla l'assegnazione di una risorsa, questa non viene eliminata da Azure. Viene scollegato solo da LUIS.


## <a name="delete-an-account"></a>Eliminare un account

Per informazioni sui dati che vengono eliminati quando viene eliminato l'account, vedere [Data storage and removal](luis-concept-data-storage.md#accounts) (Archiviazione e rimozione dei dati).

## <a name="change-the-pricing-tier"></a>Modificare il piano tariffario

1.  Nella [portale di Azure](https://portal.azure.com)trovare e selezionare la sottoscrizione Luis:

    ![Screenshot che mostra una sottoscrizione LUIS nell'portale di Azure.](./media/luis-usage-tiers/find.png)
1.  Selezionare il piano **tariffario** per visualizzare i piani tariffari disponibili:

    ![Screenshot che mostra la voce di menu piano tariffario.](./media/luis-usage-tiers/subscription.png)
1.  Selezionare il piano tariffario e quindi fare clic su **Seleziona** per salvare la modifica:

    ![Screenshot che illustra come selezionare e salvare un piano tariffario.](./media/luis-usage-tiers/plans.png)

    Al termine della modifica dei prezzi, una finestra popup verifica l'aggiornamento del piano tariffario:

    ![Screenshot della finestra popup che verifica l'aggiornamento dei prezzi.](./media/luis-usage-tiers/updated.png)
1. Ricorda di [assegnare questa chiave di endpoint](#assign-a-resource-to-an-app) nella pagina **Pubblica** e di usarla in tutte le query di endpoint.

## <a name="view-azure-resource-metrics"></a>Visualizzare le metriche delle risorse di Azure

### <a name="view-a-summary-of-azure-resource-usage"></a>Visualizzare un riepilogo dell'utilizzo delle risorse di Azure
È possibile visualizzare le informazioni sull'utilizzo di LUIS nell'portale di Azure. Nella pagina **Panoramica** viene visualizzato un riepilogo, incluse le chiamate recenti e gli errori. Se si esegue una richiesta di endpoint LUIS, attendere fino a cinque minuti per visualizzare la modifica.

![Screenshot che mostra la pagina panoramica.](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Personalizzazione dei grafici di utilizzo delle risorse di Azure
La pagina **metrica** fornisce una visualizzazione più dettagliata dei dati:

![Screenshot che mostra la pagina metrica.](./media/luis-usage-tiers/metrics-default.png)

È possibile configurare i grafici delle metriche per un periodo di tempo e un tipo di metrica specifici:

![Screenshot che mostra un grafico personalizzato.](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Avviso di superamento della soglia delle transazioni totali
Se si desidera stabilire quando si raggiunge una determinata soglia di transazione, ad esempio 10.000 transazioni, è possibile creare un avviso:

![Screenshot che mostra la pagina delle regole di avviso.](./media/luis-usage-tiers/alert-default.png)

Aggiungi un avviso di metrica per la metrica **Totale chiamate** riferito a un determinato periodo di tempo. Aggiungere gli indirizzi di posta elettronica di tutte le persone che devono ricevere l'avviso. Aggiungere webhook per tutti i sistemi che dovrebbero ricevere l'avviso. È anche possibile eseguire un'app per la logica quando viene generato l'avviso.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su come usare le versioni](luis-how-to-manage-versions.md) per controllare il ciclo di vita dell'app.
* Eseguire la migrazione alla nuova [risorsa di creazione](luis-migration-authoring.md).
