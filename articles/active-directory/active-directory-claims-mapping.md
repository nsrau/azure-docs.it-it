---
title: Mapping delle attestazioni in Azure Active Directory (anteprima pubblica) | Microsoft Docs
description: Questa pagina descrive il mapping di attestazioni di Azure Active Directory.
services: active-directory
author: billmath
manager: femila
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: dc542483a8894e31bd118ac60d248dc8ee590885
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017


---

# <a name="claims-mapping-in-azure-active-directory-public-preview"></a>Mapping delle attestazioni in Azure Active Directory (anteprima pubblica)

>[!NOTE]
>Questa funzionalità sostituisce e prevale sulla personalizzazione delle attestazioni attualmente offerta tramite il portale (come documentato [qui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)). Se si personalizzano le attestazioni tramite il portale in aggiunta al metodo Graph/PowerShell descritto in dettaglio in questo documento nella stessa applicazione, i token emessi per l'applicazione ignoreranno la configurazione nel portale.
Le configurazioni eseguite tramite i metodi descritti in dettaglio in questo documento non si rifletteranno nel portale.

Questa funzionalità viene usata dagli amministratori tenant per personalizzare le attestazioni generate nei token per un'applicazione specifica nel tenant. I criteri di mapping delle attestazioni possono essere usati per:

- Selezionare le attestazioni che verranno incluse nei token.
- Creare tipi di attestazione non esistenti.
- Scegliere o modificare l'origine dei dati generati in attestazioni specifiche.

>[!NOTE]
>Questa funzionalità è attualmente disponibile in anteprima pubblica. Potrebbe essere necessario ripristinare o rimuovere eventuali modifiche. La funzionalità è disponibile in tutte le sottoscrizioni di Azure Active Directory durante l'anteprima pubblica. Tuttavia, quando la funzionalità sarà disponibile a livello generale, alcuni aspetti potrebbero richiedere una sottoscrizione di Azure Active Directory Premium.

## <a name="claims-mapping-policy-type"></a>Tipo di criteri di mapping di attestazioni
In Azure AD un oggetto criteri rappresenta un set di regole definito per singole applicazioni o tutte le applicazioni in un'organizzazione. Ogni tipo di criteri ha una struttura univoca con un set di proprietà che verranno in seguito applicate agli oggetti a cui sono assegnate.

I criteri di mapping delle attestazioni sono un tipo di oggetto criteri che modifica le attestazioni generate nei token emessi per applicazioni specifiche.

## <a name="claim-sets"></a>Set di attestazioni
Alcuni set di attestazioni definiscono come e quando vengono utilizzate nei token.

## <a name="core-claim-set"></a>Set di attestazioni core
Saranno presenti attestazioni nel set di attestazioni core in ogni token, indipendentemente dai criteri. Queste attestazioni vengono inoltre considerate limitate e non possono essere modificate.

## <a name="basic-claim-set"></a>Set di attestazioni di base
Il set di attestazioni di base include le attestazioni generate per impostazione predefinita per i token (oltre al set di attestazioni core). Queste attestazioni possono essere omesse o modificate mediante i criteri di mapping delle attestazioni.

## <a name="restricted-claim-set"></a>Set di attestazioni con restrizioni
Le attestazioni con restrizioni non possono essere modificate mediante criteri. L'origine dati non può essere modificata e non verrà applicata alcuna trasformazione durante la generazione di queste attestazioni.

