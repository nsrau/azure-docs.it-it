---
title: 'Azure Active Directory B2C: criteri predefiniti | Microsoft Docs'
description: Argomento che descrive il framework di criteri estendibile di Azure Active Directory B2C e che illustra come creare vari tipi di criteri
services: active-directory-b2c
documentationcenter: 
author: sama
manager: mbaldwin
editor: bryanla
ms.assetid: 0d453e72-7f70-4aa2-953d-938d2814d5a9
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: sama
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: e6943eb619963dd56b1c3909a57ae7d6cefd0ddc
ms.contentlocale: it-it
ms.lasthandoff: 06/21/2017


---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: criteri predefiniti


Il framework di criteri estendibile di Azure Active Directory (Azure AD) B2C è il punto di forza del servizio. I criteri descrivono le esperienze di identità, ad esempio iscrizione, accesso o modifica del profilo. Ad esempio, i criteri di iscrizione consentono di controllare i comportamenti configurando le impostazioni seguenti:

* Tipi di account (account di social network come Facebook o account locali come un indirizzo di posta elettronica) che gli utenti possono usare per effettuare l'iscrizione all'applicazione
* Attributi (ad esempio nome, codice postale, numero di scarpe) da raccogliere dall'utente durante la procedura di iscrizione
* Uso di Azure Multi-Factor Authentication
* Aspetto di tutte le pagine di iscrizione
* Informazioni (visualizzate come attestazioni in un token) che l'applicazione riceve al termine dell'esecuzione dei criteri

È possibile creare più criteri di tipi diversi nel proprio tenant e usarli nelle applicazioni in base alle esigenze. I criteri possono essere usati per più applicazioni. Gli sviluppatori possono in questo modo definire e modificare le esperienze delle identità degli utenti con modifiche minime al codice o anche senza alcuna modifica al codice.

Per usare i criteri, è disponibile una semplice interfaccia per sviluppatori. L'applicazione attiva i criteri tramite una richiesta di autenticazione HTTP standard (passando un parametro criteri nella richiesta) e riceve un token personalizzato come risposta. L'unica differenza tra le richieste che richiamano i criteri di iscrizione e le richieste che richiamano i criteri di accesso risiede nel nome del criterio usato nel parametro della stringa di query "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Per altre informazioni sul framework criteri, vedere [questo post di blog su Azure AD B2C sul blog relativo a Enterprise Mobility and Security](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-or-sign-in-policy"></a>Creare un criterio di iscrizione o accesso
Questo criterio consente di gestire le esperienze di iscrizione e di accesso degli utenti tramite un'unica configurazione. I consumatori vengono indirizzati sul percorso corretto (iscrizione o accesso) a seconda del contesto. 

Il criterio descrive anche i contenuti dei token che l'applicazione riceverà al completamento dell'iscrizione o dell'accesso. Un esempio di codice per i criteri di iscrizione o accesso è [disponibile in questo articolo](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  È consigliabile usare questo criterio invece di un criterio di iscrizione e un criterio di accesso.  

1. [Seguire prima di tutto questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).

2. Selezionare **Criteri di iscrizione o accesso**.

3. Nella parte superiore del pannello selezionare **+Aggiungi**.

4. Il **Nome** determina il nome dei criteri di iscrizione usati dall'applicazione. Immettere ad esempio **SiUpIn**.

5. Selezionare **Provider di identità** e quindi selezionare **Iscrizione posta elettronica**. Se lo si desidera, è anche possibile selezionare provider di identità basati su social network, se già configurati. Fare clic su **OK**.

6. Selezionare **Attributi di iscrizione**. Qui è possibile scegliere gli attributi che si desidera raccogliere dall'utente durante l'iscrizione. Selezionare ad esempio **Paese/Area**, **Nome visualizzato** e **Codice postale**. Fare clic su **OK**.

7. Selezionare **Attestazioni dell'applicazione**. Qui è possibile scegliere le attestazioni che verranno restituite all'applicazione nei token inviati al termine di un'esperienza di iscrizione o accesso con esito positivo. Selezionare ad esempio **Nome visualizzato**, **Provider di identità**, **Codice postale**, **L'utente è nuovo** e l'**ID oggetto dell'utente**.

8. Selezionare **Crea**. Si noti che i criteri creati vengono visualizzati come **B2C_1_SiUpIn** (il frammento **B2C\_1\_** viene aggiunto automaticamente) nel pannello **Criteri di iscrizione o accesso**.

9. Aprire i criteri facendo clic su **B2C_1_SiUpIn**.

10. Nel menu a discesa **Applicazioni** selezionare **Contoso B2C app**. Nel menu a discesa **URL di risposta/URI di reindirizzamento** selezionare `https://localhost:44321/`.

