---
title: Verifica della posta elettronica personalizzata
titleSuffix: Azure AD B2C
description: Informazioni su come personalizzare il messaggio di posta elettronica di verifica inviato ai clienti quando si iscrivono per l'uso delle applicazioni abilitate per Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3d9316f42c8d0ac5b44cda2e484ca4c92110813d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479150"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Verifica della posta elettronica personalizzata in Azure Active Directory B2C

Usare un indirizzo di posta elettronica personalizzato in Azure Active Directory B2C (Azure AD B2C) per inviare messaggi di posta elettronica personalizzati agli utenti che si iscrivono per l'uso delle applicazioni. Usando [DisplayControls](display-controls.md) (attualmente disponibile in anteprima) e un provider di posta elettronica di terze parti, è possibile usare il proprio modello di posta elettronica e *da:* indirizzo e oggetto, nonché la localizzazione del supporto e le impostazioni della password monouso (OTP) personalizzate.

Per la verifica tramite posta elettronica personalizzata è necessario usare un provider di posta elettronica di terze parti, ad esempio [SendGrid](https://sendgrid.com) o [SPARKPOST](https://sparkpost.com), un'API REST personalizzata o qualsiasi provider di posta elettronica basato su http (incluso il proprio). Questo articolo descrive la configurazione di una soluzione che usa SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Creare un account SendGrid

Se non si ha già un account, iniziare con la configurazione di un account SendGrid (i clienti di Azure possono sbloccare 25.000 messaggi di posta elettronica gratuiti al mese). Per le istruzioni di installazione, vedere la sezione [creare un account SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) di [come inviare messaggi di posta elettronica usando SendGrid con Azure](../sendgrid-dotnet-how-to-send-email.md).

Assicurarsi di completare la sezione in cui si [Crea una chiave API SendGrid](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Registrare la chiave API da usare in un passaggio successivo.

## <a name="create-azure-ad-b2c-policy-key"></a>Crea chiave dei criteri di Azure AD B2C

Quindi, archiviare la chiave API SendGrid in una chiave dei criteri di Azure AD B2C per fare riferimento ai criteri.

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant del Azure AD B2C. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e scegliere la directory Azure ad B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `SendGridSecret`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
1. In **Segreto** immettere il segreto client registrato in precedenza.
1. In **Uso chiave** selezionare `Signature`.
1. Selezionare **Create** (Crea).

## <a name="create-sendgrid-template"></a>Crea modello SendGrid

Con un account SendGrid creato e una chiave API SendGrid archiviata in una chiave dei criteri di Azure AD B2C, creare un [modello transazionale SendGrid dinamico](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/).

1. Nel sito di SendGrid aprire la pagina [modelli transazionali](https://sendgrid.com/dynamic_templates) e selezionare **Crea modello**.
1. Immettere un nome di modello univoco, ad esempio `Verification email`, quindi selezionare **Salva**.
1. Per iniziare a modificare il nuovo modello, selezionare **Aggiungi versione**.
1. Selezionare **Editor del codice** e **continuare**.
1. Nell'editor HTML incollare il modello HTML seguente o usare il proprio. I parametri `{{otp}}` e `{{email}}` verranno sostituiti in modo dinamico con il valore della password monouso e con l'indirizzo di posta elettronica dell'utente.

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

1. Espandere **Impostazioni** a sinistra e per **oggetto posta elettronica**immettere `{{subject}}`.
1. Selezionare **Salva modello**.
1. Tornare alla pagina **modelli transazionali** selezionando la freccia indietro.
1. Registrare l' **ID** del modello creato per l'uso in un passaggio successivo. Ad esempio: `d-989077fbba9746e89f3f6411f596fb96`. Questo ID viene specificato quando si [aggiunge la trasformazione delle attestazioni](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Aggiungere Azure AD B2C tipi di attestazione

Nel criterio aggiungere i tipi di attestazione seguenti all'elemento `<ClaimsSchema>` in `<BuildingBlocks>`.

Questi tipi di attestazioni sono necessari per generare e verificare l'indirizzo di posta elettronica usando un codice OTP (One-time password).

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

## <a name="add-the-claims-transformation"></a>Aggiungere la trasformazione delle attestazioni

Quindi, è necessaria una trasformazione delle attestazioni per restituire un'attestazione stringa JSON che costituirà il corpo della richiesta inviata a SendGrid.

La struttura dell'oggetto JSON è definita dagli ID nella notazione del punto di InputParameters e TransformationClaimTypes di InputClaims. I numeri nella notazione del punto implicano matrici. I valori provengono dalle proprietà InputClaims ' Values e InputParameters ' "value". Per ulteriori informazioni sulle trasformazioni delle attestazioni JSON, vedere le [trasformazioni delle attestazioni JSON](json-transformations.md).

Aggiungere la trasformazione delle attestazioni seguente all'elemento `<ClaimsTransformations>` in `<BuildingBlocks>`. Apportare gli aggiornamenti seguenti all'XML di trasformazione delle attestazioni:

* Aggiornare il valore di `template_id` InputParameter con l'ID del modello transazionale SendGrid creato in precedenza in [create SendGrid template](#create-sendgrid-template).
* Aggiornare il valore dell'indirizzo `from.email`. Usare un indirizzo di posta elettronica valido per evitare che la posta elettronica di verifica venga contrassegnata come posta indesiderata.
* Aggiornare il valore del parametro di input della riga di `personalizations.0.dynamic_template_data.subject` oggetto con una riga dell'oggetto appropriata per l'organizzazione.

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

## <a name="add-datauri-content-definition"></a>Aggiungere la definizione del contenuto DataUri

Sotto le trasformazioni delle attestazioni all'interno `<BuildingBlocks>`aggiungere il [ContentDefinition](contentdefinitions.md) seguente per fare riferimento alla versione 2.0.0 URI dei dati:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Creare un DisplayControl

Viene usato un controllo di visualizzazione verifica per verificare l'indirizzo di posta elettronica con un codice di verifica inviato all'utente.

Questo controllo di visualizzazione di esempio è configurato per:

1. Raccogliere il tipo di attestazione indirizzo `email` dall'utente.
1. Attendere che l'utente fornisca il tipo di attestazione `verificationCode` con il codice inviato all'utente.
1. Restituisce il `email` al profilo tecnico autocertificato che contiene un riferimento a questo controllo di visualizzazione.
1. Utilizzando l'azione `SendCode`, generare un codice OTP e inviare all'utente un messaggio di posta elettronica con il codice OTP.

![Invia l'azione di posta elettronica del codice di verifica](media/custom-email/display-control-verification-email-action-01.png)

In definizioni contenuto, sempre in `<BuildingBlocks>`, aggiungere il seguente [DisplayControl](display-controls.md) di tipo [VerificationControl](display-control-verification.md) al criterio.

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

Il profilo tecnico `GenerateOtp` genera un codice per l'indirizzo di posta elettronica. Il profilo tecnico `VerifyOtp` verifica il codice associato all'indirizzo di posta elettronica. È possibile modificare la configurazione del formato e la scadenza della password monouso. Per altre informazioni sui profili tecnici OTP, vedere [definire un profilo tecnico](one-time-password-technical-profile.md)monouso per la password.

Aggiungere i seguenti profili tecnici all'elemento `<ClaimsProviders>`.

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
        <Item Key="UserMessage.VerificationHasExpired">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessage.MaxRetryAttemped">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessage.InvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessage.ServerError">Cannot verify the code, please try again later.</Item>
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

Come per i profili tecnici OTP, aggiungere i seguenti profili tecnici all'elemento `<ClaimsProviders>`.

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

## <a name="make-a-reference-to-the-displaycontrol"></a>Creare un riferimento a DisplayControl

Nel passaggio finale aggiungere un riferimento al DisplayControl creato. Se è stata usata una versione precedente dei criteri di Azure AD B2C, sostituire il profilo tecnico esistente `LocalAccountSignUpWithLogonEmail` autocertificato con il codice seguente. Questo profilo tecnico USA `DisplayClaims` con un riferimento a DisplayControl.

Per altre informazioni, vedere [profilo tecnico autocertificato](restful-technical-profile.md) e [DisplayControl](display-controls.md).

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

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare un esempio di criteri di verifica della posta elettronica personalizzati in GitHub:

[Verifica della posta elettronica personalizzata-DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Per informazioni sull'uso di un'API REST personalizzata o di qualsiasi provider di posta elettronica SMTP basato su HTTP, vedere [definire un profilo tecnico RESTful in un Azure ad B2C criteri personalizzati](restful-technical-profile.md).