### <a name="table-1---jwt-restricted-claim-set"></a>Tabella 1 - Set di attestazioni con restrizioni JWT
|Tipo di attestazione (nome)|
| ----- |
|_claim_names|
|_claim_sources|
|access_token|
|account_type|
|acr|
|actor|
|actortoken|
|aio|
|altsecid|
|amr|
|app_chain|
|app_displayname|
|app_res|
|appctx|
|appctxsender|
|appid|
|appidacr|
|assertion|
|at_hash|
|aud|
|auth_data|
|auth_time|
|authorization_code|
|azp|
|azpacr|
|c_hash|
|ca_enf|
|cc|
|cert_token_use|
|client_id|
|cloud_graph_host_name|
|cloud_instance_name|
|cnf|
|code|
|controls|
|credential_keys|
|csr|
|csr_type|
|deviceid|
|dns_names|
|domain_dns_name|
|domain_netbios_name|
|e_exp|
|email|
|endpoint|
|enfpolids|
|exp|
|expires_on|
|grant_type|
|graph|
|group_sids|
|groups|
|hasgroups|
|hash_alg|
|home_oid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expired|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier|
|iat|
|identityprovider|
|idp|
|in_corp|
|instance|
|ipaddr|
|isbrowserhostedapp|
|iss|
|jwk|
|key_id|
|key_type|
|mam_compliance_url|
|mam_enrollment_url|
|mam_terms_of_use_url|
|mdm_compliance_url|
|mdm_enrollment_url|
|mdm_terms_of_use_url|
|nameid|
|nbf|
|netbios_name|
|nonce|
|oid|
|on_prem_id|
|onprem_sam_account_name|
|onprem_sid|
|openid2_id|
|password|
|platf|
|polids|
|pop_jwk|
|preferred_username|
|previous_refresh_token|
|primary_sid|
|puid|
|pwd_exp|
|pwd_url|
|redirect_uri|
|refresh_token|
|refreshtoken|
|request_nonce|
|resource|
|role|
|roles|
|scope|
|scp|
|sid|
|signature|
|signin_state|
|src1|
|src2|
|sub|
|tbid|
|tenant_display_name|
|tenant_region_scope|
|thumbnail_photo|
|tid|
|tokenAutologonEnabled|
|trustedfordelegation|
|unique_name|
|upn|
|user_setting_sync_url|
|username|
|uti|
|ver|
|verified_primary_email|
|verified_secondary_email|
|wids|
|win_ver|

### <a name="table-2---saml-restricted-claim-set"></a>Tabella 2 - Set di attestazioni con restrizioni SAML
|Tipo di attestazione (URI)|
| ----- |
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expired|
|http://schemas.microsoft.com/identity/claims/accesstoken|
|http://schemas.microsoft.com/identity/claims/openid2_id|
|http://schemas.microsoft.com/identity/claims/identityprovider|
|http://schemas.microsoft.com/identity/claims/objectidentifier|
|http://schemas.microsoft.com/identity/claims/puid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
|http://schemas.microsoft.com/identity/claims/tenantid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod|
|http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/groups|
|http://schemas.microsoft.com/claims/groups.link|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/role|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/wids|
|http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant|
|http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown|
|http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged|
|http://schemas.microsoft.com/2014/03/psso|
|http://schemas.microsoft.com/claims/authnmethodsreferences|
|http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier|
|http://schemas.microsoft.com/identity/claims/scope|

## <a name="claims-mapping-policy-properties"></a>Proprietà di criteri di mapping di attestazioni
Le proprietà dei criteri di mapping delle attestazioni consentono di controllare le attestazioni generate e le origini dei dati. Se non sono impostati criteri, il sistema emette token contenenti il set di attestazioni core, il set di attestazioni di base ed eventuali attestazioni facoltative che l'applicazione abbia scelto di ricevere.

### <a name="include-basic-claim-set"></a>Includere set di attestazioni di base

Panoramica della proprietà

**Stringa:** IncludeBasicClaimSet

**Tipo di dati:** booleano (True o False)

**Riepilogo:** questa proprietà specifica se il set di attestazioni di base sia incluso nei token interessati da questo criterio. 

- Se impostata su True, tutte le attestazioni nel set di attestazioni di base verranno generate nei token interessati dal criterio. 
- Se impostata su False, le attestazioni nel set di attestazioni di base non verranno generate nei token, a meno che non vengano aggiunte singolarmente nella proprietà dello schema di attestazioni dello stesso criterio.

>[!NOTE] 
>Saranno presenti attestazioni nel set di attestazioni core in ogni token, indipendentemente dall'impostazione di questa proprietà. 

### <a name="claims-schema"></a>Schema di attestazioni
Panoramica della proprietà

**Stringa:** ClaimsSchema

**Tipo di dati:** BLOB JSON con una o più voci di schema di attestazioni (per il formato, vedere la sezione relativa ai criteri di mapping di attestazioni di esempio)

