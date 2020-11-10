---
title: ID della stringa di localizzazione - Azure Active Directory B2C | Microsoft Docs
description: Specificare gli ID per una definizione del contenuto con ID api.signuporsignin in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 45357092784bd9c8821a81b07ce3c381c4ce7989
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410505"
---
# <a name="localization-string-ids"></a>ID della stringa di localizzazione

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L'elemento **Localizzazione** consente di supportare più impostazioni locali o lingue nei criteri per i percorsi utente. Questo articolo fornisce l'elenco degli ID di localizzazione che è possibile usare nei criteri. Per acquisire familiarità con la localizzazione dell'interfaccia utente, vedere [Localizzazione](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Elementi della pagina di iscrizione o accesso

Gli ID seguenti vengono utilizzati per una definizione di contenuto con un ID `api.signuporsignin` e un [profilo tecnico autocertificato](self-asserted-technical-profile.md).

| ID | Valore predefinito |
| -- | ------------- |
| **local_intro_email** | Accesso con l'account esistente |
| **logonIdentifier_email** | Indirizzo di posta elettronica |
| **requiredField_email** | Immettere l'indirizzo di posta elettronica |
| **invalid_email** | Immettere un indirizzo di posta elettronica valido |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&'' *+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)* $ |
| **local_intro_username** | Accesso con il nome utente |
| **logonIdentifier_username** | Username |
| **requiredField_username** | Immettere il nome utente |
| **password** | Password |
| **requiredField_password** | Immettere la password |
| **invalid_password** | Il formato della password immessa non è quello previsto. |
| **forgotpassword_link** | Password dimenticata? |
| **createaccount_intro** | Non si dispone di un account? |
| **createaccount_link** | Effettua l'iscrizione ora |
| **divider_title** | OR |
| **cancel_message** | L'utente ha dimenticato la password |
| **button_signin** | Accesso |
| **social_intro** | Accedere con l'account di social networking |
  **remember_me** |Mantieni l'accesso. |
| **unknown_error** | Si è verificato un errore durante l'accesso. Riprovare più tardi. |

L'esempio seguente illustra l'uso di alcuni elementi dell'interfaccia utente nella pagina per l'iscrizione o l'accesso:

