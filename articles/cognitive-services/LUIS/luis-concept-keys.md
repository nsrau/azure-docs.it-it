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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 973a8dd56437506d907159f212164ff147ba975c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487497"
---
# <a name="authoring-and-runtime-keys"></a>Chiavi di creazione e runtime

Language Understanding (LUIS) dispone di due servizi e set di API: 

* Creazione (precedentemente nota come _programmatico_)
* Runtime di stima

Sono disponibili diversi tipi di chiave, a seconda del servizio che si desidera utilizzare e del modo in cui si desidera utilizzarlo.

## <a name="non-azure-resources-for-luis"></a>Risorse non di Azure per LUIS

### <a name="starter-key"></a>Chiave di avvio

Quando si avvia per la prima volta l'uso di LUIS, viene creata automaticamente una **chiave** di avvio. Questa risorsa fornisce:

* richieste di servizio di creazione gratuite tramite il portale LUIS o le API (inclusi gli SDK)
* 1\.000 richieste endpoint di stima gratuite al mese tramite un browser, un'API o un SDK

## <a name="azure-resources-for-luis"></a>Risorse di Azure per LUIS

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS consente tre tipi di risorse di Azure: 
 
|Chiave|Scopo|`kind` di servizi cognitivi|`type` di servizi cognitivi|
|--|--|--|--|
|[Chiave di creazione](#programmatic-key)|Accesso e gestione dei dati dell'applicazione con la creazione, il training, la pubblicazione e il test. Creare una chiave LUIS authoring se si prevede di creare a livello di codice le app LUIS.<br><br>Lo scopo della chiave `LUIS.Authoring` è consentire di:<br>* gestione a livello di codice di app e modelli di Language Understanding, tra cui formazione e pubblicazione<br> * controllare le autorizzazioni per la risorsa di creazione assegnando gli utenti al [ruolo Collaboratore](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|[Chiave di stima](#prediction-endpoint-runtime-key)| Richieste endpoint di stima query. Creare una chiave di stima LUIS prima che l'app client richieda stime oltre le richieste 1.000 fornite dalla risorsa iniziale. |`LUIS`|`Cognitive Services`|
|[Chiave di risorsa multiservizio di servizi cognitivi](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Richieste di endpoint di stima delle query condivise con LUIS e altri servizi cognitivi supportati.|`CognitiveServices`|`Cognitive Services`|

Al termine del processo di creazione delle risorse, [assegnare la chiave](luis-how-to-azure-subscription.md) all'app nel portale Luis.

È importante creare app LUIS in [aree](luis-reference-regions.md#publishing-regions) in cui si desidera pubblicare ed eseguire query.

> [!CAUTION]
> Per praticità, molti degli esempi utilizzano la [chiave di avvio](#starter-key) perché fornisce alcune chiamate a endpoint di stima gratuite nella [quota](luis-boundaries.md#key-limits).  


### <a name="query-prediction-resources"></a>Risorse di stima query

* La chiave di runtime può essere usata per tutte le app LUIS o per app LUIS specifiche. 
* Non usare la chiave di runtime per la creazione di app LUIS. 

L'endpoint di runtime LUIS accetta due stili di query, entrambi utilizzano la chiave di runtime dell'endpoint di stima, ma in posizioni diverse.

L'endpoint usato per accedere al runtime usa un sottodominio univoco per l'area della risorsa, indicato con `{region}` nella tabella seguente. 

## <a name="assignment-of-the-key"></a>Assegnazione della chiave

È possibile [assegnare](luis-how-to-azure-subscription.md) la chiave di runtime nel [portale Luis](https://www.luis.ai) o tramite le API corrispondenti. 

## <a name="key-limits"></a>Limiti delle chiavi

È possibile creare fino a 10 chiavi di creazione per ogni area per sottoscrizione. 

Vedere [limiti chiave](luis-boundaries.md#key-limits) e [aree di Azure](luis-reference-regions.md). 

Le regioni di pubblicazione sono diverse dalle regioni di creazione. Assicurarsi di creare un'app nell'area di creazione corrispondente all'area di pubblicazione in cui si vuole che venga individuata l'applicazione client.

## <a name="key-limit-errors"></a>Errori relativi ai limiti delle chiavi
Se si supera la quota transazioni al secondo (TPS), viene visualizzato un errore HTTP 429. Se si supera la quota di transazioni per mese (TPS), viene visualizzato un errore HTTP 403. 

## <a name="contributions-from-other-authors"></a>Contributi di altri autori

**Per la creazione di app [migrate delle risorse](luis-migration-authoring.md)** : i _collaboratori_ vengono gestiti nel portale di Azure per la risorsa di creazione, usando la pagina **controllo di accesso (IAM)** . Informazioni [su come aggiungere un utente](luis-how-to-collaborate.md), usando l'indirizzo di posta elettronica del collaboratore e il ruolo _collaboratore_ . 

**Per le app che non sono state ancora migrate**: tutti i _collaboratori_ vengono gestiti nel portale Luis dalla pagina **Manage-> collaboratori** .

## <a name="move-transfer-or-change-ownership"></a>Spostare, trasferire o modificare la proprietà

Un'app è definita dalle risorse di Azure, che è determinata dalla sottoscrizione del proprietario. 

È possibile spostare l'app LUIS. Usare le risorse di documentazione seguenti nell'portale di Azure o nell'interfaccia della riga di comando di Azure:

* [Spostare l'app tra le risorse di authoring LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Spostare una risorsa in un nuovo gruppo di risorse o sottoscrizione](../../azure-resource-manager/resource-group-move-resources.md)
* [Spostare una risorsa all'interno della stessa sottoscrizione o tra sottoscrizioni](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)

Per trasferire la [Proprietà](../../billing/billing-subscription-transfer.md) della sottoscrizione: 

**Per gli utenti che hanno eseguito la migrazione delle app [migrate](luis-migration-authoring.md) per la creazione di risorse**: come proprietario della risorsa, è possibile aggiungere un `contributor`.

**Per gli utenti che non hanno ancora**eseguito la migrazione: esportare l'app come file JSON. Un altro utente LUIS può importare l'app, diventando così il proprietario dell'app. La nuova app avrà un ID app diverso.  

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
|Eseguire il training|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Accesso al runtime dell'endpoint di stima

L'accesso per eseguire query sull'endpoint di stima è controllato da un'impostazione nella pagina **informazioni sull'applicazione** nella sezione **Gestisci** . 

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

LUIS non ha il concetto di trasferimento della proprietà di una risorsa. 

## <a name="securing-the-endpoint"></a>Protezione dell'endpoint 

È possibile controllare chi può visualizzare la chiave dell'endpoint di runtime di previsione LUIS chiamandola in un ambiente da server a server. Se si utilizza LUIS da un bot, la connessione tra il bot e LUIS è già protetta. Se si chiama direttamente l'endpoint LUIS, è necessario creare un'API lato server, ad esempio una [funzione](https://azure.microsoft.com/services/functions/) di Azure, con accesso controllato, ad esempio [AAD](https://azure.microsoft.com/services/active-directory/). Quando viene chiamata l'API lato server e vengono verificate l'autenticazione e l'autorizzazione, passare la chiamata a LUIS. Se da un lato questa strategia non impedisce gli attacchi man-in-the-middle, dall'altro offusca l'endpoint dagli utenti, consente di seguire l'accesso e di aggiungere la registrazione delle risposte endpoint, ad esempio [Application Insights](https://azure.microsoft.com/services/application-insights/).  

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui concetti di [controllo delle versioni](luis-concept-version.md). 
* Informazioni [su come creare chiavi](luis-how-to-azure-subscription.md).