**Riepilogo:** questa proprietà consente di definire le attestazioni che saranno presenti nei token interessati dal criterio oltre al set di attestazioni di base (se IncludeBasicClaimSet è impostata su True) e il set di attestazioni core.
Per ogni voce di schema di attestazioni definita in questa proprietà, è necessario stabilire 2 informazioni: l'origine dei dati (Value o coppia Source/ID) e l'attestazione in base alla quale verranno generati i dati (tipo di attestazione).

### <a name="claim-schema-entry-elements"></a>Elementi di voci dello schema di attestazioni

**Value:** l'elemento Value definisce un valore statico come dati da generare nell'attestazione.

**Coppia Source/ID:** gli elementi Source e ID definiscono l'origine da cui verranno ricavati i dati nell'attestazione. 

L'elemento Source deve essere impostato su uno dei valori seguenti: 


- "user": i dati nell'attestazione sono una proprietà dell'oggetto User 
- "application": i dati nell'attestazione sono una proprietà dell'entità servizio application (client) 
- "resource": i dati nell'attestazione sono una proprietà dell'entità servizio resource
- "audience": i dati nell'attestazione sono una proprietà dell'entità servizio che corrisponde al destinatario del token (entità servizio resource o client)
- "company": i dati nell'attestazione sono una proprietà dell'oggetto Company del tenant di risorse
- "transformation": i dati nell'attestazione derivano dalla trasformazione delle attestazioni. Vedere la sezione Trasformazione delle attestazioni di seguito. 

Se l'origine è transformation, anche l'elemento TransformationID deve essere incluso in questa definizione di attestazione.

L'elemento ID identifica la proprietà dell'origine che indicherà il valore per l'attestazione. La tabella seguente elenca i valori di ID validi per ogni valore di Source.

### <a name="table-3---valid-id-values-per-source"></a>Tabella 3 - Valori di ID validi per ogni Source
|Sorgente|ID|Descrizione|
|-----|-----|-----|
|Utente|surname|Cognome|
|Utente|givenname|Nome|
|Utente|displayname|Nome visualizzato|
|Utente|objectId|ObjectID|
|Utente|mail|Indirizzo di posta elettronica|
|Utente|userprincipalname|Nome dell'entità utente|
|Utente|department|department|
|Utente|onpremisessamaccountname|Nome account SAM locale|
|Utente|netbiosname|Nome NetBios|
|Utente|dnsdomainname|Nome di dominio DNS|
|Utente|onpremisesecurityidentifier|Identificatore di sicurezza locale|
|Utente|companyname|Nome organizzazione|
|Utente|streetaddress|Indirizzo|
|Utente|postalcode|CAP|
|Utente|preferredlanguange|Lingua preferita|
|Utente|onpremisesuserprincipalname|UPN locale|
|Utente|mailNickname|Nome di posta elettronica alternativo|
|Utente|extensionattribute1|Attributo di estensione 1|
|Utente|extensionattribute2|Attributo di estensione 2|
|Utente|extensionattribute3|Attributo di estensione 3|
|Utente|extensionattribute4|Attributo di estensione 4|
|Utente|extensionattribute5|Attributo di estensione 5|
|Utente|extensionattribute6|Attributo di estensione 6|
|Utente|extensionattribute7|Attributo di estensione 7|
|Utente|extensionattribute8|Attributo di estensione 8|
|Utente|extensionattribute9|Attributo di estensione 9|
|Utente|extensionattribute10|Attributo di estensione 10|
|Utente|extensionattribute11|Attributo di estensione 11|
|Utente|extensionattribute12|Attributo di estensione 12|
|Utente|extensionattribute13|Attributo di estensione 13|
|Utente|extensionattribute14|Attributo di estensione 14|
|Utente|extensionattribute15|Attributo di estensione 15|
|Utente|othermail|Posta elettronica alternativa|
|Utente|country|Paese|
|Utente|city|city|
|Utente|state|Stato|
|Utente|jobtitle|Posizione|
|Utente|employeeid|ID dipendente|
|Utente|facsimiletelephonenumber|Numero di telefono fax|
|application, resource, audience|displayname|Nome visualizzato|
|application, resource, audience|objected|ObjectID|
|application, resource, audience|tags|Tag di entità servizio|
|Azienda|tenantcountry|Paese del tenant|

