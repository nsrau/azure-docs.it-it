---
title: Come usare le chiavi di creazione e di runtime con LUISHow to use authoring and runtime keys with LUIS
titleSuffix: Azure Cognitive Services
description: LUIS usa due chiavi, la chiave di creazione per creare il modello e la chiave di runtime per l'esecuzione di query sull'endpoint di stima con le espressioni utente.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9d213c8fa03ad2ca5e5fd7e620e52aa502749be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220963"
---
# <a name="authoring-and-runtime-keys"></a>Chiavi di creazione e runtime

La comprensione della lingua (LUIS) dispone di due servizi e set di API:Language Understanding (LUIS) has two services and API sets: 

* Creazione (precedentemente nota come _programmatic)_
* Runtime di stimaPrediction runtime

Esistono diversi tipi di chiave, a seconda del servizio che si desidera utilizzare e di come si desidera utilizzarlo.

## <a name="non-azure-resources-for-luis"></a>Risorse non di Azure per LUISNon Azure resources for LUIS

### <a name="starter-key"></a>Tasto di avviamento

Quando si inizia a usare LUIS per la prima volta, viene creata automaticamente una chiave di **avvio.** Questa risorsa fornisce:

* richieste gratuite di servizi di creazione tramite il portale LUIS o API (inclusi SDK)
* 1.000 richieste di endpoint di stima gratuite al mese tramite un browser, un'API o SDK

## <a name="azure-resources-for-luis"></a>Azure resources for LUIS

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS consente tre tipi di risorse di Azure:LUIS allows three types of Azure resources: 
 
