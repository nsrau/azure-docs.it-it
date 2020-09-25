---
title: Sessioni SSO in Azure Active Directory B2C | Microsoft Docs
description: Panoramica della configurazione del comportamento della sessione in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0004c874a2011a78bb5cfe67ff0a840224d47bbb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258966"
---
# <a name="azure-ad-b2c-session"></a>Azure AD B2C sessione

Single Sign-on (SSO) consente di aggiungere sicurezza e praticità quando gli utenti possono accedere tra le applicazioni in Azure Active Directory B2C (Azure AD B2C). Questo articolo descrive i Single Sign-On metodi usati in Azure AD B2C e consente di scegliere il metodo SSO più appropriato durante la configurazione dei criteri.

Con Single Sign-On, gli utenti accedono una volta con un singolo account e ottengono l'accesso a più applicazioni. L'applicazione può essere un'applicazione Web, per dispositivi mobili o a pagina singola, indipendentemente dalla piattaforma o dal nome di dominio.

Quando l'utente accede inizialmente a un'applicazione, Azure AD B2C rende permanente una sessione basata su cookie. Dopo le successive richieste di autenticazione, Azure AD B2C legge e convalida la sessione basata su cookie e rilascia un token di accesso senza richiedere all'utente di eseguire di nuovo l'accesso. Se la sessione basata su cookie scade o diventa non valida, all'utente viene richiesto di eseguire di nuovo l'accesso.  

## <a name="sso-session-types"></a>Tipi di sessione SSO

L'integrazione con Azure AD B2C prevede tre tipi di sessioni SSO:

- **Azure ad B2C** sessione gestita da Azure ad B2C
- **Provider di identità federato** : sessione gestita dal provider di identità, ad esempio Facebook, Salesforce o account Microsoft
- **Applicazione** -sessione gestita dall'applicazione Web, per dispositivi mobili o a pagina singola

![Sessione SSO](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>Azure AD B2C SSO 

Quando un utente esegue l'autenticazione con un account locale o di social networking, Azure AD B2C archivia una sessione basata su cookie nel browser dell'utente. Il cookie viene archiviato con il nome di dominio del tenant Azure AD B2C, ad esempio `https://contoso.b2clogin.com` .

Se un utente accede inizialmente con un account federato e quindi durante l'intervallo di tempo della sessione (time-to-Live o TTL) accede alla stessa app o a un'altra app, Azure AD B2C tenta di acquisire un nuovo token di accesso dal provider di identità federato. Se la sessione del provider di identità federata è scaduta o non è valida, il provider di identità federato richiede le credenziali dell'utente. Se la sessione è ancora attiva (o se l'utente ha eseguito l'accesso con un account locale anziché un account federato), Azure AD B2C autorizza l'utente ed Elimina ulteriori richieste.

È possibile configurare il comportamento della sessione, inclusa la sessione TTL e il modo in cui Azure AD B2C condivide la sessione tra i criteri e le applicazioni.

### <a name="federated-identity-provider-sso"></a>SSO del provider di identità federato

Un provider di identità di social networking o aziendale gestisce la propria sessione. Il cookie viene archiviato nel nome di dominio del provider di identità, ad esempio `https://login.salesforce.com` . Azure AD B2C non controlla la sessione del provider di identità federato. Al contrario, il comportamento della sessione è determinato dal provider di identità federato. 

Si consideri lo scenario seguente:

1. Un utente accede a Facebook per verificare il feed.
2. Successivamente, l'utente apre l'applicazione e avvia il processo di accesso. L'applicazione reindirizza l'utente a Azure AD B2C per completare il processo di accesso.
3. Nella pagina Azure AD B2C iscrizione o accesso l'utente sceglie di eseguire l'accesso con il proprio account Facebook. L'utente viene reindirizzato a Facebook. Se è presente una sessione attiva in Facebook, all'utente non viene richiesto di fornire le proprie credenziali e viene immediatamente reindirizzato a Azure AD B2C con un token di Facebook.

### <a name="application-sso"></a>SSO applicazione

Un'applicazione Web, per dispositivi mobili o a pagina singola può essere protetta tramite l'accesso OAuth, i token ID o i token SAML. Quando un utente tenta di accedere a una risorsa protetta nell'app, l'app controlla se è presente una sessione attiva sul lato applicazione. Se non è presente alcuna sessione dell'app o la sessione è scaduta, l'app consente all'utente di Azure AD B2C alla pagina di accesso.

La sessione dell'applicazione può essere una sessione basata su cookie archiviata con il nome di dominio dell'applicazione, ad esempio `https://contoso.com` . Le applicazioni per dispositivi mobili potrebbero archiviare la sessione in modo diverso, ma con un approccio simile.

## <a name="azure-ad-b2c-session-configuration"></a>Configurazione della sessione di Azure AD B2C

### <a name="session-scope"></a>Ambito sessione

La sessione di Azure AD B2C può essere configurata con gli ambiti seguenti:

- **Tenant**: questa è l'impostazione predefinita. L'uso di questa impostazione consente a più applicazioni e flussi utente del tenant di B2C di condividere la stessa sessione utente. Ad esempio, una volta che un utente accede a un'applicazione, l'utente può anche accedere a un altro utente in seguito all'accesso.
- **Applicazione**: questa impostazione consente di mantenere una sessione utente esclusivamente per un'applicazione, indipendentemente dalle altre applicazioni. Ad esempio, è possibile usare questa impostazione se si vuole che l'utente possa accedere a Contoso Pharmacy indipendentemente dal fatto che l'utente abbia già eseguito l'accesso a Contoso Grocers.
- **Criterio**: questa impostazione consente di mantenere una sessione utente esclusivamente per un flusso utente, indipendentemente dalle applicazioni che lo usano. Se, ad esempio, l'utente ha già effettuato l'accesso e ha completato un passaggio di multi-factor authentication, l'utente può accedere a parti con sicurezza più elevata di più applicazioni, purché la sessione associata al flusso utente non scada.
- **Disabilitato** : questa impostazione impone all'utente di eseguire il flusso dell'intero utente a ogni esecuzione del criterio.

### <a name="session-life-time"></a>Durata della sessione

Il **tempo di durata della sessione** è la quantità di tempo durante il quale il cookie di sessione Azure ad B2C viene archiviato nel browser dell'utente dopo l'autenticazione. È possibile impostare la durata della sessione su un valore compreso tra 15 e 720 minuti.

### <a name="keep-me-signed-in"></a>Mantieni l'accesso

La funzionalità [Mantieni l'accesso](custom-policy-keep-me-signed-in.md) consente di estendere la durata della sessione utilizzando un cookie permanente. La sessione rimane attiva dopo la chiusura e la riapertura del browser da parte dell'utente. La sessione viene revocata solo quando un utente si disconnette. La funzionalità Mantieni l'accesso si applica solo all'accesso con account locali.

La funzionalità Mantieni l'accesso ha la precedenza sul tempo di vita della sessione. Se la funzionalità Mantieni l'accesso è abilitata e l'utente le seleziona, questa funzionalità impone la scadenza della sessione. 

### <a name="session-expiry-type"></a>Tipo di scadenza della sessione

Il **tipo di scadenza della sessione** indica il modo in cui una sessione viene estesa dall'impostazione della durata della sessione o l'impostazione Mantieni l'accesso.

- **Rolling** : indica che la sessione viene estesa ogni volta che l'utente esegue un'autenticazione basata su cookie (impostazione predefinita).
- **Absolute** : indica che l'utente deve eseguire di nuovo l'autenticazione dopo il periodo di tempo specificato.

## <a name="sign-out"></a>Disconnessione

Quando si desidera disconnettersi l'utente dall'applicazione, non è sufficiente cancellare i cookie dell'applicazione o terminare la sessione con l'utente. È necessario reindirizzare l'utente a Azure AD B2C per disconnettersi. In caso contrario, l'utente potrebbe essere in grado di eseguire di nuovo l'autenticazione alle applicazioni senza immettere di nuovo le credenziali.

In seguito a una richiesta di disconnessione, Azure AD B2C:

1. Invalida la sessione basata su cookie Azure AD B2C.
1. Tenta di disconnettersi dai provider di identità federati:
   - OpenId Connect: se l'endpoint di configurazione noto del provider di identità specifica un `end_session_endpoint` percorso.
   - SAML: se i metadati del provider di identità contengono il `SingleLogoutService` percorso.
1. Facoltativamente, si disconnette da altre applicazioni. Per altre informazioni, vedere la sezione [Single Sign-out](#single-sign-out) .

La disconnessione Cancella lo stato di Single Sign-On dell'utente con Azure AD B2C, ma potrebbe non disconnettersi l'utente dalla propria sessione del provider di identità di social networking. Se l'utente seleziona lo stesso provider di identità durante un accesso successivo, potrebbe eseguire nuovamente l'autenticazione senza immettere le credenziali. Se un utente vuole disconnettersi dall'applicazione, non significa necessariamente che voglia disconnettersi dal proprio account Facebook. Tuttavia, se vengono utilizzati account locali, la sessione dell'utente termina correttamente.

### <a name="single-sign-out"></a>Single Sign-Out 


> [!NOTE]
> Questa funzionalità è limitata ai [criteri personalizzati](custom-policy-overview.md).

Quando si reindirizza l'utente all'endpoint di disconnessione Azure AD B2C (per i protocolli OAuth2 e SAML), Azure AD B2C Cancella la sessione dell'utente dal browser. Tuttavia, l'utente potrebbe ancora essere connesso ad altre applicazioni che usano Azure AD B2C per l'autenticazione. Per consentire a tali applicazioni di disconnettersi l'utente contemporaneamente, Azure AD B2C invia una richiesta HTTP GET all'oggetto registrato `LogoutUrl` di tutte le applicazioni a cui l'utente è attualmente connesso.


Le applicazioni devono rispondere a questa richiesta cancellando qualsiasi sessione che identifica l'utente e restituendo una risposta `200`. Se si vuole supportare l'accesso Single Sign-out nell'applicazione, è necessario implementare `LogoutUrl` nel codice dell'applicazione. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [configurare il comportamento della sessione nel flusso utente](session-behavior.md).
- Informazioni su come [configurare il comportamento delle sessioni in criteri personalizzati](session-behavior-custom-policy.md).