---
title: Iscrizione e accesso tramite telefono con criteri personalizzati
titleSuffix: Azure AD B2C
description: Inviare password monouso (OTP) negli SMS ai telefoni degli utenti dell'applicazione con criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9d4fa80f669493e4dc47a9ad0f9bfe9390d4ab24
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953577"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Configurare l'iscrizione e l'accesso tramite telefono con criteri personalizzati in Azure AD B2C

L'iscrizione e l'accesso tramite telefono in Azure Active Directory B2C (Azure AD B2C) consente agli utenti di iscriversi e accedere alle applicazioni usando una password monouso (OTP) inviata in un messaggio di testo al telefono. Le password monouso consentono di ridurre al minimo il rischio che gli utenti dimentichino o abbiano compromessa la loro password.

Attenersi alla procedura descritta in questo articolo per usare i criteri personalizzati per consentire ai clienti di iscriversi e accedere alle applicazioni usando una password monouso inviata al telefono.

## <a name="pricing"></a>Prezzi

Le password monouso vengono inviate agli utenti tramite SMS ed è possibile che vengano addebitati i costi per ogni messaggio inviato. Per informazioni sui prezzi, vedere la sezione relativa agli **addebiti separati** di [Azure Active Directory B2C prezzi](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="user-experience-for-phone-sign-up-and-sign-in"></a>Esperienza utente per l'iscrizione e l'accesso tramite telefono

Con l'iscrizione e l'accesso tramite telefono, l'utente può iscriversi all'app usando un numero di telefono come identificatore primario. L'esperienza dell'utente finale durante l'iscrizione e l'accesso è descritta di seguito.

> [!NOTE]
> È consigliabile includere le informazioni sul consenso nell'esperienza di iscrizione e accesso, in modo analogo al testo di esempio riportato di seguito. Questo testo di esempio è esclusivamente a scopo informativo. Per informazioni sul testo finale e sulla configurazione delle funzionalità per soddisfare le esigenze di conformità, vedere il breve manuale di monitoraggio del codice nel [sito Web CTIA](https://www.ctia.org/programs) e consultare i propri esperti legali o di conformità.
>
> *Specificando il numero di telefono, si acconsente alla ricezione di un codice di accesso monouso inviato da un SMS per consentire l'accesso a *&lt; Insert: &gt; nome dell'applicazione*. Potrebbero essere applicate le tariffe standard per i messaggi e i dati.*
>
> *&lt;Inserisci: un collegamento all'informativa sulla privacy&gt;*<br/>*&lt;Inserisci: un collegamento alle condizioni per il servizio&gt;*

Per aggiungere le proprie informazioni di consenso, personalizzare l'esempio seguente e includerlo in LocalizedResources per il ContentDefinition usato dalla pagina autocertificata con il controllo di visualizzazione (il file Phone-Email-Base.xml nel pacchetto di avvio per l'iscrizione tramite telefono &):

```xml
<LocalizedResources Id="phoneSignUp.en">        
    <LocalizedStrings>
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_msg_intro">By providing your phone number, you consent to receiving a one-time passcode sent by text message to help you sign into {insert your application name}. Standard messsage and data rates may apply.</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_text">Privacy Statement</LocalizedString>                
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_url">{insert your privacy statement URL}</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_text">Terms and Conditions</LocalizedString>             
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_url">{insert your terms and conditions URL}</LocalizedString>          
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please verify your country code and phone number</LocalizedString>        
    </LocalizedStrings>      
</LocalizedResources>
   ```

### <a name="phone-sign-up-experience"></a>Esperienza di iscrizione tramite telefono

Se l'utente non dispone già di un account per l'applicazione, è possibile crearne uno scegliendo il collegamento **Iscriviti ora** . Viene visualizzata una pagina di iscrizione, in cui l'utente seleziona il **paese**, immette il numero di telefono e seleziona **Invia codice**.

![L'utente avvia l'iscrizione tramite telefono](media/phone-authentication/phone-signup-start.png)

Viene inviato un codice di verifica monouso al numero di telefono dell'utente. L'utente immette il **codice di verifica** nella pagina di iscrizione, quindi seleziona **Verifica codice**. Se l'utente non è stato in grado di recuperare il codice, è possibile selezionare **Invia nuovo codice**.

![L'utente verifica il codice durante l'iscrizione tramite telefono](media/phone-authentication/phone-signup-verify-code.png)

 L'utente immette tutte le altre informazioni richieste nella pagina di iscrizione, ad esempio il **nome visualizzato**, il **nome** e il **Cognome** (paese e numero di telefono rimangono popolati). Se l'utente vuole usare un numero di telefono diverso, può scegliere **Cambia numero** per riavviare l'iscrizione. Al termine, l'utente seleziona **continua**.

