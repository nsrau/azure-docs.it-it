---
title: Verifiche email personalizzate
titleSuffix: Azure AD B2C
description: Informazioni su come personalizzare la posta elettronica di verifica inviata ai clienti quando si iscrivono per usare le applicazioni abilitate per Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6cc0508a63f26b955ac5e0ebf3ef58a184a35997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671641"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Verifica della posta elettronica personalizzata in Azure Active Directory B2CCustom email verification in Azure Active Directory B2C

Usare la posta elettronica personalizzata in Azure Active Directory B2C (Azure AD B2C) per inviare posta elettronica personalizzata agli utenti che si iscrivono per usare le applicazioni. Utilizzando [DisplayControls](display-controls.md) (attualmente in anteprima) e un provider di posta elettronica di terze parti, è possibile utilizzare il proprio modello di posta elettronica e *Da:* indirizzo e oggetto, nonché supportare la localizzazione e le impostazioni personalizzate della password monouso (OTP).

La verifica personalizzata della posta elettronica richiede l'utilizzo di un provider di posta elettronica di terze parti, ad esempio [SendGrid](https://sendgrid.com) o [SparkPost](https://sparkpost.com), un'API REST personalizzata o qualsiasi provider di posta elettronica basato su HTTP (incluso il proprio). In questo articolo viene descritta la configurazione di una soluzione che utilizza SendGrid.This article describes setting up a solution that uses SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Creare un account SendGrid

Se non ne hai già uno, inizia configurando un account SendGrid (i clienti di Azure possono sbloccare 25.000 email gratuite ogni mese). Per istruzioni sull'installazione, vedere la sezione [Creare un account SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) in [Come inviare messaggi di posta elettronica usando SendGrid con Azure.](../sendgrid-dotnet-how-to-send-email.md)

