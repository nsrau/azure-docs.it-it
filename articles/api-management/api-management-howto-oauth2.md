---
title: Autorizzare gli account per sviluppatori utilizzando OAuth 2.0 in Gestione API di Azure | Documentazione Microsoft
description: Informazioni su come autorizzare gli utenti tramite OAuth 2.0 in Gestione API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: c0e502c1627ebe9d63ca43cb46b982bb0639f3d7
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444726"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Come autorizzare gli account per sviluppatori utilizzando OAuth 2.0 in Gestione API di Azure

Molte API supportano [OAuth 2.0](http://oauth.net/2/) per proteggere l'API e assicurare che solo gli utenti validi siano autorizzati all'accesso e che possano accedere solo alle risorse a cui hanno diritto. Per usare la console per sviluppatori interattiva di Gestione API di Azure con queste API, il servizio permette di configurare l'istanza del servizio per l'uso delle API abilitate per OAuth 2.0.

## <a name="prerequisites"> </a>Prerequisiti

Questa guida illustra come configurare un'istanza del servizio Gestione API per l'uso dell'autorizzazione OAuth 2.0 per gli account per sviluppatori, ma non viene spiegato come configurare un provider OAuth 2.0. La configurazione cambia in base al provider OAuth 2.0, sebbene le procedure siano simili e le informazioni necessarie usate per la configurazione di OAuth 2.0 nell'istanza del servizio Gestione API siano le stesse. Questo argomento mostra degli esempi di utilizzo di Azure Active Directory come provider OAuth 2.0.

> [!NOTE]
> Per altre informazioni sulla configurazione di OAuth 2.0 mediante Azure Active Directory, vedere l'esempio [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet].

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Configurare un server autorizzazione OAuth 2.0 in Gestione API

> [!NOTE]
> Se non è ancora stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API][Create an API Management service instance].

1. Fare clic sulla scheda OAuth 2.0 nel menu a sinistra e fare clic su **+ Aggiungi**.

    ![Menu di OAuth 2.0](./media/api-management-howto-oauth2/oauth-01.png)

2. Immettere un nome ed eventualmente una descrizione nei campi **Nome** e **Descrizione**.

    > [!NOTE]
    > Questi campi vengono usati per identificare il server autorizzazione OAuth 2.0 all'interno dell'istanza del servizio Gestione API corrente e i loro valori non provengono dal server OAuth 2.0.

3. Immettere il **Client registration page URL**. In questa pagina gli utenti possono creare e gestire i loro account e il suo contenuto varia in base al provider OAuth 2.0 usato. **Client registration page URL** fa riferimento alla pagina che gli utenti possono usare per creare e configurare i propri account per i provider OAuth 2.0 che supportano la gestione degli account da parte degli utenti. Alcune organizzazioni non configurano o usano questa funzionalità, anche se è supportata dal provider OAuth 2.0. Se nel provider OAuth 2.0 non è stata configurata la gestione degli account da parte degli utenti, immettere qui un URL segnaposto, ad esempio l'URL della propria azienda, oppure un URL analogo a `https://placeholder.contoso.com`.

    ![Nuovo server OAuth 2.0](./media/api-management-howto-oauth2/oauth-02.png)

4. La sezione successiva del modulo contiene le impostazioni relative a **Authorization code grant types**, **Authorization endpoint URL** e **Authorization request method**.

    Selezionare i tipi desiderati in **Authorization code grant types** . **Authorization code** è specificato per impostazione predefinita.

    Immettere il valore relativo a **Authorization endpoint URL**. Per Azure Active Directory, questo URL sarà simile all'URL seguente, dove `<client_id>` viene sostituito dall'ID client che identifica l'applicazione in uso nel server OAuth 2.0.

    `https://login.microsoftonline.com/<client_id>/oauth2/authorize`

    L'impostazione **Authorization request method** specifica la modalità di invio della richiesta di autorizzazione al server OAuth 2.0. Il valore selezionato per impostazione predefinita è **GET** .