11. Selezionare **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui sarà possibile eseguire l'esperienza utente di iscrizione o accesso.
    
    > [!NOTE]
    > La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
    > 
    > 

## <a name="create-a-sign-up-policy"></a>Creare un criterio di iscrizione
Per abilitare l'iscrizione nell'applicazione, è necessario creare i criteri di iscrizione. I criteri descrivono l'esperienza utente durante la procedura di iscrizione e il contenuto dei token che l'applicazione riceverà al completamento dell'iscrizione.

1. [Seguire prima di tutto questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).

2. Selezionare **Criteri di iscrizione**.

3. Nella parte superiore del pannello selezionare **+Aggiungi**.

4. Il **Nome** determina il nome dei criteri di iscrizione usati dall'applicazione. Immettere ad esempio **SiUp**.

5. Selezionare **Provider di identità** e quindi selezionare **Iscrizione posta elettronica**. Se lo si desidera, è anche possibile selezionare provider di identità basati su social network, se già configurati. Fare clic su **OK**.

6. Selezionare **Attributi di iscrizione**. Qui è possibile scegliere gli attributi che si desidera raccogliere dall'utente durante l'iscrizione. Selezionare ad esempio **Paese/Area**, **Nome visualizzato**, **Codice postale** e quindi fare clic su **OK**.

7. Selezionare **Attestazioni dell'applicazione**. Qui si scelgono le attestazioni che verranno restituite all'applicazione nei token inviati al termine di un'esperienza di iscrizione con esito positivo. Selezionare ad esempio **Nome visualizzato**, **Provider di identità**, **Codice postale**, **L'utente è nuovo** e l'**ID oggetto dell'utente**.

8. Selezionare **Crea**. Si noti che i criteri appena creati vengono visualizzati come "**B2C_1_SiUp**" (il frammento **B2C\_1\_** viene aggiunto automaticamente) nel pannello **Criteri di iscrizione**.

9. Aprire i criteri selezionando **B2C_1_SiUp**.

10. Selezionare **Contoso B2C app** nel menu a discesa **Applicazioni** e `https://localhost:44321/` nel menu a discesa **URL di risposta/URI di reindirizzamento**.

11. Selezionare **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di iscrizione per l'applicazione.
    
    > [!NOTE]
    > La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
    > 
    > 

## <a name="create-a-sign-in-policy"></a>Creare un criterio di accesso
Per abilitare l'accesso nell'applicazione, è necessario creare criteri di accesso. I criteri descrivono l'esperienza utente durante la procedura di accesso e il contenuto dei token che l'applicazione riceverà al completamento dell'accesso.

1. [Seguire prima di tutto questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).

2. Selezionare **Criteri di accesso**.

3. Nella parte superiore del pannello selezionare **+Aggiungi**.

4. Il **Nome** determina il nome dei criteri di accesso usati dall'applicazione. Immettere ad esempio **SiIn**.

5. Fare clic su **Provider di identità** e selezionare **Accesso all'account locale**. Se lo si desidera, è anche possibile selezionare provider di identità basati su social network, se già configurati. Fare clic su **OK**.

6. Selezionare **Attestazioni dell'applicazione**. Qui si scelgono le attestazioni che verranno restituite all'applicazione nei token inviati al termine di un'esperienza di accesso con esito positivo. Selezionare ad esempio **Nome visualizzato**, **Provider di identità**, **Codice postale** e l'**ID oggetto dell'utente** e quindi fare clic su **OK**.

7. Selezionare **Crea**. Si noti che i criteri appena creati vengono visualizzati come **B2C_1_SiIn** (il frammento **B2C\_1\_** viene aggiunto automaticamente) nel pannello **Criteri di accesso**.

8. Aprire i criteri facendo clic su **B2C_1_SiIn**.

9. Nel menu a discesa **URL di risposta/URI di reindirizzamento** selezionare **Contoso B2C app** e `https://localhost:44321/`.

10. Selezionare **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di accesso all'applicazione.
    
    > [!NOTE]
    > La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
    > 
    > 

## <a name="create-a-profile-editing-policy"></a>Creare i criteri di modifica del profilo
Per abilitare la modifica del profilo nell'applicazione, è necessario creare i criteri di modifica del profilo. Questi criteri descrivono l'esperienza utente durante la procedura di modifica del profilo e il contenuto dei token che l'applicazione riceverà al completamento della procedura.

1. [Seguire prima di tutto questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).

1. Selezionare **Criteri di modifica del profilo**.

2. Nella parte superiore del pannello selezionare **+Aggiungi**.

3. Il **Nome** determina il nome dei criteri di modifica del profilo usati dall'applicazione. Immettere ad esempio **SiPe**.

4. Fare clic su **Provider di identità** e selezionare **Accesso all'account locale**. Se lo si desidera, è anche possibile selezionare provider di identità basati su social network, se già configurati. Fare clic su **OK**.

