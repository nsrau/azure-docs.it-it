---
title: Informazioni sull'accesso alle applicazioni LUIS - Azure | Microsoft Docs
description: Informazioni sull'accesso in creazione all'app LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: fe0ee7d575e69c883ad7f980477e86fbd004778e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397244"
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
|Esportare i registri endpoint||
|Importare una versione||
|Rendere pubblica un'app|Quando un'app è pubblica, chiunque disponga di una chiave di creazione o endpoint può eseguire una query nell'app.|
|Modificare il modello|
|Pubblicare|
|Rivedere le espressioni endpoint per l'[apprendimento attivo](luis-how-to-review-endoint-utt.md)|
|Eseguire il training|

## <a name="access-to-endpoint"></a>Accedere all'endpoint
L'accesso all'endpoint per eseguire query nell'app LUIS è controllato dall'impostazione **Public** (Pubblica) dell'app nella pagina **Settings** (Impostazioni). Verificare la presenza di una chiave autorizzata con accessi quota rimanenti nella query endpont di un'app privata. La query endpoint di un'app pubblica deve inoltre fornire una chiave endpoint (da chiunque esegua la query) nella quale viene anche verificata la presenza di accessi quota rimanenti. 

La chiave endpoint viene passata nella stringa di query della richiesta GET o nell'intestazione della richiesta POST.

![Impostazione dell'app su pubblica](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>Sicurezza dell'endpoint dell'app privata
L'endpoint di un'app privata è disponibile a:

|Chiave e utente|Spiegazione|
|--|--|--|
|Chiave di creazione del proprietario| Fino a 1000 accessi endpoint|
|Chiavi di creazione dei collaboratori| Fino a 1000 accessi endpoint|
|Chiavi endpoint aggiunte dalla pagina **[Publish](luis-how-to-publish-app.md)** (Pubblica)|Il proprietario e i collaboratori possono aggiungere chiavi endpoint|

Le altre chiavi di creazione o endpoint **non** dispongono dell'accesso.

### <a name="public-app-endpoint-access"></a>Accesso endpoint all'app pubblica
Configurare l'app come **pubblica** nella pagina **Settings** (Impostazioni) dell'app. Dopo che un'app è stata configurata come pubblica, _qualsiasi_ chiave di creazione LUIS o chiave endpoint LUIS valida può eseguire una query nell'app, purché la chiave non abbia usato l'intera quota endpoint.

Un utente che non è proprietario né collaboratore può accedere solo a un'app pubblica se ne possiede l'ID. LUIS non ha un _mercato_ pubblico o un altro modo per cercare un'app pubblica.  

## <a name="microsoft-user-accounts"></a>Account utente Microsoft
Gli autori e i collaboratori possono aggiungere chiavi a LUIS nella pagina Publish (Pubblica). L'account utente Microsoft che crea la chiave LUIS nel portale di Azure deve essere il proprietario dell'app o un collaboratore. 

Per altre informazioni sugli account utente Active Directory vedere [Tenant di Active Directory di Azure](luis-how-to-collaborate.md#azure-active-directory-tenant-user). 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>Protezione dell'endpoint 
È possibile controllare chi può visualizzare la chiave endpoint LUIS chiamandola in un ambiente server-to-server. Se si utilizza LUIS da un bot, la connessione tra il bot e LUIS è già protetta. Se si chiama direttamente l'endpoint LUIS, è necessario creare un'API lato server, ad esempio una [funzione](https://azure.microsoft.com/services/functions/) di Azure, con accesso controllato, ad esempio [AAD](https://azure.microsoft.com/services/active-directory/). Quando viene chiamata l'API lato server e vengono verificate l'autenticazione e l'autorizzazione, passare la chiamata a LUIS. Se da un lato questa strategia non impedisce gli attacchi man-in-the-middle, dall'altro offusca l'endpoint dagli utenti, consente di seguire l'accesso e di aggiungere la registrazione delle risposte endpoint, ad esempio [Application Insights](https://azure.microsoft.com/services/application-insights/).  

## <a name="security-compliance"></a>Conformità agli standard di sicurezza
LUIS ha completato con successo l'auditing per le certificazioni ISO 27001:2013 e ISO 27018:2014 con ZERO non conformità (risultati) nel report di auditing. LUIS ha inoltre ottenuto la certificazione CSA STAR con il massimo riconoscimento (Gold Award) per la valutazione del livello di maturità (Maturity Capability). Azure è l'unico provider di servizi cloud pubblici ad aver acquisito questa certificazione. Per ulteriori dettagli, il servizio LUIS è incluso nella definizione aggiornata dell'ambito nel documento principale sulla [conformità](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) di Azure a cui viene fatto riferimento nelle pagine ISO di [Centro protezione](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001).  

## <a name="next-steps"></a>Passaggi successivi

Vedere le [procedure consigliate](luis-concept-best-practices.md) per imparare a usare finalità ed entità per le stime migliori.