![L'utente fornisce informazioni aggiuntive](media/phone-authentication/phone-signup-additional-info.png)

Successivamente, all'utente viene richiesto di fornire un messaggio di posta elettronica di ripristino. L'utente immette il proprio indirizzo di posta elettronica e quindi seleziona **Invia codice di verifica**. Viene inviato un codice alla posta in arrivo dell'utente, che è possibile recuperare e immettere nella casella **codice di verifica** . L'utente seleziona quindi **Verifica codice**. 

Una volta verificato il codice, l'utente seleziona **Crea** per creare il proprio account. In alternativa, se l'utente vuole usare un indirizzo di posta elettronica diverso, può scegliere **modifica messaggio di posta elettronica**.

![L'utente crea un account](media/phone-authentication/email-verification.png)

### <a name="phone-sign-in-experience"></a>Esperienza di accesso tramite telefono

Se l'utente dispone di un account esistente con numero di telefono come identificatore, l'utente immette il proprio numero di telefono e seleziona **continua**. Per confermare il paese e il numero di telefono, selezionare **continue (continua**). verrà inviato un codice di verifica una sola volta al telefono. L'utente immette il codice di verifica e seleziona **continua** per accedere.

![Esperienza utente per l'accesso tramite telefono](media/phone-authentication/phone-signin-screens.png)

## <a name="deleting-a-user-account"></a>Eliminare un account utente

In alcuni casi potrebbe essere necessario eliminare un utente e i dati associati dalla directory Azure AD B2C. Per informazioni dettagliate su come eliminare un account utente tramite il portale di Azure, fare riferimento a [queste istruzioni](/microsoft-365/compliance/gdpr-dsr-azure#step-5-delete). 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]



## <a name="prerequisites"></a>Prerequisiti

Prima di configurare OTP è necessario disporre delle risorse seguenti.

* [Tenant di Azure AD B2C](tutorial-create-tenant.md)
* [Applicazione Web registrata](tutorial-register-applications.md) nel tenant
* [Criteri personalizzati](custom-policy-get-started.md) caricati nel tenant

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Ottenere il pacchetto Starter & per l'iscrizione al telefono

Per iniziare, aggiornare i file di criteri personalizzati per l'iscrizione e l'accesso tramite telefono per l'uso con il tenant Azure AD B2C.

Nei passaggi seguenti si presuppone che siano stati completati i [prerequisiti](#prerequisites) e che sia già stato clonato il repository [Starter Pack dei criteri personalizzati][starter-pack] nel computer locale.

1. Individuare i [file di criteri personalizzati per l'iscrizione e l'accesso tramite telefono][starter-pack-phone] nel clone locale del repository Starter Pack oppure scaricarli direttamente. I file dei criteri XML si trovano nella seguente directory:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. In ogni file sostituire la stringa `yourtenant` con il nome del tenant del Azure ad B2C. Ad esempio, se il nome del tenant B2C è *contosob2c*, tutte le istanze di `yourtenant.onmicrosoft.com` diventano `contosob2c.onmicrosoft.com` .

1. Completare i passaggi della sezione [aggiungere ID applicazione ai criteri personalizzati](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) di [Introduzione ai criteri personalizzati in Azure Active Directory B2C](custom-policy-get-started.md). In questo caso, aggiornare `/phone-number-passwordless/` **`Phone_Email_Base.xml`** con gli **ID dell'applicazione (client)** delle due applicazioni registrate quando si completano i prerequisiti, *IdentityExperienceFramework* e *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Caricare i file dei criteri

1. Accedere al [portale di Azure](https://portal.azure.com) e passare al tenant di Azure ad B2C.
1. In **Criteri** selezionare **Identity Experience Framework**.
1. Selezionare **Carica criteri personalizzati**.
1. Caricare i file dei criteri nell'ordine seguente:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

Quando si carica ogni file, Azure aggiunge il prefisso `B2C_1A_` .

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. In **criteri personalizzati** selezionare **B2C_1A_SignUpOrSignInWithPhone**.
1. In **Seleziona applicazione** selezionare l'applicazione *app Web 1* registrata al termine dei prerequisiti.
1. Per **Seleziona URL di risposta** scegliere `https://jwt.ms` .
1. Selezionare **Esegui adesso** e iscriversi usando un indirizzo di posta elettronica o un numero di telefono.
1. Selezionare **Esegui** ancora una volta e accedere con lo stesso account per confermare di avere la configurazione corretta.

## <a name="get-user-account-by-phone-number"></a>Ottieni l'account utente per numero di telefono

Un utente che si iscrive con un numero di telefono, ma non fornisce un indirizzo di posta elettronica di ripristino, viene registrato nella directory Azure AD B2C con il proprio numero di telefono come nome di accesso. Se l'utente desidera modificare il numero di telefono, il help desk o il team di supporto deve prima trovare il proprio account, quindi aggiornare il numero di telefono.

È possibile trovare un utente con il proprio numero di telefono (nome di accesso) usando [Microsoft Graph](manage-user-accounts-graph-api.md):

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Ad esempio:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare il pacchetto Starter Pack per l'iscrizione e l'accesso tramite telefono (e altri Starter Pack) su GitHub: [Azure-Samples/Active-Directory-B2C-Custom-Policy-Starterpack/Scenarios/Phone-number-password][starter-pack-phone] i file dei criteri dello Starter Pack usano i profili tecnici di autenticazione a più fattori e le trasformazioni delle attestazioni del numero di telefono:
* [Definire un profilo tecnico Multi-Factor Authentication Azure AD](multi-factor-auth-technical-profile.md)
* [Definire le trasformazioni delle attestazioni del numero di telefono](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless