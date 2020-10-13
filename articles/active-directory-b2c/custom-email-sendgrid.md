---
title: Verifica della posta elettronica personalizzata con SendGrid
titleSuffix: Azure AD B2C
description: Informazioni su come eseguire l'integrazione con SendGrid per personalizzare il messaggio di verifica inviato ai clienti quando si iscrivono per usare le applicazioni abilitate per Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e2040f52efae1955cf5a7b530358f2cec07b5fbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91396519"
---
# <a name="custom-email-verification-with-sendgrid"></a>Verifica della posta elettronica personalizzata con SendGrid

Usare un indirizzo di posta elettronica personalizzato in Azure Active Directory B2C (Azure AD B2C) per inviare messaggi di posta elettronica personalizzati agli utenti che si iscrivono per l'uso delle applicazioni. Usando [DisplayControls](display-controls.md) (attualmente in anteprima) e il provider di posta elettronica di terze parti SendGrid, è possibile usare il proprio modello di posta elettronica e *da:* indirizzo e oggetto, nonché la localizzazione del supporto e le impostazioni della password monouso (OTP) personalizzate.

Per la verifica tramite posta elettronica personalizzata è necessario usare un provider di posta elettronica di terze parti, ad esempio [SendGrid](https://sendgrid.com), [Mailjet](https://Mailjet.com)o [SparkPost](https://sparkpost.com), un'API REST personalizzata o qualsiasi provider di posta elettronica basato su http (incluso il proprio). Questo articolo descrive la configurazione di una soluzione che usa SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Creare un account SendGrid

Se non si ha già un account, iniziare con la configurazione di un account SendGrid (i clienti di Azure possono sbloccare 25.000 messaggi di posta elettronica gratuiti al mese). Per le istruzioni di installazione, vedere la sezione [creare un account SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) di [come inviare messaggi di posta elettronica usando SendGrid con Azure](../sendgrid-dotnet-how-to-send-email.md).

Assicurarsi di completare la sezione in cui si [Crea una chiave API SendGrid](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Registrare la chiave API da usare in un passaggio successivo.

## <a name="create-azure-ad-b2c-policy-key"></a>Crea chiave dei criteri di Azure AD B2C

Quindi, archiviare la chiave API SendGrid in una chiave dei criteri di Azure AD B2C per fare riferimento ai criteri.

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory Azure AD B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni**scegliere **manuale**.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `SendGridSecret`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
1. In **Segreto** immettere il segreto client registrato in precedenza.
1. Per **Uso chiave** selezionare **Firma**.
1. Selezionare **Create** (Crea).

## <a name="create-sendgrid-template"></a>Crea modello SendGrid

Con un account SendGrid creato e una chiave API SendGrid archiviata in una chiave dei criteri di Azure AD B2C, creare un [modello transazionale SendGrid dinamico](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/).

1. Nel sito di SendGrid aprire la pagina [modelli transazionali](https://sendgrid.com/dynamic_templates) e selezionare **Crea modello**.
1. Immettere un nome di modello univoco `Verification email` , ad esempio, e quindi selezionare **Salva**.
1. Per iniziare a modificare il nuovo modello, selezionare **Aggiungi versione**.
1. Selezionare **Editor del codice** e **continuare**.
1. Nell'editor HTML incollare il modello HTML seguente o usare il proprio. I `{{otp}}` `{{email}}` parametri e verranno sostituiti in modo dinamico con il valore della password monouso e con l'indirizzo di posta elettronica dell'utente.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Espandere **Impostazioni** a sinistra e, per **messaggio di posta elettronica**, immettere `{{subject}}` .
1. Selezionare **Salva modello**.
1. Tornare alla pagina **modelli transazionali** selezionando la freccia indietro.
1. Registrare l' **ID** del modello creato per l'uso in un passaggio successivo. Ad esempio: `d-989077fbba9746e89f3f6411f596fb96`. Questo ID viene specificato quando si [aggiunge la trasformazione delle attestazioni](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Aggiungere Azure AD B2C tipi di attestazione

Nel criterio aggiungere i tipi di attestazione seguenti all' `<ClaimsSchema>` elemento all'interno di `<BuildingBlocks>` .

Questi tipi di attestazioni sono necessari per generare e verificare l'indirizzo di posta elettronica usando un codice OTP (One-time password).

```xml
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="emailRequestBody">
  <DisplayName>SendGrid request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>Aggiungere la trasformazione delle attestazioni

Quindi, è necessaria una trasformazione delle attestazioni per restituire un'attestazione stringa JSON che costituirà il corpo della richiesta inviata a SendGrid.

La struttura dell'oggetto JSON è definita dagli ID nella notazione del punto di InputParameters e TransformationClaimTypes di InputClaims. I numeri nella notazione del punto implicano matrici. I valori provengono dalle proprietà InputClaims ' Values e InputParameters ' "value". Per ulteriori informazioni sulle trasformazioni delle attestazioni JSON, vedere le [trasformazioni delle attestazioni JSON](json-transformations.md).

Aggiungere la trasformazione delle attestazioni seguente all' `<ClaimsTransformations>` elemento all'interno di `<BuildingBlocks>` . Apportare gli aggiornamenti seguenti all'XML di trasformazione delle attestazioni:

* Aggiornare il `template_id` valore InputParameter con l'ID del modello transazionale SendGrid creato in precedenza in [create SendGrid template](#create-sendgrid-template).
* Aggiornare il `from.email` valore dell'indirizzo. Usare un indirizzo di posta elettronica valido per evitare che la posta elettronica di verifica venga contrassegnata come posta indesiderata.
* Aggiornare il valore del `personalizations.0.dynamic_template_data.subject` parametro di input della riga dell'oggetto con una riga dell'oggetto appropriata per l'organizzazione.

```xml
<ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Aggiungere la definizione del contenuto DataUri

Sotto le trasformazioni delle attestazioni all'interno `<BuildingBlocks>` di, aggiungere il [ContentDefinition](contentdefinitions.md) seguente per fare riferimento alla versione 2.0.0 URI dei dati:

```xml
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Creare un DisplayControl

Viene usato un controllo di visualizzazione verifica per verificare l'indirizzo di posta elettronica con un codice di verifica inviato all'utente.

Questo controllo di visualizzazione di esempio è configurato per:

1. Raccogliere il `email` tipo di attestazione Address dall'utente.
1. Attendere che l'utente fornisca il `verificationCode` tipo di attestazione con il codice inviato all'utente.
1. Tornare al `email` profilo tecnico autocertificato che contiene un riferimento a questo controllo di visualizzazione.
1. Utilizzando l' `SendCode` azione, generare un codice OTP e inviare all'utente un messaggio di posta elettronica con il codice OTP.

![Invia l'azione di posta elettronica del codice di verifica](media/custom-email-sendgrid/display-control-verification-email-action-01.png)

In definizioni contenuto, ancora in `<BuildingBlocks>` , aggiungere il seguente [DisplayControl](display-controls.md) di tipo [VerificationControl](display-control-verification.md) al criterio.

```xml
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Aggiungere profili tecnici OTP

Il `GenerateOtp` profilo tecnico genera un codice per l'indirizzo di posta elettronica. Il `VerifyOtp` profilo tecnico verifica il codice associato all'indirizzo di posta elettronica. È possibile modificare la configurazione del formato e la scadenza della password monouso. Per altre informazioni sui profili tecnici OTP, vedere [definire un profilo tecnico](one-time-password-technical-profile.md)monouso per la password.

Aggiungere i seguenti profili tecnici all' `<ClaimsProviders>` elemento.

```xml
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Aggiungere un profilo tecnico dell'API REST

Questo profilo tecnico dell'API REST genera il contenuto di posta elettronica (usando il formato SendGrid). Per altre informazioni sui profili tecnici RESTful, vedere [definire un profilo tecnico RESTful](restful-technical-profile.md).

Come per i profili tecnici OTP, aggiungere i profili tecnici seguenti all' `<ClaimsProviders>` elemento.

```xml
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendOtp">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Creare un riferimento a DisplayControl

Nel passaggio finale aggiungere un riferimento al DisplayControl creato. Sostituire `LocalAccountSignUpWithLogonEmail` i profili tecnici esistenti e autocertificati `LocalAccountDiscoveryUsingEmailAddress` con il codice seguente. Se è stata usata una versione precedente di Azure AD B2C criterio. Questi profili tecnici usano `DisplayClaims` con un riferimento a DisplayControl.

Per altre informazioni, vedere [profilo tecnico autocertificato](restful-technical-profile.md) e [DisplayControl](display-controls.md).

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>Opzionale Localizzare il messaggio di posta elettronica

Per localizzare il messaggio di posta elettronica, è necessario inviare le stringhe localizzate a SendGrid o al provider di posta elettronica. Ad esempio, è possibile localizzare l'oggetto, il corpo, il messaggio del codice o la firma del messaggio di posta elettronica. A tale scopo, è possibile usare la trasformazione delle attestazioni [GetLocalizedStringsTransformation](string-transformations.md) per copiare le stringhe localizzate in tipi di attestazione. La `GenerateEmailRequestBody` trasformazione delle attestazioni, che genera il payload JSON, USA le attestazioni di input che contengono le stringhe localizzate.

1. Nel criterio definire le seguenti attestazioni di stringa: oggetto, messaggio, codeintro e firma.
1. Definire una trasformazione delle attestazioni [GetLocalizedStringsTransformation](string-transformations.md) per sostituire i valori di stringa localizzata nelle attestazioni del passaggio 1.
1. Modificare la `GenerateEmailRequestBody` trasformazione delle attestazioni per utilizzare le attestazioni di input con il frammento di codice XML seguente.
1. Aggiornare il modello di SendGrid per usare i parametri dinamici al posto di tutte le stringhe che verranno localizzate da Azure AD B2C.

    ```xml
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Aggiungere l'elemento di [localizzazione](localization.md) seguente.

    ```xml
    <Localization Enabled="true">
      <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
        <SupportedLanguage>en</SupportedLanguage>
        <SupportedLanguage>es</SupportedLanguage>
      </SupportedLanguages>
      <LocalizedResources Id="api.custom-email.en">
        <LocalizedStrings>
          <!--Email template parameters-->
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
        </LocalizedStrings>
      </LocalizedResources>
      <LocalizedResources Id="api.custom-email.es">
        <LocalizedStrings>
          <!--Email template parameters-->
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
        </LocalizedStrings>
      </LocalizedResources>
    </Localization>
    ```

1. Aggiungere riferimenti agli elementi LocalizedResources aggiornando l'elemento [ContentDefinitions](contentdefinitions.md) .

    ```XML
    <ContentDefinitions>
      <ContentDefinition Id="api.localaccountsignup">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
        <LocalizedResourcesReferences MergeBehavior="Prepend">
          <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
          <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
        </LocalizedResourcesReferences>
      </ContentDefinition>
      <ContentDefinition Id="api.localaccountpasswordreset">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
        <LocalizedResourcesReferences MergeBehavior="Prepend">
          <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
          <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
        </LocalizedResourcesReferences>
      </ContentDefinition>
    </ContentDefinitions>
    ```

1. Infine, aggiungere la trasformazione delle attestazioni di input seguente ai `LocalAccountSignUpWithLogonEmail` `LocalAccountDiscoveryUsingEmailAddress` profili tecnici e.

    ```XML
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare un esempio di criteri di verifica della posta elettronica personalizzati in GitHub:

- [Verifica della posta elettronica personalizzata-DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Per informazioni sull'uso di un'API REST personalizzata o di qualsiasi provider di posta elettronica SMTP basato su HTTP, vedere [definire un profilo tecnico RESTful in un Azure ad B2C criteri personalizzati](restful-technical-profile.md).