5. È quindi necessario specificare **URL dell'endpoint token**, **Metodi di autenticazione client**, **Metodo di invio del token di accesso** e **Ambito predefinito**.

    ![Nuovo server OAuth 2.0](./media/api-management-howto-oauth2/oauth-03.png)

    Per un server OAuth 2.0 di Azure Active Directory, il **Token endpoint URL** avrà il seguente formato, dove `<TenantID>` avrà il formato `yourapp.onmicrosoft.com`.

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    L'impostazione predefinita di **Client authentication methods** è **Basic**, mentre quella di **Access token sending method** è **Authorization header**. Questi valori vengono configurati in questa sezione del modulo, insieme a **Default scope**.

6. La sezione **Credenziali client** contiene l'**ID client** e il **Segreto client**, che vengono ricavati durante il processo di creazione e configurazione del server OAuth 2.0. Una volta specificati l'**ID client** e il **Segreto client**, viene generato il **redirect_uri** per il **codice autorizzazione**. Questo URI viene usato per configurare l'URL di risposta nella configurazione del server OAuth 2.0.

    ![Nuovo server OAuth 2.0](./media/api-management-howto-oauth2/oauth-04.png)

    Se **Authorization code grant types** è impostato su **Resource owner password**, la sezione **Resource owner password credentials** viene usata per specificare le credenziali; in caso contrario è possibile lasciarla vuota.

    Dopo aver completato il modulo, fare clic su **Crea** per salvare la configurazione del server di autorizzazione OAuth 2.0 di Gestione API. Dopo aver salvato la configurazione del server, è possibile configurare le API in modo che usino questa configurazione, come illustrato nella sezione successiva.

## <a name="step2"></a>Configurare un'API per l'uso di un'autorizzazione utente OAuth 2.0

1. Fare clic su **API** nel menu **Gestione API** a sinistra.

    ![API OAuth 2.0](./media/api-management-howto-oauth2/oauth-05.png)

2. Fare clic sul nome dell'API desiderata e quindi su **Impostazioni**. Scorrere fino alla sezione **Sicurezza** e quindi selezionare la casella **OAuth 2.0**.

    ![Impostazioni OAuth 2.0](./media/api-management-howto-oauth2/oauth-06.png)

3. Selezionare il **server autorizzazione** desiderato dall'elenco a discesa e fare clic su **Salva**.

    ![Impostazioni OAuth 2.0](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="step3"></a>Test dell'autorizzazione utente OAuth 2.0 nel Portale per sviluppatori

Dopo aver configurato il server autorizzazione OAuth 2.0 e l'API per l'uso di tale server, è possibile testarlo andando al portale per sviluppatori e chiamando un'API.  Fare clic su **Portale per sviluppatori** nel menu in alto dalla pagina **Panoramica** dell'istanza di Gestione API di Azure.

![Portale per sviluppatori][api-management-developer-portal-menu]

Fare clic su **API** nel menu superiore e scegliere **API Echo**.

![API Echo][api-management-apis-echo-api]

> [!NOTE]
> Se è stata configurata una sola API o se ne è visibile solo una per l'account, facendo clic sulle API vengono visualizzate le operazioni per l'API.

Selezionare l'operazione **GET su risorsa**, fare clic su **Apri console**, quindi selezionare **codice di autorizzazione** dal menu a discesa.

![Open console][api-management-open-console]

Quando **Authorization code** è selezionato, viene visualizzata una finestra popup con il modulo di accesso del provider OAuth 2.0. In questo esempio il modulo di accesso viene fornito da Azure Active Directory.

> [!NOTE]
> Se i popup sono stati disattivati, verrà richiesto di attivarli tramite il browser. Dopo averli attivati, selezionare di nuovo **Authorization code** per visualizzare il modulo di accesso.

![Accesso][api-management-oauth2-signin]

Dopo aver effettuato l'accesso, le **intestazioni della richiesta** vengono compilate con un'intestazione `Authorization : Bearer` che autorizza la richiesta.

![Token di intestazione della richiesta][api-management-request-header-token]

A questo punto è possibile configurare i valori desiderati per i restanti parametri e inviare la richiesta.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di OAuth 2.0 e di Gestione API, vedere il video seguente e l’ [articolo](api-management-howto-protect-backend-with-aad.md)correlato.

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
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