**TransformationID:** l'elemento TransformationID è obbligatorio solo se l'elemento Source è impostato su "transformation".

- Questo elemento deve corrispondere all'elemento ID della voce di trasformazione nella proprietà ClaimsTransformation che definisce la modalità di generazione dei dati per questa attestazione.

**Tipo di attestazione:** gli elementi JwtClaimType e SamlClaimType definiscono l'attestazione a cui questa voce dello schema di attestazioni fa riferimento.

- JwtClaimType deve contenere il nome dell'attestazione da generare in JWT.
- SamlClaimType deve contenere l'URI dell'attestazione da generare nei token SAML.

>[!NOTE]
>Non è possibile utilizzare i nomi e gli URI delle attestazioni nel set di attestazioni con restrizioni per gli elementi dei tipi di attestazioni. Vedere la sezione Eccezioni e limitazioni di seguito.

### <a name="claims-transformation"></a>Trasformazione delle attestazioni
Panoramica della proprietà

**Stringa:** ClaimsTransformation

**Tipo di dati:** BLOB JSON con una o più voci di trasformazione (per il formato, vedere la sezione relativa ai criteri di mapping di attestazioni di esempio)

**Riepilogo:** questa proprietà consente di applicare trasformazioni comuni ai dati di origine per generare i dati di output per le attestazioni specificate nello schema di attestazioni.
Elementi della voce di trasformazione

**ID:** l'elemento ID viene utilizzato per fare riferimento a questa voce di trasformazione in TransformationID in una voce dello schema di attestazioni. Questo valore deve essere univoco per ogni voce di trasformazione all'interno di questo criterio.

**TransformationMethod:** l'elemento TransformationMethod identifica l'operazione che verrà eseguita per generare i dati per l'attestazione.

In base al metodo scelto, è previsto un set di input e output. Questi vengono definiti mediante gli elementi InputClaims, InputParameters e OutputClaims.

### <a name="table-4---transformation-methods-and-expected-inputsoutputs"></a>Tabella 4 - Metodi di trasformazione e input/output previsti
|TransformationMethod|Input previsto|Output previsto|Descrizione|
|-----|-----|-----|-----|
|Join|string1, string2, separator|outputClaim|Aggiunge stringhe di input dividendole con un separatore. Es.: string1:"foo@bar.com" , string2:"sandbox", separator:"." genererà outputClaim:"foo@bar.com.sandbox"|
|ExtractMailPrefix|mail|outputClaim|Estrae la parte locale di un indirizzo di posta elettronica. Es.: mail:"foo@bar.com" genererà outputClaim:"foo". Se non è presente il simbolo "@", la stringa di input originale verrà restituita invariata.|

**InputClaims:** un elemento InputClaims può essere usato per passare i dati da una voce dello schema di attestazioni a una trasformazione. Include due attributi: ClaimTypeReferenceId e TransformationClaimType.

- ClaimTypeReferenceId viene aggiunto all'elemento ID della voce dello schema di attestazioni per individuare l'attestazione di input appropriata. 
- TransformationClaimType viene usato per assegnare un nome univoco a questo input. Questo nome deve corrispondere a uno degli input previsti per il metodo di trasformazione.

**InputParameters:** un elemento InputParameters viene usato per passare un valore costante a una trasformazione. Include due attributi: Value e ID.
Value è il valore costante effettivo da passare.
ID viene usato per assegnare un nome univoco a questo input. Questo nome deve corrispondere a uno degli input previsti per il metodo di trasformazione.

**OutputClaims:** un elemento OutputClaims viene usato per contenere i dati generati da una trasformazione e associarli a una voce dello schema di attestazioni. Include due attributi: ClaimTypeReferenceId e TransformationClaimType.

ClaimTypeReferenceId viene aggiunto all'elemento ID della voce dello schema di attestazioni per individuare l'attestazione di output appropriata. TransformationClaimType viene usato per assegnare un nome univoco a questo output. Questo nome deve corrispondere a uno degli output previsti per il metodo di trasformazione.

### <a name="exceptions-and-restrictions"></a>Eccezioni e restrizioni

**NameID e UPN di SAML:** gli attributi da cui hanno origine i valori NameID e UPN, e le trasformazioni di attestazioni consentite, presentano limitazioni.