![Elementi dell'esperienza utente della pagina per l'iscrizione o l'accesso](./media/localization-string-ids/localization-susi.png)


### <a name="sign-up-or-sign-in-identity-providers"></a>Provider di identità di iscrizione o accesso

L'ID dei provider di identità viene configurato nell'elemento del percorso utente **ClaimsExchange**. Per localizzare il titolo del provider di identità **ElementType** è impostato su `ClaimsProvider` mentre **StringId** è impostato sull'ID di `ClaimsExchange`.

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

L'esempio seguente localizza il provider di identità Facebook sull'arabo:

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Messaggi di errore in fase di iscrizione o di accesso

| ID | Valore predefinito |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | La password non è corretta. |
| **UserMessageIfPasswordExpired**| La password è scaduta.|
| **UserMessageIfClaimsPrincipalDoesNotExist** | L'account non è stato trovato. |
| **UserMessageIfOldPasswordUsed** | È stata usata una password non aggiornata. |
| **DefaultMessage** | Nome utente o password non validi. |
| **UserMessageIfUserAccountDisabled** | L'account è stato bloccato. Contattare il supporto tecnico per sbloccarlo, quindi riprovare. |
| **UserMessageIfUserAccountLocked** | L'account è stato temporaneamente bloccato per impedirne l'uso non autorizzato. Riprovare più tardi. |
| **AADRequestsThrottled** | In questo momento sono presenti troppe richieste. Attendere qualche minuto e riprovare. |

### <a name="sign-up-or-sign-in-example"></a>Esempio di iscrizione o accesso

```xml
<LocalizedResources Id="api.signuporsignin.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">Email Address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_email">Please enter your email</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">Username</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="password">Password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_link">Sign up now</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_username">Please enter your user name</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_intro">Don't have an account?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">Forgot your password?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="divider_title">OR</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has forgotten their password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_signin">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="social_intro">Sign in with your social account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_password">Please enter your password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_password">The password you entered is not in the expected format.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_username">Sign in with your user name</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_email">Sign in with your existing account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_email">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="unknown_error">We are having trouble signing you in. Please try again later.</LocalizedString>
    <!-- Uncomment the remember_me only if the keep me signed in is activated. 
    <LocalizedString ElementType="UxElement" StringId="remember_me">Keep me signed in</LocalizedString> -->
    <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;’'+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)$</LocalizedString>
    <LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfPasswordExpired">Your password has expired.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">Looks like you used an old password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">Invalid username or password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Elementi dell'interfaccia utente delle pagine di iscrizione e di autocertificazione

Di seguito sono riportati gli ID per una definizione di contenuto con un ID `api.localaccountsignup` o qualsiasi definizione di contenuto che inizia con `api.selfasserted` , ad esempio `api.selfasserted.profileupdate` e `api.localaccountpasswordreset` , e un [profilo tecnico autocertificato](self-asserted-technical-profile.md).

| ID | Valore predefinito |
| -- | ------------- |
| **ver_sent** | Il codice di verifica è stato inviato a: |
| **ver_but_default** | Predefinito |
| **cancel_message** | L'utente ha annullato l'immissione delle informazioni autocertificate |
| **preloader_alt** | Attendere |
| **ver_but_send** | Inviare il codice di verifica |
| **alert_yes** | Sì |
| **error_fieldIncorrect** | Uno o più campi sono stati compilati in modo non corretto. Verificare le immissioni e riprovare. |
| **year** | Year |
| **verifying_blurb** | Attendere mentre vengono elaborate le informazioni. |
| **button_cancel** | Annulla |
| **ver_fail_no_retry** | Sono stati effettuati troppi tentativi non validi. Riprovare più tardi. |
| **month** | Month |
| **ver_success_msg** | Indirizzo di posta elettronica verificato. È ora possibile continuare. |
| **months** | Gennaio, febbraio, marzo, aprile, maggio, giugno, luglio, agosto, settembre, ottobre, novembre, dicembre |
| **ver_fail_server** | Si sono verificati problemi nella verifica dell'indirizzo di posta elettronica. Immettere un indirizzo di posta elettronica valido e riprovare. |
| **error_requiredFieldMissing** | Manca un campo obbligatorio. Compilare tutti i campi obbligatori e riprovare. |
| **initial_intro** | Specificare i dettagli seguenti. |
| **ver_but_resend** | Inviare un nuovo codice |
| **button_continue** | Create |
| **error_passwordEntryMismatch** | I campi di immissione della password non corrispondono. Immettere la stessa password in entrambi i campi e riprovare. |
| **ver_incorrect_format** | Il formato non è corretto. |
| **ver_but_edit** | Cambiare l'indirizzo di posta elettronica |
| **ver_but_verify** | Verificare il codice |
| **alert_no** | No |
| **ver_info_msg** | Il codice di verifica è stato inviato alla Posta in arrivo. Copiarlo nella casella di input qui di seguito. |
| **day** | Giorno |
| **ver_fail_throttled** | Sono state effettuate troppe richieste di verifica di questo indirizzo di posta elettronica. Attendere qualche minuto e riprovare. |
| **helplink_text** | Che cos'è? |
| **ver_fail_retry** | Il codice non è corretto. Riprovare. |
| **alert_title** | Annullare l'immissione dei dettagli personali |
| **required_field** | Informazione obbligatoria. |
| **alert_message** | Annullare l'immissione dei dettagli personali? |
| **ver_intro_msg** | È necessaria la verifica. Fare clic sul pulsante Invia. |
| **ver_input** | Codice di verifica |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Messaggi di errore delle pagine di iscrizione e di autocertificazione

| ID | Valore predefinito |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Un utente con il nome specificato esiste già. Sceglierne un altro. |
| **UserMessageIfClaimNotVerified** | Attestazione non verificata: {0} |
| **UserMessageIfIncorrectPattern** | Modello non corretto per: {0} |
| **UserMessageIfMissingRequiredElement** | Manca un elemento obbligatorio: {0} |
| **UserMessageIfValidationError** | Errore durante la convalida da: {0} |
| **UserMessageIfInvalidInput** | {0} contiene un input non valido. |
| **ServiceThrottled** | In questo momento sono presenti troppe richieste. Attendere qualche minuto e riprovare. |

L'esempio seguente illustra l'uso di alcuni elementi dell'interfaccia utente nella pagina di iscrizione:

![Pagina di iscrizione con etichette per i nomi degli elementi dell'interfaccia utente](./media/localization-string-ids/localization-sign-up.png)

L'esempio seguente illustra l'uso di alcuni elementi dell'interfaccia utente nella pagina di iscrizione, dopo che l'utente ha fatto clic sul pulsante di invio del codice di verifica:

![Elementi dell'esperienza utente per la verifica della posta elettronica della pagina di iscrizione](./media/localization-string-ids/localization-email-verification.png)

## <a name="sign-up-and-self-asserted-pages-example"></a>Esempio di iscrizione e di pagine autocertificate

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Email address that can be used to contact you.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">Enter new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">Confirm New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">Confirm new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">Display Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">Your display name.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">Surname</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">Your surname (also known as family name or last name).</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">Given Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">Your given name (also known as first name).</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">A required field is missing. Please fill out all required fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="helplink_text">What is this?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please provide the following details.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="preloader_alt">Please wait</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="required_field">This information is required.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_edit">Change e-mail</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_resend">Send new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_send">Send verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_verify">Verify code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">That code is incorrect. Please try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_server">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_info_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_input">Verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_success_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">Claim not verified: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">A user with the specified ID already exists. Please choose a different one.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">Incorrect pattern for: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">{0} has invalid input.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">Missing required element: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">Error in validation by: {0}</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="phone-factor-authentication-page-user-interface-elements"></a>Elementi dell'interfaccia utente della pagina di autenticazione del fattore telefono

Di seguito sono riportati gli ID per una definizione di contenuto con ID `api.phonefactor` e [profilo tecnico Phone Factor](phone-factor-technical-profile.md).

| ID | Valore predefinito |
| -- | ------------- |
| **button_verify** | Chiama |
| **country_code_label** | Prefisso internazionale |
| **cancel_message** | L'utente ha annullato l'autenticazione a più fattori |
| **text_button_send_second_code** | Inviare un nuovo codice |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Nel record dell'utente è presente il seguente numero. È possibile inviare un codice di autenticazione via SMS o con una telefonata. |
| **intro_mixed_p** | Nel record dell'utente sono presenti i seguenti numeri. Scegliere un numero al quale sia possibile inviare un codice di autenticazione con una telefonata o via SMS. |
| **button_verify_code** | Verificare il codice |
| **requiredField_code** | Immettere il codice di verifica ricevuto |
| **invalid_code** | Immettere il codice a sei cifre ricevuto |
| **button_cancel** | Annulla |
| **local_number_input_placeholder_text** | Numero di telefono |
| **button_retry** | Riprova |
| **alternative_text** | Non ho il telefono con me |
| **intro_phone_p** | Nel record dell'utente sono presenti i seguenti numeri. Scegliere un numero al quale si vuole ricevere una telefonata per l'autenticazione. |
| **intro_phone** | Nel record dell'utente è presente il seguente numero. Faremo una telefonata per autenticare l'utente. |
| **enter_code_text_intro** | Immettere il codice di verifica nel campo seguente oppure  |
| **intro_entry_phone** | Nel campo seguente immettere il numero al quale si vuole ricevere una telefonata per l'autenticazione. |
| **intro_entry_sms** | Nel campo seguente immettere il numero al quale si vuole ricevere un SMS con il codice di autenticazione. |
| **button_send_code** | Invia codice |
| **invalid_number** | Immettere un numero di telefono valido |
| **intro_sms** | Nel record dell'utente è presente il seguente numero. Verrà inviato un codice di autenticazione via SMS. |
| **intro_entry_mixed** | Nel campo seguente immettere il numero al quale si vuole ricevere un SMS con il codice o una telefonata per l'autenticazione. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |Nel record dell'utente sono presenti i seguenti numeri. Scegliere un numero al quale sia possibile inviare un codice di autenticazione via SMS. |
| **requiredField_countryCode** | Selezionare il prefisso internazionale |
| **requiredField_number** | Immettere il proprio numero di telefono |
| **country_code_input_placeholder_text** |Paese o area geografica |
| **number_label** | Numero di telefono |
| **error_tryagain** | Il numero di telefono specificato è occupato o non disponibile. Verificare il numero e riprovare. |
| **error_incorrect_code** | Il codice di verifica immesso non corrisponde a quello in archivio. Riprovare o richiedere un nuovo codice. |
| **countryList** | Vedere [l'elenco dei paesi](#phone-factor-authentication-page-example). |
| **error_448** | Il numero di telefono specificato non è raggiungibile. |
| **error_449** | L'utente ha superato il numero di tentativi consentiti. |
| **verification_code_input_placeholder_text** | Codice di verifica |

L'esempio seguente illustra l'uso di alcuni elementi dell'interfaccia utente nella pagina di registrazione MFA:

![Elementi UX di registrazione di autenticazione a fattore di telefono](./media/localization-string-ids/localization-mfa1.png)

L'esempio seguente illustra l'uso di alcuni elementi dell'interfaccia utente nella pagina di convalida MFA:

![Elementi UX di convalida dell'autenticazione del telefono Factor](./media/localization-string-ids/localization-mfa2.png)

## <a name="phone-factor-authentication-page-example"></a>Esempio di pagina di autenticazione del fattore telefonico

```xml
<LocalizedResources Id="api.phonefactor.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="button_verify">Call Me</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_label">Country Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has canceled multi-factor authentication</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="text_button_send_second_code">Send a new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="code_pattern">\d{6}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed">We have the following number on record for you. We can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed_p">We have the following numbers on record for you. Choose a number that we can phone or send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_code">Please enter the verification code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_code">Please enter the 6-digit code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_number_input_placeholder_text">Phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_retry">Retry</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="alternative_text">I don't have my phone</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone_p">We have the following numbers on record for you. Choose a number that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone">We have the following number on record for you. We will phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="enter_code_text_intro">Enter your verification code below, or</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_phone">Enter a number below that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_sms">Enter a number below that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_number">Please enter a valid phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms">We have the following number on record for you. We will send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_mixed">Enter a number below that we can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_pattern">^\+(?:[0-9][\x20-]?){6,14}[0-9]$</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms_p">We have the following numbers on record for you. Choose a number that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_countryCode">Please select your country code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_number">Please enter your phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_input_placeholder_text">Country or region</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_label">Phone Number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_tryagain">The phone number you provided is busy or unavailable. Please check the number and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_incorrect_code">The verification code you have entered does not match our records. Please try again, or request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="countryList">{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_448">The phone number you provided is unreachable.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_449">User has exceeded the number of retry attempts.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_code_input_placeholder_text">Verification code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="strongAuthenticationPhoneNumber" StringId="DisplayName">Phone Number</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>

```

## <a name="verification-display-control-user-interface-elements"></a>Elementi dell'interfaccia utente del controllo di visualizzazione Verifica

Di seguito sono riportati gli ID per un [controllo di visualizzazione della verifica](display-control-verification.md) con il layout di pagina 2.1.0 o [versione](page-layout.md) successiva.

| ID | Valore predefinito |
| -- | ------------- |
|intro_msg| È necessaria la verifica. Fare clic sul pulsante Invia.|
|success_send_code_msg | Il codice di verifica è stato inviato alla Posta in arrivo. Copiarlo nella casella di input qui di seguito.|
|failure_send_code_msg | Si sono verificati problemi nella verifica dell'indirizzo di posta elettronica. Immettere un indirizzo di posta elettronica valido e riprovare.|
|success_verify_code_msg | Indirizzo di posta elettronica verificato. È ora possibile continuare.|
|failure_verify_code_msg | Si sono verificati problemi nella verifica dell'indirizzo di posta elettronica. Riprova.|
|but_send_code | Inviare il codice di verifica|
|but_verify_code | Verifica il codice|
|but_send_new_code | Inviare un nuovo codice|
|but_change_claims | Cambiare l'indirizzo di posta elettronica|

### <a name="verification-display-control-example"></a>Esempio di controllo di visualizzazione Verifica

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
   <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="verification-display-control-user-interface-elements-deprecated"></a>Elementi dell'interfaccia utente del controllo visualizzazione Verifica (deprecato)

Di seguito sono riportati gli ID per un [controllo di visualizzazione della verifica](display-control-verification.md) con layout di [pagina versione](page-layout.md) 2.0.0.

| ID | Valore predefinito |
| -- | ------------- |
|verification_control_but_change_claims |Modifica |
|verification_control_fail_send_code |Non è stato possibile inviare il codice. Riprovare più tardi. |
|verification_control_fail_verify_code |Non è stato possibile verificare il codice. Riprovare più tardi. |
|verification_control_but_send_code |Invia codice |
|verification_control_but_send_new_code |Invia nuovo codice |
|verification_control_but_verify_code |Verifica codice |
|verification_control_code_sent| Il codice di verifica è stato inviato. Copiarlo nella casella di input sottostante. |

### <a name="verification-display-control-example-deprecated"></a>Esempio di controllo di visualizzazione della verifica (deprecato)

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Messaggi di errore del servizio RESTful

Di seguito sono riportati gli ID dei messaggi di errore relativi al [profilo tecnico del servizio RESTful](restful-technical-profile.md) :

| ID | Valore predefinito |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | Non è stato possibile stabilire la connessione all'endpoint del servizio RESTful. URL del servizio RESTful: {0} |
|UserMessageIfCircuitOpen | {0} URL del servizio RESTful: {1} |
|UserMessageIfDnsResolutionFailed | Non è stato possibile risolvere il nome host dell'endpoint del servizio RESTful. URL del servizio RESTful: {0} |
|UserMessageIfRequestTimeout | Non è stato possibile stabilire la connessione all'endpoint del servizio RESTful entro i secondi limite del timeout {0} . URL del servizio RESTful: {1} |


### <a name="restful-service-example"></a>Esempio di servizio RESTful

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-mfa-error-messages"></a>Messaggi di errore relativi ad Azure MFA

Di seguito sono elencati gli ID relativi ai messaggi di errore di un [profilo tecnico Azure MFA](multi-factor-auth-technical-profile.md):

| ID | Valore predefinito |
| -- | ------------- |
|UserMessageIfCouldntSendSms | Non è possibile inviare SMS al telefono. Provare con un altro numero. |
|UserMessageIfInvalidFormat | Il formato del numero di telefono non è valido. Correggerlo e riprovare.|
|UserMessageIfMaxAllowedCodeRetryReached | È stato immesso un codice errato per troppe volte. Riprovare più tardi.|
|UserMessageIfServerError | Non è possibile usare il servizio MFA. Riprovare più tardi.|
|UserMessageIfThrottled | La richiesta è stata limitata. Riprovare più tardi.|
|UserMessageIfWrongCodeEntered|È stato immesso un codice errato. Riprovare.|

### <a name="azure-mfa-example"></a>Esempio di autenticazione a più fattori di Azure

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>Reimpostazione della password self-service di Azure AD

Di seguito sono riportati gli ID per Azure AD messaggi di errore del [profilo tecnico SSPR](aad-sspr-technical-profile.md) :

| ID | Valore predefinito |
| -- | ------------- |
|UserMessageIfChallengeExpired | Il codice è scaduto.|
|UserMessageIfInternalError | Si è verificato un errore interno del servizio di posta elettronica. riprovare più tardi.|
|UserMessageIfThrottled | Sono state inviate troppe richieste. riprovare più tardi.|
|UserMessageIfVerificationFailedNoRetry | È stato superato il numero massimo di tentativi di verifica.|
|UserMessageIfVerificationFailedRetryAllowed | La verifica non è riuscita. riprovare.|


### <a name="azure-ad-sspr-example"></a>Esempio di Azure AD SSPR

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>Messaggi di errore relativi alle password monouso

Di seguito sono elencati gli ID relativi ai messaggi di errore di un [profilo tecnico con password monouso](one-time-password-technical-profile.md).

| ID | Valore predefinito |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |È stato superato il numero massimo di tentativi per la verifica con password monouso |
|UserMessageIfSessionDoesNotExist |La sessione di verifica della password monouso è scaduta |
|UserMessageIfSessionConflict |Conflitto nella sessione di verifica della password monouso |
|UserMessageIfInvalidCode |La password monouso specificata per la verifica non è corretta |
|UserMessageIfVerificationFailedRetryAllowed |Il codice non è corretto. Riprovare. | 

### <a name="one-time-password-example"></a>Esempio di password una sola volta

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>Messaggi di errore relativi alle trasformazioni delle attestazioni

Di seguito sono elencati gli ID dei messaggi di errore relativi alle trasformazioni delle attestazioni:

| ID | Trasformazione delle attestazioni | Valore predefinito |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | Il confronto dei valori di attestazione booleani non è riuscito per il tipo di attestazione "inputClaim".| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | Il confronto dei valori di attestazione non è riuscito: l'operando di sinistra specificato è maggiore dell'operando di destra.|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | Il confronto dei valori di attestazione con StringComparison "OrdinalIgnoreCase" non è riuscito.|

### <a name="claims-transformations-example"></a>Esempio di trasformazioni delle attestazioni

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="next-steps"></a>Passaggi successivi

Per esempi di localizzazione, vedere gli articoli seguenti:

- [Personalizzazione della lingua con criteri personalizzati in Azure Active Directory B2C](custom-policy-localization.md)
- [Personalizzazione della lingua con i flussi utente in Azure Active Directory B2C](user-flow-language-customization.md)
