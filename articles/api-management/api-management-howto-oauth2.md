---
title: Autorizzare gli account per sviluppatori utilizzando OAuth 2.0 in Gestione API di Azure | Documentazione Microsoft
description: Informazioni su come autorizzare gli utenti tramite OAuth 2.0 in Gestione API.
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: bd8b3d13754e981235cbce39e919f39228820426
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Come autorizzare gli account per sviluppatori utilizzando OAuth 2.0 in Gestione API di Azure
Molte API supportano [OAuth 2.0](http://oauth.net/2/) per proteggere l'API e assicurare che solo gli utenti validi siano autorizzati all'accesso e che possano accedere solo alle risorse a cui hanno diritto. Per usare la console per sviluppatori interattiva di Gestione API di Azure con queste API, il servizio permette di configurare l'istanza del servizio per l'uso delle API abilitate per OAuth 2.0.

## <a name="prerequisites"> </a>Prerequisiti
Questa guida illustra come configurare un'istanza del servizio Gestione API per l'uso dell'autorizzazione OAuth 2.0 per gli account per sviluppatori, ma non viene spiegato come configurare un provider OAuth 2.0. La configurazione cambia in base al provider OAuth 2.0, sebbene le procedure siano simili e le informazioni necessarie usate per la configurazione di OAuth 2.0 nell'istanza del servizio Gestione API siano le stesse. Questo argomento mostra degli esempi di utilizzo di Azure Active Directory come provider OAuth 2.0.

> [!NOTE]
> Per altre informazioni sulla configurazione di OAuth 2.0 mediante Azure Active Directory, vedere l'esempio [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet].
> 
> 

## <a name="step1"> </a>Configurare un server autorizzazione OAuth 2.0 in Gestione API
Per iniziare, fare clic sul **portale di pubblicazione** nel Portale di Azure relativo al servizio Gestione API.

![Portale di pubblicazione][api-management-management-console]

> [!NOTE]
> Se non è ancora stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API][Create an API Management service instance].
> 
> 

Fare clic su **Sicurezza** dal menu **Gestione API** a sinistra, scegliere **OAuth 2.0** e fare clic su **Add authorization server**.

![OAuth 2.0][api-management-oauth2]

Dopo aver fatto clic su **Add authorization server**, viene visualizzato il modulo per il nuovo server autorizzazione.

![Nuovo server][api-management-oauth2-server-1]

Immettere un nome ed eventualmente una descrizione nei campi **Nome** e **Descrizione**. 

> [!NOTE]
> Questi campi vengono usati per identificare il server autorizzazione OAuth 2.0 all'interno dell'istanza del servizio Gestione API corrente e i loro valori non provengono dal server OAuth 2.0.
> 
> 

Immettere il **Client registration page URL**. In questa pagina gli utenti possono creare e gestire i loro account e il suo contenuto varia in base al provider OAuth 2.0 usato. **Client registration page URL** fa riferimento alla pagina che gli utenti possono usare per creare e configurare i propri account per i provider OAuth 2.0 che supportano la gestione degli account da parte degli utenti. Alcune organizzazioni non configurano o usano questa funzionalità, anche se è supportata dal provider OAuth 2.0. Se nel provider OAuth 2.0 non è stata configurata la gestione degli account da parte degli utenti, immettere qui un URL segnaposto, ad esempio l'URL della propria azienda, oppure un URL analogo a `https://placeholder.contoso.com`.

La sezione successiva del modulo contiene le impostazioni relative a **Authorization code grant types**, **Authorization endpoint URL** e **Authorization request method**.

![Nuovo server][api-management-oauth2-server-2]

Selezionare i tipi desiderati in **Authorization code grant types** . **Authorization code** è specificato per impostazione predefinita.

Immettere il valore relativo a **Authorization endpoint URL**. Per Azure Active Directory, questo URL sarà simile all'URL seguente, dove `<client_id>` viene sostituito dall'ID client che identifica l'applicazione in uso nel server OAuth 2.0.

`https://login.microsoftonline.com/<client_id>/oauth2/authorize`

L'impostazione **Authorization request method** specifica la modalità di invio della richiesta di autorizzazione al server OAuth 2.0. Il valore selezionato per impostazione predefinita è **GET** .

Nella sezione successiva vengono specificate le impostazioni **Token endpoint URL**, **Client authentication methods**, **Access token sending method** e **Ambito predefinito**.

![Nuovo server][api-management-oauth2-server-3]

Per un server OAuth 2.0 di Azure Active Directory, il **Token endpoint URL** avrà il seguente formato, dove `<APPID>` avrà il formato `yourapp.onmicrosoft.com`.

`https://login.microsoftonline.com/<APPID>/oauth2/token`

L'impostazione predefinita di **Client authentication methods** è **Basic**, mentre quella di **Access token sending method** è **Authorization header**. Questi valori vengono configurati in questa sezione del modulo, insieme a **Default scope**.

La sezione **Credenziali client** contiene l'**ID client** e il **Segreto client**, che vengono ricavati durante il processo di creazione e configurazione del server OAuth 2.0. Una volta specificati l'**ID client** e il **Segreto client**, viene generato il **redirect_uri** per il **codice autorizzazione**. Questo URI viene usato per configurare l'URL di risposta nella configurazione del server OAuth 2.0.

![Nuovo server][api-management-oauth2-server-4]

Se **Authorization code grant types** è impostato su **Resource owner password**, la sezione **Resource owner password credentials** viene usata per specificare le credenziali; in caso contrario è possibile lasciarla vuota.

![Nuovo server][api-management-oauth2-server-5]

Dopo aver completato il modulo, fare clic su **Salva** per salvare la configurazione del server autorizzazione OAuth 2.0 di Gestione API. Dopo aver salvato la configurazione del server, è possibile configurare le API in modo che usino questa configurazione, come illustrato nella sezione successiva.

## <a name="step2"></a>Configurare un'API per l'uso di un'autorizzazione utente OAuth 2.0
Fare clic su **API** dal menu **Gestione API** a sinistra, fare clic sul nome dell'API desiderata, scegliere **Sicurezza**, quindi selezionare la casella relativa a **OAuth 2.0**.

![Autorizzazione utente][api-management-user-authorization]

Selezionare il **server autorizzazione** desiderato dall'elenco a discesa e fare clic su **Salva**.

![Autorizzazione utente][api-management-user-authorization-save]

## <a name="step3"></a>Test dell'autorizzazione utente OAuth 2.0 nel Portale per sviluppatori
Dopo aver configurato il server autorizzazione OAuth 2.0 e l'API per l'uso di tale server, è possibile testarlo andando al portale per sviluppatori e chiamando un'API.  Fare clic su **Developer portal** nel menu in alto a destra.

![Portale per sviluppatori][api-management-developer-portal-menu]

Fare clic su **API** nel menu superiore e scegliere **API Echo**.

![API Echo][api-management-apis-echo-api]

> [!NOTE]
> Se è stata configurata una sola API o se ne è visibile solo una per l'account, facendo clic sulle API vengono visualizzate le operazioni per l'API.
> 
> 

Selezionare l'operazione **GET su risorsa**, fare clic su **Apri console**, quindi selezionare **codice di autorizzazione** dal menu a discesa.

![Open console][api-management-open-console]

Quando **Authorization code** è selezionato, viene visualizzata una finestra popup con il modulo di accesso del provider OAuth 2.0. In questo esempio il modulo di accesso viene fornito da Azure Active Directory.

> [!NOTE]
> Se i popup sono stati disattivati, verrà richiesto di attivarli tramite il browser. Dopo averli attivati, selezionare di nuovo **Authorization code** per visualizzare il modulo di accesso.
> 
> 

![pagina di accesso][api-management-oauth2-signin]

Dopo aver effettuato l'accesso, le **intestazioni della richiesta** vengono compilate con un'intestazione `Authorization : Bearer` che autorizza la richiesta.

![Token di intestazione della richiesta][api-management-request-header-token]

A questo punto è possibile configurare i valori desiderati per i restanti parametri e inviare la richiesta. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso di OAuth 2.0 e di Gestione API, vedere il video seguente e l’ [articolo](api-management-howto-protect-backend-with-aad.md)correlato.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Protecting-Web-API-Backend-with-Azure-Active-Directory-and-API-Management/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