5. Selezionare **Attributi del profilo**. Qui è possibile scegliere gli attributi che l'utente potrà visualizzare e modificare. Selezionare ad esempio **Paese/Area**, **Nome visualizzato**, **Codice postale** e quindi fare clic su **OK**.

6. Selezionare **Attestazioni dell'applicazione**. Qui si scelgono le attestazioni che verranno restituite all'applicazione nei token inviati al termine di un'esperienza di modifica del profilo con esito positivo. Selezionare ad esempio **Nome visualizzato** e **Codice postale**.

8. Selezionare **Crea**. Si noti che i criteri appena creati vengono visualizzati come **B2C_1_SiPe** (il frammento **B2C\_1\_** viene aggiunto automaticamente) nel pannello **Criteri di modifica del profilo**.

9. Aprire i criteri selezionando **B2C_1_SiPe**.

10. Nel menu a discesa **Applicazioni** selezionare **Contoso B2C app**. Nel menu a discesa **URL di risposta/URI di reindirizzamento** selezionare `https://localhost:44321/`.

11. Selezionare **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di modifica del profilo nell'applicazione.
    
    > [!NOTE]
    > La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
    > 
    > 

## <a name="create-a-password-reset-policy"></a>Creare i criteri di reimpostazione delle password
Per abilitare la reimpostazione dettagliata delle password nell'applicazione, è necessario creare criteri di reimpostazione delle password. Si noti che l'opzione per la reimpostazione delle password a livello di tenant specificata in [Azure Active Directory B2C: configurare la reimpostazione password self-service per gli utenti](active-directory-b2c-reference-sspr.md) è comunque applicabile per i criteri di accesso. 

Questi criteri descrivono l'esperienza utente durante la procedura di reimpostazione delle password e il contenuto dei token che l'applicazione riceverà al completamento della procedura.

1. [Seguire prima di tutto questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).

2. Selezionare **Criteri di reimpostazione password**.

3. Nella parte superiore del pannello selezionare **+Aggiungi**.

4. **Nome** indica il nome dei criteri di reimpostazione delle password usati dall'applicazione. Immettere ad esempio **SSPR**.

5. Fare clic su **Provider di identità** e selezionare **Reimposta la password usando l'indirizzo di posta elettronica**. Fare clic su **OK**.

6. Selezionare **Attestazioni dell'applicazione**. Qui si scelgono le attestazioni che verranno restituite all'applicazione nei token inviati al termine di un'esperienza di reimpostazione delle password con esito positivo. Selezionare ad esempio **ID oggetto dell'utente**.

7. Selezionare **Crea**. Si noti che i criteri appena creati vengono visualizzati come **B2C_1_SSPR** (il frammento **B2C\_1\_** viene aggiunto automaticamente) nel pannello **Criteri di reimpostazione password**.

8. Aprire i criteri facendo clic su **B2C_1_SSPR**.

9. Nel menu a discesa **Applicazioni** selezionare **Contoso B2C app**. Nel menu a discesa **URL di risposta/URI di reindirizzamento** selezionare `https://localhost:44321/`.

10. Selezionare **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di reimpostazione delle password nell'applicazione.
    
    > [!NOTE]
    > La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
    > 
    > 

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Come si collegano i criteri di iscrizione o accesso ai criteri di reimpostazione delle password?
Quando si creano i criteri di iscrizione o accesso (con gli account locali), l'utente visualizza un collegamento **Password dimenticata?** nella prima pagina. Se si fa clic su questo collegamento non si attivano automaticamente i criteri di reimpostazione delle password. 

Viene invece restituito il codice di errore **`AADB2C90118`** all'app. L'applicazione deve gestire questo codice di errore e richiamare criteri di reimpostazione delle password specifici. Per altre informazioni, vedere un [esempio che illustra come collegare i criteri](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>È consigliabile usare criteri di iscrizione o accesso o criteri di iscrizione e criteri di accesso?
È consigliabile usare criteri di iscrizione o accesso al posto di criteri di iscrizione e criteri di accesso.  

I criteri di iscrizione o accesso dispongono di più funzionalità rispetto ai criteri di accesso. Consentono anche di usare la personalizzazione dell'interfaccia utente di pagina e sono meglio supportati nella localizzazione. 

I criteri di accesso sono consigliati se non si desidera localizzare i criteri, servono solo funzionalità di personalizzazione secondarie per la personalizzazione e si desidera disporre della funzionalità di reimpostazione delle password integrata.

## <a name="next-steps"></a>Passaggi successivi
* [Configurazione di token, sessione e accesso Single Sign-On](active-directory-b2c-token-session-sso.md)
* [Disabilitare la verifica della posta elettronica durante l'iscrizione dell'utente](active-directory-b2c-reference-disable-ev.md)