### <a name="table-5---attributes-allowed-as-data-source-for-saml-nameid"></a>Tabella 5 - Attributi consentiti come origine dati per NameID di SAML
|Sorgente|ID|Descrizione|
|-----|-----|-----|
|Utente|mail|Indirizzo di posta elettronica|
|Utente|userprincipalname|Nome dell'entità utente|
|Utente|onpremisessamaccountname|Nome account SAM locale|
|Utente|employeeid|ID dipendente|
|Utente|extensionattribute1|Attributo di estensione 1|
|Utente|extensionattribute2|Attributo di estensione 2|
|Utente|extensionattribute3|Attributo di estensione 3|
|Utente|extensionattribute4|Attributo di estensione 4|
|Utente|extensionattribute5|Attributo di estensione 5|
|Utente|extensionattribute6|Attributo di estensione 6|
|Utente|extensionattribute7|Attributo di estensione 7|
|Utente|extensionattribute8|Attributo di estensione 8|
|Utente|extensionattribute9|Attributo di estensione 9|
|Utente|extensionattribute10|Attributo di estensione 10|
|Utente|extensionattribute11|Attributo di estensione 11|
|Utente|extensionattribute12|Attributo di estensione 12|
|Utente|extensionattribute13|Attributo di estensione 13|
|Utente|extensionattribute14|Attributo di estensione 14|
|Utente|extensionattribute15|Attributo di estensione 15|

### <a name="table-6---transformation-methods-allowed-for-saml-nameid"></a>Tabella 6 - Metodi di trasformazione consentiti per NameID di SAML
|TransformationMethod|Restrizioni|
| ----- | ----- |
|ExtractMailPrefix||Nessuna|
|Join|Il suffisso da aggiungere deve essere un dominio verificato del tenant delle risorse.|

### <a name="custom-signing-key"></a>Chiave di firma personalizzata
È necessario assegnare una chiave di firma personalizzata all'oggetto entità servizio per poter applicare criteri di mapping di attestazioni. Tutti i token emessi interessati dal criterio di accesso verranno firmati con questa chiave. Le applicazioni devono essere configurate per accettare i token firmati con questa chiave. In questo modo si conferma che i token sono stati modificati dall'autore del criterio di mapping delle attestazioni. Le applicazioni vengono protette dai criteri di mapping delle attestazioni creati da attori dannosi.

### <a name="cross-tenant-scenarios"></a>Scenari tra tenant
I criteri di mapping delle attestazioni non si applicano agli utenti guest. Se un utente guest tenta di accedere a un'applicazione con un criterio di mapping delle attestazioni assegnato all'entità servizio, verrà emesso il token predefinito (il criterio non avrà effetto).

## <a name="claims-mapping-policy-assignment"></a>Assegnazione di criteri di mapping delle attestazioni
I criteri di mapping delle attestazioni possono essere assegnati solo a oggetti entità servizio.

### <a name="example-claims-mapping-policies"></a>Criteri di mapping delle attestazioni di esempio

In molti scenari di Azure AD è possibile personalizzare le attestazioni generate nei token per specifiche entità servizio. In questa sezione vengono illustrati alcuni scenari comuni che permettono di comprendere come utilizzare il tipo di criteri di mapping delle attestazioni.

#### <a name="prerequisites"></a>Prerequisiti
Gli esempi seguenti mostrano come creare, aggiornare, collegare ed eliminare criteri per le entità servizio. Se non si ha familiarità con Azure AD, è consigliabile capire come ottenere un tenant di Azure AD prima di procedere con questi esempi. 

Per iniziare, seguire questa procedura:


1. Scaricare l'ultima [versione di anteprima del modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.127).
2.  Eseguire il comando Connect per accedere all'account amministratore di Azure AD. Eseguire questo comando ogni volta che si avvia una nuova sessione.
    
     ``` powershell
    Connect-AzureAD -Confirm
    
    ```
3.  Per visualizzare tutti i criteri creati nell'organizzazione, eseguire questo comando. È consigliabile eseguire questo comando dopo la maggior parte delle operazioni negli scenari seguenti per verificare che i criteri siano stati creati come previsto.
   
    ``` powershell
        Get-AzureADPolicy
    
    ```