|Chiave|Scopo|Servizio cognitivo`kind`|Servizio cognitivo`type`|
|--|--|--|--|
|[Chiave di creazione](#programmatic-key)|Accedi e gestisci i dati dell'applicazione con authoring, formazione, pubblicazione e test. Creare una chiave di creazione LUIS se si intende creare app LUIS a livello di codice.<br><br>Lo scopo `LUIS.Authoring` della chiave è quello di consentire di:<br>- gestire a livello di codice le applicazioni e i modelli di comprensione della lingua, tra cui la formazione e la pubblicazione<br> : controlla le autorizzazioni per la risorsa di creazione assegnando gli utenti al [ruolo collaboratore.](#contributions-from-other-authors)|`LUIS.Authoring`|`Cognitive Services`|
|[Chiave di stima](#prediction-endpoint-runtime-key)| Richieste di endpoint di previsione delle query. Creare una chiave di stima LUIS prima che l'app client richieda stime oltre le 1.000 richieste fornite dalla risorsa iniziale. |`LUIS`|`Cognitive Services`|
|[Chiave di risorsa multiservizio Servizio cognitivo](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Richieste di endpoint di stima delle query condivise con LUIS e altri servizi cognitivi supportati.|`CognitiveServices`|`Cognitive Services`|

Al termine del processo di creazione delle risorse, [assegnare la chiave](luis-how-to-azure-subscription.md) all'app nel portale LUIS.

È importante creare app LUIS nelle [aree](luis-reference-regions.md#publishing-regions) in cui si desidera pubblicare ed eseguire query.

> [!CAUTION]
> Per comodità, molti degli esempi usano la [chiave Starter](#starter-key) perché fornisce alcune chiamate gratuite all'endpoint di stima nella [quota.](luis-boundaries.md#key-limits)  


### <a name="query-prediction-resources"></a>Risorse di previsione delle query

* La chiave di runtime può essere usata per tutte le app LUIS o per app LUIS specifiche. 
* Non usare la chiave di runtime per la creazione di app LUIS. 

L'endpoint di runtime LUIS accetta due stili di query, entrambi utilizzano la chiave di runtime dell'endpoint di stima, ma in posizioni diverse.

L'endpoint utilizzato per accedere al runtime utilizza un sottodominio univoco per `{region}` l'area della risorsa, indicato con nella tabella seguente. 

## <a name="assignment-of-the-key"></a>Assegnazione del tasto

È possibile [assegnare](luis-how-to-azure-subscription.md) la chiave di runtime nel [portale LUIS](https://www.luis.ai) o tramite le API corrispondenti. 

## <a name="key-limits"></a>Limiti delle chiavi

È possibile creare fino a 10 chiavi di creazione per area per sottoscrizione. 

Vedere [Limiti chiave](luis-boundaries.md#key-limits) e [aree](luis-reference-regions.md)di Azure . 

Le regioni di pubblicazione sono diverse dalle regioni di creazione. Assicurarsi di creare un'app nell'area di creazione e modifica corrispondente all'area di pubblicazione in cui si desidera che si trovi l'applicazione client.

## <a name="key-limit-errors"></a>Errori relativi ai limiti delle chiavi
Se si supera la quota transazioni al secondo (TPS), viene visualizzato un errore HTTP 429. Se si supera la quota transazione al mese (TPS), viene visualizzato un errore HTTP 403. 

## <a name="contributions-from-other-authors"></a>Contributi di altri autori

**Per le app [migrate](luis-migration-authoring.md) **delle risorse di creazione: _i collaboratori_ vengono gestiti nel portale di Azure per la risorsa di creazione, usando la pagina Controllo di **accesso (IAM).** [Informazioni su come aggiungere un utente,](luis-how-to-collaborate.md)utilizzando l'indirizzo di posta elettronica del collaboratore e il ruolo di _collaboratore._ 

**Per le app di cui non è ancora stata eseguita la migrazione:** tutti i collaboratori vengono _gestiti_ nel portale LUIS dalla pagina **Gestisci-> Collaboratori.**

## <a name="move-transfer-or-change-ownership"></a>Spostare, trasferire o modificare la proprietà

Un'app è definita dalle relative risorse di Azure, determinate dalla sottoscrizione del proprietario. 

È possibile spostare l'app LUIS. Usare le risorse di documentazione seguenti nel portale di Azure o nell'interfaccia della riga di comando di Azure:Use the following documentation resources in the Azure portal or Azure CLI:

* [Spostare l'app tra le risorse di creazione di LUISMove app between LUIS authoring resources](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Spostare la risorsa in un nuovo gruppo di risorse o sottoscrizioneMove resource to new resource group or subscription](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Spostare una risorsa all'interno della stessa sottoscrizione o tra sottoscrizioniMove resource within same subscription or across subscriptions](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Per trasferire [la proprietà](../../cost-management-billing/manage/billing-subscription-transfer.md) dell'abbonamento: 

**Per gli utenti che hanno eseguito la migrazione - creazione di [risorse migrati](luis-migration-authoring.md) apps**: Come proprietario della risorsa, è possibile aggiungere un `contributor`.

**Per gli utenti che non hanno ancora eseguito la migrazione:** esportare l'app come file JSON. Un altro utente LUIS può importare l'app, diventando così il proprietario dell'app. La nuova app avrà un ID app diverso.  

## <a name="access-for-private-and-public-apps"></a>Accesso per app private e pubbliche

Per un'app **privata,** l'accesso in fase di esecuzione è disponibile per proprietari e collaboratori. Per un'app **pubblica,** l'accesso in runtime è disponibile per tutti gli utenti che hanno il proprio [servizio cognitivo](../cognitive-services-apis-create-account.md) di Azure o la risorsa di runtime [LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) e l'ID dell'app pubblica. 

Attualmente, non esiste un catalogo di app pubbliche.

### <a name="authoring-access"></a>Accesso alla creazione
L'accesso all'app dal portale [LUIS](luis-reference-regions.md#luis-website) o dalle API di creazione è controllato dalla risorsa di creazione di Azure.Access to the app from the LUIS portal or the [authoring APIs](https://go.microsoft.com/fwlink/?linkid=2092087) is controlled by the Azure authoring resource. 

Il proprietario e tutti i collaboratori hanno accesso alla creazione dell'app. 

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

### <a name="prediction-endpoint-runtime-access"></a>Accesso runtime dell'endpoint di stimaPrediction endpoint runtime access

L'accesso per eseguire query sull'endpoint di stima è controllato da un'impostazione nella pagina Informazioni applicazione nella sezione Gestisci.Access to query the prediction endpoint is controlled by a setting on the **Application Information** page in the **Manage** section. 

|[Endpoint privato](#runtime-security-for-private-apps)|[Endpoint pubblico](#runtime-security-for-public-apps)|
|:--|:--|
|Disponibile per il proprietario e i collaboratori|Disponibile per il proprietario, i collaboratori e chiunque altro conosca l'ID dell'app|

È possibile controllare chi vede la chiave di runtime LUIS chiamandola in un ambiente server-server. Se si utilizza LUIS da un bot, la connessione tra il bot e LUIS è già protetta. Se si chiama direttamente l'endpoint LUIS, è necessario creare un'API lato server, ad esempio una [funzione](https://azure.microsoft.com/services/functions/) di Azure, con accesso controllato, ad esempio [AAD](https://azure.microsoft.com/services/active-directory/). Quando l'API lato server viene chiamata e autenticata e l'autorizzazione viene verificata, passare la chiamata a LUIS. Sebbene questa strategia non impedisca gli attacchi man-in-the-middle, offusca l'URL della chiave e dell'endpoint dagli utenti, consente di tenere traccia dell'accesso e consente di aggiungere la registrazione delle risposte agli endpoint (ad esempio [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Sicurezza di runtime per le app private

Il runtime di un'app privata è disponibile solo per gli elementi seguenti:A private app's runtime is only available to the following:

|Chiave e utente|Spiegazione|
|--|--|
|Chiave di creazione del proprietario| Fino a 1000 accessi endpoint|
|Chiavi di creazione collaboratore/collaboratore| Fino a 1000 accessi endpoint|
|Qualsiasi tasto assegnato a LUIS da un autore o collaboratore/collaboratore|Basato sul livello di uso della chiave|

#### <a name="runtime-security-for-public-apps"></a>Sicurezza di runtime per le app pubbliche

Dopo che un'app è stata configurata come pubblica, _qualsiasi_ chiave di creazione LUIS o chiave endpoint LUIS valida può eseguire una query nell'app, purché la chiave non abbia usato l'intera quota endpoint.

Un utente che non è un proprietario o un collaboratore, può accedere al runtime di un'app pubblica solo se viene fornito l'ID dell'app. LUIS non ha un _mercato_ pubblico o un altro modo per cercare un'app pubblica.  

Un'app pubblica viene pubblicata in tutte le regioni in modo che un utente con una chiave di risorsa LUIS basata su regione possa accedere all'app in qualsiasi regione associata la chiave di risorsa.

## <a name="transfer-of-ownership"></a>Trasferire la proprietà

LUIS non ha il concetto di trasferire la proprietà di una risorsa. 

## <a name="securing-the-endpoint"></a>Protezione dell'endpoint 

È possibile controllare chi può visualizzare la chiave dell'endpoint di runtime di stima LUIS chiamandola in un ambiente server-server. Se si utilizza LUIS da un bot, la connessione tra il bot e LUIS è già protetta. Se si chiama direttamente l'endpoint LUIS, è necessario creare un'API lato server, ad esempio una [funzione](https://azure.microsoft.com/services/functions/) di Azure, con accesso controllato, ad esempio [AAD](https://azure.microsoft.com/services/active-directory/). Quando viene chiamata l'API lato server e vengono verificate l'autenticazione e l'autorizzazione, passare la chiamata a LUIS. Se da un lato questa strategia non impedisce gli attacchi man-in-the-middle, dall'altro offusca l'endpoint dagli utenti, consente di seguire l'accesso e di aggiungere la registrazione delle risposte endpoint, ad esempio [Application Insights](https://azure.microsoft.com/services/application-insights/).  

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui concetti di [controllo delle versioni](luis-concept-version.md). 
* Informazioni su [come creare chiavi](luis-how-to-azure-subscription.md).
