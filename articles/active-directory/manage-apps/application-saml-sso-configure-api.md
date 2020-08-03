---
title: Usare le API Microsoft Graph per configurare l'accesso Single Sign-On basato su SAML
titleSuffix: Azure Active Directory
description: È necessario configurare l'accesso Single Sign-On basato su SAML per più istanze di un'applicazione? Informazioni su come risparmiare tempo usando le API Microsoft Graph per automatizzare la configurazione dell'accesso Single Sign-On basato su SAML.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: bae5770baf8cfad7e5f28d5cc0499949912c1146
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513129"
---
# <a name="automate-saml-based-sso-app-configuration-with-microsoft-graph-api"></a>Automatizzare la configurazione dell'accesso SSO basato su SAML per l'app con l'API Microsoft Graph

Questo articolo illustra come creare e configurare un'applicazione dalla raccolta di Azure Active Directory (Azure AD). L'articolo usa AWS come esempio, ma è possibile usare questa procedura per tutte le app basate su SAML nella raccolta di Azure AD.

**Passaggi per usare le API Microsoft Graph per automatizzare la configurazione dell'accesso Single Sign-On basato su SAML**

| Passaggio  | Dettagli  |
|---------|---------|
| [1. Creare l'applicazione della raccolta](#step-1-create-the-gallery-application) | Accedere al client API <br> Recuperare l'applicazione della raccolta <br> Creare l'applicazione della raccolta|
| [2. Configurare l'accesso Single Sign-On](#step-2-configure-single-sign-on) | Recuperare l'ID oggetto app e l'ID oggetto entità servizio <br> Impostare la modalità Single Sign-On <br> Impostare gli URL SAML di base, ad esempio identificatore, URL di risposta, URL di accesso <br> Aggiungere i ruoli app (facoltativo)|
| [3. Configurare il mapping delle attestazioni](#step-3-configure-claims-mapping) | Creare i criteri di mapping delle attestazioni <br> Assegnare i criteri di mapping delle attestazioni all'entità servizio|
| [4. Configurare il certificato di firma](#step-4-configure-signing-certificate) | Creare un certificato <BR> Aggiungere una chiave di firma personalizzata <br> Attivare la chiave di firma personalizzata|
| [5. Assegnare gli utenti](#step-5-assign-users) | Assegnazione di utenti e gruppi all'applicazione
| [6. Configurare il lato applicazione](#step-6-configure-the-application-side)| Ottenere i metadati SAML di Azure AD

**Elenco di tutte le API usate nella documentazione**

Assicurarsi di disporre delle autorizzazioni corrispondenti per chiamare le API seguenti.

|Tipo di risorsa |Metodo |
|---------|---------|
|[applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta)|[List applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) <br>[Instantiate applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)|
|[servicePrincipals](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0)|[Update servicePrincipal](https://docs.microsoft.com/graph/api/serviceprincipal-update?view=graph-rest-1.0&tabs=http) <br> [Create appRoleAssignments](https://docs.microsoft.com/graph/api/serviceprincipal-post-approleassignments?view=graph-rest-1.0&tabs=http) <br> [Assign claimsMappingPolicies](https://docs.microsoft.com/graph/api/serviceprincipal-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)|
|[applications](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)|[Update application](https://docs.microsoft.com/graph/api/application-update?view=graph-rest-1.0&tabs=http)|
|[claimsMappingPolicy](https://docs.microsoft.com/graph/api/resources/claimsmappingpolicy?view=graph-rest-beta)| [Create claimsMappingPolicy](https://docs.microsoft.com/graph/api/claimsmappingpolicy-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)

>[!NOTE]
>Gli oggetti risposta illustrati in questo articolo potrebbero essere stati abbreviati per migliorare la leggibilità. Tutte le proprietà verranno restituite da una chiamata effettiva.

## <a name="step-1-create-the-gallery-application"></a>Passaggio 1: Creare l'applicazione della raccolta

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Accedere a Microsoft Graph Explorer (scelta consigliata), Postman o un altro client API preferito

1. Avviare [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
2. Selezionare **Sign-In with Microsoft** (Accedi con Microsoft) e accedere con un account amministratore globale di Azure AD o credenziali di amministratore applicazione.
3. Una volta eseguito l'accesso, verranno visualizzati i dettagli dell'account utente nel riquadro sinistro.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Recuperare l'identificatore del modello di applicazione della raccolta

Le applicazioni nella raccolta di applicazioni di Azure AD presentano un [modello di applicazione](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) che descrive i metadati per l'applicazione in questione. Usando questo modello, è possibile creare un'istanza dell'applicazione e un'entità servizio nel tenant per la gestione.

#### <a name="request"></a>Richiesta

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>Risposta

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>Creare l'applicazione della raccolta

Con l'ID modello recuperato per l'applicazione nell'ultimo passaggio, [creare un'istanza](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) dell'applicazione e l'entità servizio nel tenant.

> [!NOTE] 
> È possibile usare l'API applicationTemplate per creare un'istanza di [app non della raccolta](add-non-gallery-app.md). Usare applicationTemplateId `8adf8e6e-67b2-4cf2-a259-e3dc5476c621` .

> [!NOTE]
> Consentire il provisioning dell'app nel tenant Azure AD. Non è immediato. Una strategia consiste nell'eseguire una query GET sull'oggetto dell'entità servizio/applicazione ogni 5-10 secondi prima che la query abbia esito positivo.


#### <a name="request"></a>Richiesta

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/8b1025e4-1dd2-430b-a150-2ef79cd700f5/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>Risposta


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-configure-single-sign-on"></a>Passaggio 2: Configura accesso Single Sign-On

### <a name="retrieve-app-object-id-and-service-principal-object-id"></a>Recuperare l'ID oggetto app e l'ID oggetto entità servizio

Usare la risposta della chiamata precedente per recuperare e salvare l'ID oggetto applicazione e l'ID oggetto entità servizio.

```
"application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63"
}
"servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
### <a name="set-single-sign-on-mode"></a>Impostare la modalità Single Sign-On

In questo esempio si imposterà `saml` come modalità di accesso Single Sign-On nel [tipo di risorsa servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0). Altre proprietà dell'accesso SSO SAML che è possibile configurare sono: `notificationEmailAddresses`, `loginUrl` e `samlSingleSignOnSettings.relayState`

Prima che questa query funzioni, è necessario fornire il consenso sulla scheda **modifica autorizzazioni** in Graph Explorer. Assicurarsi anche di usare l'ID **servicePrincipal** ottenuto in precedenza.

#### <a name="request"></a>Richiesta

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: servicePrincipal/json

{
    "preferredSingleSignOnMode": "saml"
}
```

#### <a name="response"></a>Risposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```

### <a name="set-basic-saml-urls-such-as-identifier-reply-url-sign-on-url"></a>Impostare gli URL SAML di base, ad esempio identificatore, URL di risposta, URL di accesso

Impostare l'identificatore e gli URL di risposta per AWS nell'oggetto applicazione.

Assicurarsi di usare l'ID **applicazione** ottenuto in precedenza.

#### <a name="request"></a>Richiesta

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/applications/cbc071a6-0fa5-4859-8g55-e983ef63df63
Content-type: applications/json

{
    "web": {
        "redirectUris": [
            "https://signin.aws.amazon.com/saml"
        ] 
    },
    "identifierUris": [
        "https://signin.aws.amazon.com/saml"
    ]    
}
```
#### <a name="response"></a>Risposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```
### <a name="add-app-roles-optional"></a>Aggiungere i ruoli app (facoltativo)

Se l'applicazione richiede le informazioni sui ruoli nel token, aggiungere la definizione dei ruoli nell'oggetto applicazione. Per AWS, è possibile [abilitare il provisioning utenti](../app-provisioning/application-provisioning-configure-api.md) per recuperare tutti i ruoli dall'account AWS. 

Per altre informazioni, vedere [Configurare l'attestazione basata su ruolo rilasciata nel token SAML](../develop/active-directory-enterprise-app-role-management.md)

> [!NOTE] 
> Quando si aggiungono i ruoli dell'app, non modificare i ruoli app predefiniti msiam_access. 

#### <a name="request"></a>Richiesta

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/serviceprincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: serviceprincipals/json

{
"appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "454dc4c2-8176-498e-99df-8c4efcde41ef",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-admin,arn:aws:iam::212743507312:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Finance,WAAD",
            "displayName": "Finance,WAAD",
            "id": "8642d5fa-18a3-4245-ab8c-a96000c1a217",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-finance,arn:aws:iam::212743507312:saml-provider/WAAD"
        }
    ]

}
```

#### <a name="response"></a>Risposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-3-configure-claims-mapping"></a>Passaggio 3: Configurare il mapping delle attestazioni

### <a name="create-claims-mapping-policy"></a>Creare i criteri di mapping delle attestazioni

Oltre alle attestazioni di base, configurare le attestazioni seguenti per Azure AD da rilasciare nel token SAML:

| Nome dell'attestazione | Source (Sorgente)  |
|---------|---------|
| `https://aws.amazon.com/SAML/Attributes/Role` | assignedroles| 
| `https://aws.amazon.com/SAML/Attributes/RoleSessionName` | userprincipalname |
| `https://aws.amazon.com/SAML/Attributes/SessionDuration` | "900" |
| roles | assignedroles |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` | userprincipalname |

Per altre informazioni, vedere [Customize Claims emitted in token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

> [!NOTE]
> Alcune chiavi del criterio di mapping delle attestazioni fanno distinzione tra maiuscole e minuscole (ad esempio, "Version"). Se viene visualizzato un messaggio di errore simile a "la proprietà ha un valore non valido", potrebbe trattarsi di un problema con distinzione tra maiuscole e minuscole.

#### <a name="request"></a>Richiesta

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies
Content-type: claimsMappingPolicies/json

{
    "definition":[
            "{\"ClaimsMappingPolicy\": {
                \"Version\": 1,
                \"IncludeBasicClaimSet\": \"true\",
                \"ClaimsSchema\": [
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/Role\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"900\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"appRoles\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"
                    }
                ]
            }
        }"

    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

#### <a name="response"></a>Risposta

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.claimsMappingPolicies",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: claimsMappingPolicies/json

{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#policies/claimsMappingPolicies/$entity",
    "id": "6b33aa8e-51f3-41a6-a0fd-d660d276197a",
    "definition": [
        "{\"ClaimsMappingPolicy\": {\"Version\": 1,\"IncludeBasicClaimSet\": \"true\",\"ClaimsSchema\": [{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"https://aws.amazon.com/SAML/Attributes/Role\"\r\n                    },{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"},{\"Source\": \"user\",\"ID\": \"900\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"},{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"appRoles\"},{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"}]}}"
    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

### <a name="assign-claims-mapping-policy-to-service-principal"></a>Assegnare i criteri di mapping delle attestazioni all'entità servizio

#### <a name="request"></a>Richiesta

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/claimsMappingPolicies/$ref

Content-type: claimsMappingPolicies/json

{
  "@odata.id":"https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies/6b33aa8e-51f3-41a6-a0fd-d660d276197a"
}
```

#### <a name="response"></a>Risposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-4-configure-signing-certificate"></a>Passaggio 4: Configurare il certificato di firma

L'uso dell'API [applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta) non crea un certificato di firma per impostazione predefinita. Creare il certificato di firma personalizzato e assegnarlo all'applicazione. 

### <a name="create-a-custom-signing-certificate"></a>Creare un certificato di firma personalizzato

Per eseguire il test, è possibile usare il comando di PowerShell seguente per ottenere un certificato autofirmato. Sarà quindi necessario modificare e estrarre manualmente i valori necessari usando altri strumenti. Usare le procedure di sicurezza consigliate dell'azienda per creare un certificato di firma per l'ambiente di produzione.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [string]$fqdn,
    [Parameter(Mandatory=$true)]
    [string]$pwd,
    [Parameter(Mandatory=$true)]
    [string]$location
) 

if (!$PSBoundParameters.ContainsKey('location'))
{
    $location = "."
} 

$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx" 

Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile
```

In alternativa, è possibile usare l'app console C# seguente come modello di verifica per comprendere come è possibile ottenere i valori necessari. Si noti che questo codice è **solo per l'apprendimento e il riferimento** e non deve essere usato così com'è nell'ambiente di produzione.

```csharp
using System;
using System.Security.Cryptography;
using System.Security.Cryptography.X509Certificates;
using System.Text;


/* CONSOLE APP - PROOF OF CONCEPT CODE ONLY!!
 * This code uses a self signed certificate and should not be used 
 * in production. This code is for reference and learning ONLY.
 */
namespace Self_signed_cert
{
    class Program
    {
        static void Main(string[] args)
        {
            // Generate a guid to use as a password and then create the cert.
            string password = Guid.NewGuid().ToString();
            var selfsignedCert = buildSelfSignedServerCertificate(password);

            // Print values so we can copy paste into the JSON fields.
            // Print out the private key in base64 format.
            Console.WriteLine("Private Key: {0}{1}", Convert.ToBase64String(selfsignedCert.Export(X509ContentType.Pfx, password)), Environment.NewLine);

            // Print out the start date in ISO 8601 format.
            DateTime startDate = DateTime.Parse(selfsignedCert.GetEffectiveDateString()).ToUniversalTime();
            Console.WriteLine("For All startDateTime: " + startDate.ToString("o"));

            // Print out the end date in ISO 8601 format.
            DateTime endDate = DateTime.Parse(selfsignedCert.GetExpirationDateString()).ToUniversalTime();
            Console.WriteLine("For All endDateTime: " + endDate.ToString("o"));

            // Print the GUID used for keyId
            string signAndPasswordGuid = Guid.NewGuid().ToString();
            string verifyGuid = Guid.NewGuid().ToString();
            Console.WriteLine("GUID to use for keyId for keyCredentials->Usage == Sign and passwordCredentials: " + signAndPasswordGuid);
            Console.WriteLine("GUID to use for keyId for keyCredentials->Usage == Verify: " + verifyGuid);

            // Print out the password.
            Console.WriteLine("Password is: {0}", password);

            // Print out a displayName to use as an example.
            Console.WriteLine("displayName to use: CN=Example");
            Console.WriteLine();

            // Print out the public key.
            Console.WriteLine("Public Key: {0}{1}", Convert.ToBase64String(selfsignedCert.Export(X509ContentType.Cert)), Environment.NewLine);
            Console.WriteLine();

            // Generate the customKeyIdentifier using hash of thumbprint.
            Console.WriteLine("You can generate the customKeyIdentifier by getting the SHA256 hash of the certs thumprint.\nThe certs thumbprint is: {0}{1}", selfsignedCert.Thumbprint, Environment.NewLine);
            Console.WriteLine("The hash of the thumbprint that we will use for customeKeyIdentifier is:");
            string keyIdentifier = GetSha256FromThumbprint(selfsignedCert.Thumbprint);
            Console.WriteLine(keyIdentifier);
        }

        // Generate a self-signed certificate.
        private static X509Certificate2 buildSelfSignedServerCertificate(string password)
        {
            const string CertificateName = @"Microsoft Azure Federated SSO Certificate TEST";
            DateTime certificateStartDate = DateTime.UtcNow;
            DateTime certificateEndDate = certificateStartDate.AddYears(2).ToUniversalTime();

            X500DistinguishedName distinguishedName = new X500DistinguishedName($"CN={CertificateName}");

            using (RSA rsa = RSA.Create(2048))
            {
                var request = new CertificateRequest(distinguishedName, rsa, HashAlgorithmName.SHA256, RSASignaturePadding.Pkcs1);

                request.CertificateExtensions.Add(
                    new X509KeyUsageExtension(X509KeyUsageFlags.DataEncipherment | X509KeyUsageFlags.KeyEncipherment | X509KeyUsageFlags.DigitalSignature, false));

                var certificate = request.CreateSelfSigned(new DateTimeOffset(certificateStartDate), new DateTimeOffset(certificateEndDate));
                certificate.FriendlyName = CertificateName;

                return new X509Certificate2(certificate.Export(X509ContentType.Pfx, password), password, X509KeyStorageFlags.Exportable);
            }
        }

        // Generate hash from thumbprint.
        public static string GetSha256FromThumbprint(string thumbprint)
        {
            var message = Encoding.ASCII.GetBytes(thumbprint);
            SHA256Managed hashString = new SHA256Managed();
            return Convert.ToBase64String(hashString.ComputeHash(message));
        }
    }
}
```

### <a name="add-a-custom-signing-key"></a>Aggiungere una chiave di firma personalizzata

Aggiungere le informazioni seguenti all'entità servizio:

* Chiave privata
* Password
* Chiave pubblica 

Estrarre la chiave privata e la chiave pubblica con codifica Base 64 dal file PFX. Per altre informazioni sulle proprietà, vedere [tipo di risorsa keyCredential](https://docs.microsoft.com/graph/api/resources/keycredential?view=graph-rest-1.0).

Verificare che il valore di keyId per la proprietà keyCredential usata per "Sign" corrisponda al valore di keyId per la proprietà passwordCredential. È possibile generare `customkeyIdentifier` recuperando l'hash dell'identificazione personale del certificato. Vedere il codice di riferimento C# precedente.

#### <a name="request"></a>Richiesta

> [!NOTE]
> Il valore "key" nella proprietà keyCredentials viene abbreviato per migliorare la leggibilità. Il valore è codificato in base 64. Per la chiave privata la proprietà `usage` è "Sign". Per la chiave pubblica la proprietà `usage` è "Verify".

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "X509CertAndPassword",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=awsAPI"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=awsAPI"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "61891f4ee44d"
        }
    ]
}
```

#### <a name="response"></a>Risposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

### <a name="activate-the-custom-signing-key"></a>Attivare la chiave di firma personalizzata

È necessario impostare la proprietà `preferredTokenSigningKeyThumbprint` sull'identificazione personale del certificato che Azure AD dovrà usare per firmare la risposta SAML. 

#### <a name="request"></a>Richiesta

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "preferredTokenSigningKeyThumbprint": "AC09FEF18DDE6983EE2A164FBA3C4DD7518BD787"
}
```

#### <a name="response"></a>Risposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```
## <a name="step-5-assign-users"></a>Passaggio 5: Assegna utenti

### <a name="assign-users-and-groups-to-the-application"></a>Assegnazione di utenti e gruppi all'applicazione

Assegnare l'utente seguente all'entità servizio e assegnare il ruolo AWS_Role1. 

| Nome  | ID  |
|---------|---------|
| ID utente (principalId) | 6cad4079-4e79-4a3f-9efb-ea30a14bdb26 |
| Tipo (principalType) | Utente |
| ID ruolo app (appRoleId) | 454dc4c2-8176-498e-99df-8c4efcde41ef |
| servicePrincipalID (resourceId) | f47a6776-bca7-4f2e-bc6c-eec59d058e3e |

#### <a name="request"></a>Richiesta

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/appRoleAssignments

Content-type: appRoleAssignments/json

{
  "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
  "principalType": "User",
  "appRoleId":"454dc4c2-8176-498e-99df-8c4efcde41ef",
  "resourceId":"f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
#### <a name="response"></a>Risposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 201 
Content-type: appRoleAssignments/json

{
    "id": "rq7hyzl4yECaNZleMrTpDV-OCe5TEl5Ao_o76XMrRFU",
    "creationTimestamp": "2020-04-23T17:38:13.2508567Z",
    "appRoleId": "454dc4c2-8176-498e-99df-8c4efcde41ef",
    "principalDisplayName": "User 1",
    "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
    "principalType": "User",
    "resourceDisplayName": "AWS API Created",
    "resourceId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```

Per altre informazioni, vedere il tipo di risorsa [appRoleAssignment](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-1.0).

## <a name="step-6-configure-the-application-side"></a>Passaggio 6: Configurare il lato applicazione

### <a name="get-azure-ad-saml-metadata"></a>Ottenere i metadati SAML di Azure AD

Usare l'URL seguente per ottenere i metadati SAML di Azure AD per l'applicazione configurata specifica. I metadati contengono informazioni come certificato di firma, Azure AD entityID e Azure AD SingleSignOnService, tra le altre.

https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id}

## <a name="next-steps"></a>Passaggi successivi
- [Usare le API Microsoft Graph per configurare il provisioning utenti](../app-provisioning/application-provisioning-configure-api.md)
- [Usare il report attività dell'applicazione Active Directory Federation Services per eseguire la migrazione delle applicazioni ad Azure AD](migrate-adfs-application-activity.md)
