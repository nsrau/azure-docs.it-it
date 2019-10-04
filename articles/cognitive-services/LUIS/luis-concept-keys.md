---
title: Creazione e modifica di chiavi di runtime-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS utilizza due chiavi, la chiave di creazione per creare il modello e la chiave di runtime per eseguire query sull'endpoint di stima con espressioni utente.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 70e58077fa40ce685324cd24b447886ec3411034
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703194"
---
# <a name="authoring-and-runtime-keys"></a>Chiavi di creazione e runtime


>[!NOTE]
>Prima di continuare, [eseguire la migrazione](luis-migration-authoring.md) di tutte le app che non usano la risorsa di creazione di Azure.

LUIS USA due tipi di risorse di Azure, ognuno dei quali include chiavi: 
 
* Creazione e [modifica](#programmatic-key) per creare Intent, entità ed espressioni di etichetta, eseguire il training e pubblicare. Quando si è pronti per pubblicare l'app LUIS, è necessaria una [chiave dell'endpoint di stima per il runtime](luis-how-to-azure-subscription.md) assegnato all'app.
* [Chiave dell'endpoint di stima per il runtime](#prediction-endpoint-runtime-key). Le applicazioni client, ad esempio un bot di chat, devono accedere all'endpoint di **stima della query** del runtime tramite questa chiave. 

|Chiave|Scopo|Servizio cognitivo `kind`|Servizio cognitivo `type`|
|--|--|--|--|
|[Chiave di creazione](#programmatic-key)|Creazione, formazione, pubblicazione e test.|`LUIS.Authoring`|`Cognitive Services`|
|[Chiave di runtime dell'endpoint di stima](#prediction-endpoint-runtime-key)| Query PREDICTION endpoint Runtime con un enunciato utente per determinare gli Intent ed entità.|`LUIS`|`Cognitive Services`|

LUIS fornisce inoltre una [chiave di avvio](luis-how-to-azure-subscription.md#starter-key) con una quota dell'endpoint di stima di 1000 transazioni al mese. 

Sebbene non sia necessario creare entrambe le chiavi nello stesso momento, è molto più semplice.

È importante creare app LUIS in [aree](luis-reference-regions.md#publishing-regions) in cui si desidera pubblicare ed eseguire query.

<a name="programmatic-key" ></a>

## <a name="authoring-key"></a>Chiave di creazione

Una chiave di creazione viene creata automaticamente quando si crea un account LUIS ed è gratuita. Quando si inizia con LUIS, è presente una chiave di avvio in tutte le app LUIS per ogni [area](luis-reference-regions.md)di creazione. Lo scopo della chiave di creazione è fornire l'autenticazione per gestire l'app LUIS o per testare le query degli endpoint di stima. 

La creazione di chiavi di creazione nel portale di Azure consente di controllare le autorizzazioni per la risorsa di creazione assegnando gli utenti al [ruolo Collaboratore](#contributions-from-other-authors). Per aggiungere collaboratori, è necessaria l'autorizzazione a livello di sottoscrizione di Azure. 

Per trovare la chiave di creazione, accedere a [LUIS](luis-reference-regions.md#luis-website) e fare clic sul nome dell'account nella barra di spostamento superiore destra per aprire **Account Settings** (Impostazioni account).

![chiave di creazione](./media/luis-concept-keys/authoring-key.png)

Quando si vogliono eseguire **query di runtime**, creare la [risorsa Luis](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)di Azure. 

> [!CAUTION]
> Per praticità, molti degli esempi utilizzano la [chiave di avvio](#starter-prediction-endpoint-runtime-key) perché fornisce alcune chiamate a endpoint di stima gratuite nella [quota](luis-boundaries.md#key-limits).  

<a name="endpoint-key"></a>

## <a name="prediction-endpoint-runtime-key"></a>Chiave di runtime dell'endpoint di stima 

Quando sono necessarie **query dell'endpoint di runtime**, creare una risorsa Language Understanding (Luis), quindi assegnarla all'app Luis. 

[!INCLUDE [Azure runtime resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

Al termine del processo di creazione delle risorse, [assegnare la chiave](luis-how-to-azure-subscription.md) all'app. 

* La chiave Runtime (query PREDICTION endpoint) consente una quota di riscontri dell'endpoint in base al piano di utilizzo specificato durante la creazione della chiave di Runtime. Per informazioni sui prezzi, vedere [Prezzi dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h).

* La chiave di runtime può essere usata per tutte le app LUIS o per app LUIS specifiche. 
* Non usare la chiave di runtime per la creazione di app LUIS. 

### <a name="starter-prediction-endpoint-runtime-key"></a>Chiave runtime dell'endpoint di stima Starter

La chiave dell'endpoint di stima **iniziale** è disponibile gratuitamente e include le query dell'endpoint di stima 1000. Una volta utilizzate queste query, è necessario creare una risorsa dell'endpoint di stima per Language Understanding.  

Si tratta di una risorsa speciale creata automaticamente. Non viene visualizzato nell'elenco delle risorse di Azure perché è concepito come chiave iniziale temporanea. 

<a name="use-endpoint-key-in-query"></a>

### <a name="use-runtime-key-in-query"></a>Usare la chiave di runtime nella query
L'endpoint di runtime LUIS accetta due stili di query, entrambi utilizzano la chiave di runtime dell'endpoint di stima, ma in posizioni diverse.

L'endpoint usato per accedere al runtime usa un sottodominio univoco per l'area della risorsa, indicato con `{region}` nella tabella seguente. 


#### <a name="v2-prediction-endpointtabv2"></a>[V2 endpoint di stima](#tab/V2)

|Verbo|URL di esempio e posizione della chiave|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?runtime-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`|

#### <a name="v3-prediction-endpointtabv3"></a>[Endpoint di stima V3](#tab/V3)

|Verbo|URL di esempio e posizione della chiave|
|--|--|
|[GET](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a91e54c9db63d589f433)|`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?runtime-key=your-endpoint-key-here&query=turn%20on%20the%20lights`|
|[POST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a5830f741b27cd03a061)| `https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict`| 

Altre informazioni sull' [endpoint di stima V3](luis-migration-api-v3.md).

* * * 

**OTTENERE**: Modificare il valore della query endpoint per `runtime-key` dalla chiave di creazione (avvio) alla nuova chiave endpoint per usare il limite di quota della chiave endpoint LUIS. Se si crea e si assegna la chiave, ma non si modifica il valore della query endpoint per `runtime-key`, non si usa la quota della chiave endpoint.

**POST**: Modificare il valore dell'intestazione per `Ocp-Apim-Subscription-Key`<br>Se si crea la chiave di runtime e si assegna la chiave di runtime, ma non si modifica il valore della query dell'endpoint per `Ocp-Apim-Subscription-Key`, non si usa la chiave di Runtime.

L'ID app usato nell'URL precedente, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, è l'app IoT pubblica usata per la [dimostrazione interattiva](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="assignment-of-the-runtime-key"></a>Assegnazione della chiave di runtime

È possibile [assegnare](luis-how-to-azure-subscription.md) la chiave di runtime nel [portale Luis](https://www.luis.ai) o tramite le API corrispondenti. 

## <a name="key-limits"></a>Limiti delle chiavi

È possibile creare fino a 10 chiavi di creazione per ogni area per sottoscrizione. 

Vedere [limiti chiave](luis-boundaries.md#key-limits) e [aree di Azure](luis-reference-regions.md). 

Le regioni di pubblicazione sono diverse dalle regioni di creazione. Assicurarsi di creare un'app nell'area di creazione corrispondente all'area di pubblicazione in cui si vuole che venga individuata l'applicazione client.

## <a name="key-limit-errors"></a>Errori relativi ai limiti delle chiavi
Se si supera la quota transazioni al secondo (TPS), viene visualizzato un errore HTTP 429. Se si supera la quota di transazioni per mese (TPS), viene visualizzato un errore HTTP 403. 

## <a name="contributions-from-other-authors"></a>Contributi di altri autori



La gestione dei contributi dai collaboratori dipende dallo stato corrente dell'app.

**Per la creazione di app [migrate delle risorse](luis-migration-authoring.md)** : i _collaboratori_ vengono gestiti nel portale di Azure per la risorsa di creazione, usando la pagina **controllo di accesso (IAM)** . Informazioni [su come aggiungere un utente](luis-how-to-collaborate.md), usando l'indirizzo di posta elettronica del collaboratore e il ruolo _collaboratore_ . 

**Per le app che non sono state ancora migrate**: tutti i _collaboratori_ vengono gestiti nel portale Luis dalla pagina **Manage-> collaboratori** .

### <a name="contributor-roles-vs-entity-roles"></a>Ruoli collaboratore e ruoli entità

I [ruoli dell'entità](luis-concept-roles.md) si applicano al modello di dati dell'app Luis. I ruoli collaboratore/collaboratore si applicano ai livelli di accesso alla creazione. 

## <a name="moving-or-changing-ownership"></a>Trasferimento o modifica della proprietà

Un'app è definita dalle risorse di Azure, che è determinata dalla sottoscrizione del proprietario. 

È possibile spostare l'app LUIS. Usare le risorse di documentazione seguenti nell'portale di Azure o nell'interfaccia della riga di comando di Azure:

* [Spostare l'app tra le risorse di authoring LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Spostare una risorsa in un nuovo gruppo di risorse o sottoscrizione](../../azure-resource-manager/resource-group-move-resources.md)
* [Spostare una risorsa all'interno della stessa sottoscrizione o tra sottoscrizioni](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)
* [Trasferire la proprietà](../../billing/billing-subscription-transfer.md) della sottoscrizione 

## <a name="access-for-private-and-public-apps"></a>Accesso per le app private e pubbliche

Per un'app **privata** , l'accesso in fase di esecuzione è disponibile per proprietari e collaboratori. Per un'app **pubblica** , l'accesso in fase di esecuzione è disponibile per tutti gli utenti che dispongono del proprio [servizio cognitivo](../cognitive-services-apis-create-account.md) di Azure o della risorsa di runtime [Luis](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) e l'ID dell'app pubblica. 

Attualmente non è disponibile un catalogo di app pubbliche.

### <a name="authoring-access"></a>Accesso alla creazione
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
|Treno|

### <a name="prediction-endpoint-runtime-access"></a>Accesso al runtime dell'endpoint di stima

L'accesso per eseguire query sull'endpoint di stima è controllato da un'impostazione nella pagina **informazioni sull'applicazione** nella sezione **Gestisci** . 

![Impostazione dell'app su pubblica](./media/luis-concept-security/set-application-as-public.png)

|[Endpoint privato](#runtime-security-for-private-apps)|[Endpoint pubblico](#runtime-security-for-public-apps)|
|:--|:--|
|Disponibile per proprietari e collaboratori|Disponibile per proprietario, collaboratori e altri utenti che conoscono l'ID app|

È possibile controllare chi vede la chiave del runtime di LUIS chiamandola in un ambiente da server a server. Se si utilizza LUIS da un bot, la connessione tra il bot e LUIS è già protetta. Se si chiama direttamente l'endpoint LUIS, è necessario creare un'API lato server, ad esempio una [funzione](https://azure.microsoft.com/services/functions/) di Azure, con accesso controllato, ad esempio [AAD](https://azure.microsoft.com/services/active-directory/). Quando l'API lato server viene chiamata e autenticata e viene verificata l'autorizzazione, passare la chiamata a LUIS. Anche se questa strategia non impedisce gli attacchi man-in-the-Middle, offusca la chiave e l'URL dell'endpoint dagli utenti, consente di tenere traccia dell'accesso e consente di aggiungere la registrazione delle risposte degli endpoint, ad esempio [Application Insights](https://azure.microsoft.com/services/application-insights/).

#### <a name="runtime-security-for-private-apps"></a>Sicurezza del runtime per le app private

Il runtime di un'app privata è disponibile solo per gli elementi seguenti:

|Chiave e utente|Spiegazione|
|--|--|
|Chiave di creazione del proprietario| Fino a 1000 accessi endpoint|
|Chiavi di collaborazione collaboratore/collaboratore| Fino a 1000 accessi endpoint|
|Qualsiasi chiave assegnata a LUIS da un autore o collaboratore/collaboratore|Basato sul livello di uso della chiave|

#### <a name="runtime-security-for-public-apps"></a>Sicurezza del runtime per le app pubbliche

Dopo che un'app è stata configurata come pubblica, _qualsiasi_ chiave di creazione LUIS o chiave endpoint LUIS valida può eseguire una query nell'app, purché la chiave non abbia usato l'intera quota endpoint.

Un utente che non è un proprietario o un collaboratore può accedere al runtime di un'app pubblica solo se è stato specificato l'ID app. LUIS non ha un _mercato_ pubblico o un altro modo per cercare un'app pubblica.  

Un'app pubblica viene pubblicata in tutte le regioni in modo che un utente con una chiave di risorsa LUIS basata su regione possa accedere all'app in qualsiasi regione associata la chiave di risorsa.

## <a name="transfer-of-ownership"></a>Trasferire la proprietà

**Per la creazione di app [migrate di risorse](luis-migration-authoring.md)** : Il proprietario della risorsa può aggiungere un `contributor`.

**Per le app di cui non è ancora stata eseguita la migrazione**: Esportare l'app come file JSON. Un altro utente LUIS può importare l'app, diventando così il proprietario dell'app. La nuova app avrà un ID app diverso.  

## <a name="securing-the-endpoint"></a>Protezione dell'endpoint 

È possibile controllare chi può visualizzare la chiave dell'endpoint di runtime di previsione LUIS chiamandola in un ambiente da server a server. Se si utilizza LUIS da un bot, la connessione tra il bot e LUIS è già protetta. Se si chiama direttamente l'endpoint LUIS, è necessario creare un'API lato server, ad esempio una [funzione](https://azure.microsoft.com/services/functions/) di Azure, con accesso controllato, ad esempio [AAD](https://azure.microsoft.com/services/active-directory/). Quando viene chiamata l'API lato server e vengono verificate l'autenticazione e l'autorizzazione, passare la chiamata a LUIS. Se da un lato questa strategia non impedisce gli attacchi man-in-the-middle, dall'altro offusca l'endpoint dagli utenti, consente di seguire l'accesso e di aggiungere la registrazione delle risposte endpoint, ad esempio [Application Insights](https://azure.microsoft.com/services/application-insights/).  

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui concetti di [controllo delle versioni](luis-concept-version.md). 
* Informazioni [su come creare chiavi](luis-how-to-azure-subscription.md).