Assicurarsi di completare la sezione in cui si [crea una chiave API SendGrid](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Registrare la chiave API da usare in un passaggio successivo.

## <a name="create-azure-ad-b2c-policy-key"></a>Creare la chiave dei criteri B2C di Azure ADCreate Azure AD B2C policy key

Archiviare quindi la chiave API SendGrid in una chiave dei criteri B2C di Azure AD per fare riferimento ai criteri.

1. Accedere al [portale](https://portal.azure.com/)di Azure .
1. Assicurarsi di usare la directory che contiene il tenant B2C di Azure AD. Nel menu **superiore,** selezionare il filtro Directory e sottoscrizione e scegliere la directory B2C di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `SendGridSecret`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
1. In **Segreto** immettere il segreto client registrato in precedenza.
1. In **Uso chiave** selezionare `Signature`.
1. Selezionare **Crea**.

## <a name="create-sendgrid-template"></a>Crea modello SendGrid

Con un account SendGrid creato e una chiave API SendGrid archiviata in una chiave dei criteri B2C di Azure AD, creare un [modello transazionale dinamico](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)SendGrid.

1. Nel sito SendGrid aprire la pagina [dei modelli transazionali](https://sendgrid.com/dynamic_templates) e selezionare **Crea modello**.
1. Immettere un nome `Verification email` di modello univoco, quindi selezionare **Salva**.
1. Per iniziare a modificare il nuovo modello, selezionare **Aggiungi versione**.
1. Selezionare **Editor di codice** e quindi **Continua**.
1. Nell'editor HTML, incolla il seguente modello HTML o usa il tuo. I `{{otp}}` `{{email}}` parametri e verranno sostituiti dinamicamente con il valore della password mono(e l'indirizzo e-mail dell'utente).

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
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

1. Espandere **Impostazioni** a sinistra e, per `{{subject}}`Oggetto **e-mail,** immettere .
1. Selezionare **Salva modello**.
1. Tornare alla pagina **Modelli transazionali** selezionando la freccia indietro.
1. Registrare **l'ID** del modello creato per l'utilizzo in un passaggio successivo. Ad esempio: `d-989077fbba9746e89f3f6411f596fb96`. Questo ID viene specificato quando si [aggiunge la trasformazione delle attestazioni.](#add-the-claims-transformation)

## <a name="add-azure-ad-b2c-claim-types"></a>Aggiungere i tipi di attestazione Azure AD B2CAdd Azure AD B2C claim types

Nei criteri aggiungere i tipi di `<ClaimsSchema>` attestazione seguenti all'elemento all'interno di `<BuildingBlocks>`.

Questi tipi di attestazioni sono necessari per generare e verificare l'indirizzo di posta elettronica utilizzando un codice OTP (One-Time Password).

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
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

## <a name="add-the-claims-transformation"></a>Aggiungere la trasformazione delle attestazioniAdd the claims transformation

Successivamente, è necessaria una trasformazione delle attestazioni per generare un'attestazione di stringa JSON che sarà il corpo della richiesta inviata a SendGrid.Next, you need a claims transformation to output a JSON string claim that will be the body of the request sent to SendGrid.

La struttura dell'oggetto JSON è definita dagli ID in notazione del punto di InputParameters e TransformationClaimTypes di InputClaims. I numeri nella notazione del punto implicano matrici. I valori provengono dai valori di InputClaims e dalle proprietà "Value" di InputParameters. Per altre informazioni sulle trasformazioni delle attestazioni JSON, vedere [Trasformazioni delle attestazioni JSON.](json-transformations.md)

Aggiungere la trasformazione `<ClaimsTransformations>` delle `<BuildingBlocks>`attestazioni seguente all'elemento all'interno di . Apportare gli aggiornamenti seguenti all'XML di trasformazione delle attestazioni:Make the following updates to the claims transformation XML:

* Aggiornare `template_id` il valore InputParameter con l'ID del modello transazionale SendGrid creato in precedenza in [Crea modello SendGrid](#create-sendgrid-template).
* Aggiornare `from.email` il valore dell'indirizzo. Utilizza un indirizzo email valido per evitare che l'email di verifica venga contrassegnata come spam.
* Aggiornare il `personalizations.0.dynamic_template_data.subject` valore del parametro di input della riga dell'oggetto con una riga dell'oggetto appropriata per l'organizzazione.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
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
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Aggiungere la definizione di contenuto DataUriAdd DataUri content definition

Sotto le trasformazioni `<BuildingBlocks>`delle attestazioni all'interno di , aggiungere la [contentDefinition](contentdefinitions.md) seguente per fare riferimento all'URI dei dati della versione 2.0.0:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Creare un DisplayControlCreate a DisplayControl

Un controllo di visualizzazione della verifica viene utilizzato per verificare l'indirizzo di posta elettronica con un codice di verifica inviato all'utente.

Questo esempio di controllo di visualizzazione è configurato per:This example display control is configured to:

1. Raccogliere `email` il tipo di attestazione indirizzo dall'utente.
1. Attendere che l'utente `verificationCode` fornisca il tipo di attestazione con il codice inviato all'utente.
1. Riportare `email` la ritorna al profilo tecnico auto-asserito che ha un riferimento a questo controllo di visualizzazione.
1. Utilizzando `SendCode` l'azione, generare un codice OTP e inviare un messaggio di posta elettronica con il codice OTP all'utente.

![Invia il codice di verifica e-mail azione](media/custom-email/display-control-verification-email-action-01.png)

In definizioni di `<BuildingBlocks>`contenuto, ancora all'interno di , aggiungere il seguente [DisplayControl](display-controls.md) di tipo [VerificationControl](display-control-verification.md) ai criteri.

```XML
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
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
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

Il `GenerateOtp` profilo tecnico genera un codice per l'indirizzo di posta elettronica. Il `VerifyOtp` profilo tecnico verifica il codice associato all'indirizzo di posta elettronica. È possibile modificare la configurazione del formato e la scadenza della password mono(abitrare. Per ulteriori informazioni sui profili tecnici OTP, vedere Definire un profilo tecnico per [password monoorarie.](one-time-password-technical-profile.md)

Aggiungere i seguenti profili `<ClaimsProviders>` tecnici all'elemento.

```XML
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

## <a name="add-a-rest-api-technical-profile"></a>Aggiungere un profilo tecnico dell'API RESTAdd a REST API technical profile

Questo profilo tecnico dell'API REST genera il contenuto dell'e-mail (utilizzando il formato SendGrid). Per ulteriori informazioni sui profili tecnici RESTful, vedere [Definire un profilo tecnico RESTful](restful-technical-profile.md).

Come per i profili tecnici OTP, aggiungere `<ClaimsProviders>` i seguenti profili tecnici all'elemento.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Creare un riferimento al DisplayControl

Nel passaggio finale, aggiungere un riferimento al DisplayControl creato. Sostituire il `LocalAccountSignUpWithLogonEmail` profilo tecnico auto-asserito esistente con quanto segue se è stata usata una versione precedente dei criteri B2C di Azure AD. Questo profilo `DisplayClaims` tecnico utilizza con un riferimento al DisplayControl.This technical profile uses with a reference to the DisplayControl.

Per ulteriori informazioni, vedere [Profilo tecnico con asserazione automatica](restful-technical-profile.md) e [DisplayControl](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
        
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[Facoltativo] Localizzare la posta elettronica

Per localizzare il messaggio di posta elettronica, è necessario inviare stringhe localizzate a SendGrid o al provider di posta elettronica. Ad esempio, per localizzare l'oggetto del messaggio di posta elettronica, il corpo, il messaggio di codice o la firma del messaggio di posta elettronica. A tale scopo, è possibile utilizzare la trasformazione attestazioni [GetLocalizedStringsTransformation](string-transformations.md) per copiare le stringhe localizzate nei tipi di attestazione. Nella `GenerateSendGridRequestBody` trasformazione delle attestazioni, che genera il payload JSON, usa le attestazioni di input che contengono le stringhe localizzate.

1. Nei criteri definire le attestazioni stringa seguenti: subject, message, codeIntro e signature.
1. Definire una trasformazione delle attestazioni GetLocalizedStringsTransformation per sostituire i valori stringa localizzati nelle attestazioni del passaggio 1.Define a [GetLocalizedStringsTransformation](string-transformations.md) claims transformation to substitute localized string values into the claims from step 1.
1. Modificare `GenerateSendGridRequestBody` la trasformazione delle attestazioni in modo da usare le attestazioni di input con il frammento XML seguente.
1. Aggiornare il modello SendGrind per usare i parametri dinamici al posto di tutte le stringhe che verranno localizzate da Azure AD B2C.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
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
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare un esempio di criteri di verifica della posta elettronica personalizzati in GitHub:You can find an example of a custom email verification policy on GitHub:

[Verifica e-mail personalizzata - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Per informazioni sull'utilizzo di un'API REST personalizzata o di qualsiasi provider di posta elettronica SMTP basato su HTTP, vedere [Definire un profilo tecnico RESTful in un criterio personalizzato B2C](restful-technical-profile.md)di Azure AD.
