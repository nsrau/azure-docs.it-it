---
title: Modelli di pagina in Gestione API di Azure | Microsoft Docs
description: Informazioni su come personalizzare il contenuto delle pagine del portale per sviluppatori usando un set di modelli in Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: aca44e14ab85fcfeb9d1eb3c3eadfff7831c372f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="page-templates-in-azure-api-management"></a>Modelli di pagina in Gestione API di Azure
In Gestione API di Azure è possibile personalizzare le pagine del portale per sviluppatori usando un set di modelli che ne configurano il contenuto. La sintassi [DotLiquid](http://dotliquidmarkup.org/) usata insieme all'editor di propria scelta, ad esempio [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), e a un set di [risorse stringa](api-management-template-resources.md#strings) localizzate, [risorse Glifo](api-management-template-resources.md#glyphs) e [controlli di pagina](api-management-page-controls.md) offre una grande flessibilità nella configurazione personalizzata del contenuto delle pagine attraverso questi modelli.  
  
 I modelli in questa sezione consentono di personalizzare il contenuto delle pagine di accesso, di iscrizione e Pagina non trovata del portale per sviluppatori.  
  
-   [Accesso](#SignIn)  
  
-   [Iscrizione](#SignUp)  
  
-   [Pagina non trovata](#PageNotFound)  
  
> [!NOTE]
>  La documentazione seguente include alcuni modelli predefiniti di esempio. A causa dei continui miglioramenti che vengono apportati, questi modelli sono però soggetti a modifiche. È possibile visualizzare i modelli predefiniti direttamente nel portale per sviluppatori accedendo ai singoli modelli desiderati. Per altre informazioni sull'uso dei modelli, vedere [Come personalizzare il portale per sviluppatori di Gestione API di Azure con i modelli](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
##  <a name="SignIn"></a> Accesso  
 Il modello di **accesso** consente di personalizzare la pagina di accesso del portale per sviluppatori.  
  
 ![Pagina di accesso](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "Modelli della pagina di accesso nel portale per sviluppatori di Gestione API")  
  
### <a name="default-template"></a>Modello predefinito  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>Controlli  
 Questo modello può usare i [controlli di pagina](api-management-page-controls.md) seguenti.  
  
-   [basic-signin](api-management-page-controls.md#basic-signin)  
  
-   [provider](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Modello di dati  
 Entità [Accesso utente](api-management-template-data-model-reference.md#UseSignIn).  
  
### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "Email": null,  
    "Password": null,  
    "ReturnUrl": null,  
    "RememberMe": false,  
    "RegistrationEnabled": true,  
    "DelegationEnabled": false,  
    "DelegationUrl": null,  
    "SsoSignUpUrl": null,  
    "AuxServiceUrl": "https://manage.windowsazure.com/#Workspaces/ApiManagementExtension/service/contoso5/dashboard",  
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
    ],  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsEnabled": false  
}  
```  
  
##  <a name="SignUp"></a> Iscrizione  
 Il modello di **iscrizione** consente di personalizzare la pagina di iscrizione del portale per sviluppatori.  
  
 ![Pagina di iscrizione](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "Modelli della pagina di iscrizione nel portale per sviluppatori di Gestione API")  
  
### <a name="default-template"></a>Modello predefinito  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Controlli  
 Questo modello può usare i [controlli di pagina](api-management-page-controls.md) seguenti.  
  
-   [sign-up](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Modello di dati  
 Entità [Iscrizione utente](api-management-template-data-model-reference.md#UserSignUp).  
  
### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="PageNotFound"></a> Pagina non trovata  
 Il modello **Pagina non trovata** consente di personalizzare la pagina Pagina non trovata del portale per sviluppatori.  
  
 ![Pagina non trovata](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "Modelli di Pagina non trovata nel portale per sviluppatori di Gestione API")  
  
### <a name="default-template"></a>Modello predefinito  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>Controlli  
 Questo modello potrebbe non usare i [controlli di pagina](api-management-page-controls.md).  
  
### <a name="data-model"></a>Modello di dati  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|referenceCode|string|Codice generato se questa pagina viene visualizzata in seguito a un errore interno.|  
|errorCode|string|Codice generato se questa pagina viene visualizzata in seguito a un errore interno.|  
|emailBody|string|Corpo del messaggio di posta elettronica generato se questa pagina viene visualizzata in seguito a un errore interno.|  
|requestedUrl|string|URL richiesto quando la pagina non è stata trovata.|  
|referrerUrl|string|URL del referrer all'URL richiesto.|  
  
### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sull'uso dei modelli, vedere [Come personalizzare il portale per sviluppatori di Gestione API usando i modelli](api-management-developer-portal-templates.md).