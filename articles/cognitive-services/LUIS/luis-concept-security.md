---
title: Sicurezza durante la collaborazione
titleSuffix: Language Understanding - Azure Cognitive Services
description: L'accesso in creazione è disponibile ai proprietari e ai collaboratori. Per un'app privata, l'accesso endpoint è disponibile ai proprietari e ai collaboratori.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 5abc2026362e19b406b34a4321e766c3229cc0b4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894184"
---
# <a name="authoring-and-endpoint-user-access"></a>Accesso utente endpoint e in creazione
L'accesso in creazione è disponibile ai proprietari e ai collaboratori. Per un'app privata, l'accesso endpoint è disponibile ai proprietari e ai collaboratori. Per un'app pubblica, l'accesso endpoint è disponibile a chiunque disponga di un proprio account LUIS e dell'ID dell'app pubblica. 

## <a name="access-to-authoring"></a>Accesso in creazione
L'accesso all'app dal sito Web [LUIS](luis-reference-regions.md#luis-website) o dalle [API di creazione](https://aka.ms/luis-authoring-apis) è controllato dal proprietario dell'app. 

Il proprietario e tutti i collaboratori hanno accesso in creazione all'app. 

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

## <a name="access-to-endpoint"></a>Accedere all'endpoint

L'accesso per eseguire una query sull'endpoint è controllato da un'impostazione nella pagina **Informazioni applicazione**, sotto la sezione **Gestisci**. 

![Impostazione dell'app su pubblica](./media/luis-concept-security/set-application-as-public.png)

|[Endpoint privato](#private-app-endpoint-security)|[Endpoint pubblico](#public-app-endpoint-access)|
|:--|:--|
|Disponibile per proprietario e collaboratori|Disponibile per proprietario, collaboratori e chiunque altro che conosca l'ID app|

### <a name="private-app-endpoint-security"></a>Sicurezza dell'endpoint dell'app privata

L'endpoint di un'app privata è disponibile a:

|Chiave e utente|Spiegazione|
|--|--|
|Chiave di creazione del proprietario| Fino a 1000 accessi endpoint|
|Chiavi di creazione dei collaboratori| Fino a 1000 accessi endpoint|
|Qualsiasi chiave assegnata a LUIS da un autore o un collaboratore|Basato sul livello di uso della chiave|

#### <a name="microsoft-user-accounts"></a>Account utente Microsoft

Autori e collaboratori possono assegnare chiavi a un'app LUIS privata. L'account utente Microsoft che crea la chiave LUIS nel portale di Azure deve essere il proprietario dell'app o un collaboratore. Non è possibile assegnare una chiave a un'app privata tramite un altro account di Azure.

Per altre informazioni sugli account utente Active Directory vedere [Tenant di Active Directory di Azure](luis-how-to-collaborate.md#azure-active-directory-tenant-user). 

### <a name="public-app-endpoint-access"></a>Accesso endpoint all'app pubblica

Dopo che un'app è stata configurata come pubblica, _qualsiasi_ chiave di creazione LUIS o chiave endpoint LUIS valida può eseguire una query nell'app, purché la chiave non abbia usato l'intera quota endpoint.

Un utente che non è proprietario né collaboratore può accedere solo a un'app pubblica se ne possiede l'ID. LUIS non ha un _mercato_ pubblico o un altro modo per cercare un'app pubblica.  

Un'app pubblica viene pubblicata in tutte le regioni in modo che un utente con una chiave di risorsa LUIS basata su regione possa accedere all'app in qualsiasi regione associata la chiave di risorsa.

## <a name="microsoft-user-accounts"></a>Account utente Microsoft

Gli autori e i collaboratori possono aggiungere chiavi a LUIS nella pagina Publish (Pubblica). L'account utente Microsoft che crea la chiave LUIS nel portale di Azure deve essere il proprietario dell'app o un collaboratore. 

Per altre informazioni sugli account utente Active Directory vedere [Tenant di Active Directory di Azure](luis-how-to-collaborate.md#azure-active-directory-tenant-user). 

## <a name="securing-the-endpoint"></a>Protezione dell'endpoint 

È possibile controllare chi può visualizzare la chiave endpoint LUIS chiamandola in un ambiente server-to-server. Se si utilizza LUIS da un bot, la connessione tra il bot e LUIS è già protetta. Se si chiama direttamente l'endpoint LUIS, è necessario creare un'API lato server, ad esempio una [funzione](https://azure.microsoft.com/services/functions/) di Azure, con accesso controllato, ad esempio [AAD](https://azure.microsoft.com/services/active-directory/). Quando viene chiamata l'API lato server e vengono verificate l'autenticazione e l'autorizzazione, passare la chiamata a LUIS. Se da un lato questa strategia non impedisce gli attacchi man-in-the-middle, dall'altro offusca l'endpoint dagli utenti, consente di seguire l'accesso e di aggiungere la registrazione delle risposte endpoint, ad esempio [Application Insights](https://azure.microsoft.com/services/application-insights/).  

## <a name="security-compliance"></a>Conformità agli standard di sicurezza
 
[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-security-compliance.md)]

## <a name="next-steps"></a>Passaggi successivi

Vedere le [procedure consigliate](luis-concept-best-practices.md) per imparare a usare finalità ed entità per le stime migliori.