#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Esempio: creare e assegnare un criterio per omettere le attestazioni di base dai token emessi per un'entità servizio.
In questo esempio si creerà un criterio che rimuove il set di attestazioni di base dai token emessi per le entità servizio collegate.


1. Creare un criterio di mapping delle attestazioni. Questo criterio, che verrà collegato a specifiche entità servizio, rimuove il set di attestazioni di base dai token.
    1. Per creare i criteri, eseguire questo comando: 
    
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims” -Type "ClaimsMappingPolicy"
    ```
    2. Per visualizzare il nuovo criterio e ottenere il relativo ObjectId, eseguire questo comando:
    
     ``` powershell
    Get-AzureADPolicy
    ```
2.  Assegnare i criteri all'entità servizio. È necessario ottenere anche l'ObjectId dell'entità servizio. 
    1.  Per visualizzare tutte le entità servizio dell'organizzazione, è possibile eseguire query in Microsoft Graph. In alternativa, eseguire l'accesso all'account Azure AD dallo strumento Graph Explorer di Azure AD.
    2.  Dopo aver ottenuto l'ObjectId dell'entità servizio, eseguire questo comando:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Esempio: creare e assegnare un criterio per includere EmployeeID e TenantCountry come attestazioni nei token emessi per un'entità servizio.
In questo esempio si creerà un criterio che aggiunge EmployeeID e TenantCountry ai token emessi per le entità servizio collegate. EmployeeID viene emesso come tipo di attestazione nome nei token e nei JWT SAML. TenantCountry viene emesso come tipo di attestazione paese nei token e nei JWT SAML. In questo esempio si sceglierà anche di continuare a includere il set di attestazioni di base nei token.

1. Creare un criterio di mapping delle attestazioni. Questo criterio, che verrà collegato a specifiche entità servizio, aggiunge le attestazioni EmployeeID e TenantCountry ai token.
    1. Per creare i criteri, eseguire questo comando:  
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":" tenantcountry ","SamlClaimType":" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country ","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample” -Type "ClaimsMappingPolicy"
    ```
    
    2. Per visualizzare il nuovo criterio e ottenere il relativo ObjectId, eseguire questo comando:
     
     ``` powershell  
    Get-AzureADPolicy
    ```
2.  Assegnare i criteri all'entità servizio. È necessario ottenere anche l'ObjectId dell'entità servizio. 
    1.  Per visualizzare tutte le entità servizio dell'organizzazione, è possibile eseguire query in Microsoft Graph. In alternativa, eseguire l'accesso all'account Azure AD dallo strumento Graph Explorer di Azure AD.
    2.  Dopo aver ottenuto l'ObjectId dell'entità servizio, eseguire questo comando:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-utilizing-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Esempio: creare e assegnare un criterio mediante una trasformazione di attestazioni nei token emessi per un'entità servizio.
In questo esempio si creerà un criterio che genera un'attestazione personalizzata "JoinedData" nei token emessi per le entità servizio collegate. Questa attestazione contiene un valore creato aggiungendo i dati archiviati nell'attributo extensionattribute1 all'oggetto utente con ".sandbox". In questo esempio si sceglierà anche di escludere il set di attestazioni di base nei token.


1. Creare un criterio di mapping delle attestazioni. Questo criterio, che verrà collegato a specifiche entità servizio, aggiunge le attestazioni EmployeeID e TenantCountry ai token.
    1. Per creare i criteri, eseguire questo comando: 
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformation":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"Id":"string2","Value":"sandbox"},{"Id":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample” -Type "ClaimsMappingPolicy"
    ```
    
    2. Per visualizzare il nuovo criterio e ottenere il relativo ObjectId, eseguire questo comando: 
     
     ``` powershell
    Get-AzureADPolicy
    ```
2.  Assegnare i criteri all'entità servizio. È necessario ottenere anche l'ObjectId dell'entità servizio. 
    1.  Per visualizzare tutte le entità servizio dell'organizzazione, è possibile eseguire query in Microsoft Graph. In alternativa, eseguire l'accesso all'account Azure AD dallo strumento Graph Explorer di Azure AD.
    2.  Dopo aver ottenuto l'ObjectId dell'entità servizio, eseguire questo comando: 
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```

