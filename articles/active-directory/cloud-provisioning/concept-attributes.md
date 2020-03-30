---
title: Informazioni sullo schema di Azure AD e sulle espressioni personalizzateUnderstand the Azure AD schema and custom expressions
description: Questo articolo descrive lo schema di Azure AD, gli attributi sorcuiti dall'agente di provisioning ed espressioni personalizzate.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac09fb3faf55be6c07a1e0a88b6e2032c9ab8ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299330"
---
# <a name="understand-the-azure-ad-schema"></a>Comprendere lo schema di Azure ADUnderstand the Azure AD schema
Un oggetto in Azure Active Directory (Azure AD), come qualsiasi directory, è un costrutto di dati di alto livello a livello di codice che rappresenta elementi quali utenti, gruppi e contatti. Quando si crea un nuovo utente o contatto in Azure AD, si crea una nuova istanza di tale oggetto. Queste istanze possono essere differenziate in base alle relative proprietà.

Le proprietà in Azure AD sono gli elementi responsabili dell'archiviazione di informazioni su un'istanza di un oggetto in Azure AD.

Lo schema di Azure AD definisce le regole per le quali le proprietà possono essere usate in una voce, i tipi di valori che tali proprietà potrebbero avere e il modo in cui gli utenti potrebbero interagire con tali valori. 

Azure AD include due tipi di proprietà:Azure AD has two types of properties:
- **Proprietà predefinite:** proprietà predefinite dallo schema di Azure AD. Queste proprietà forniscono usi diversi e potrebbero essere accessibili o meno.
- **Estensioni di directory:** proprietà fornite in modo da poter personalizzare Azure AD per uso personale. Ad esempio, se Active Directory locale è stato esteso con un determinato attributo e si desidera eseguire il flusso di tale attributo, è possibile utilizzare una delle proprietà personalizzate fornite. 

## <a name="attributes-and-expressions"></a>Attributi ed espressioni
Quando viene eseguito il provisioning di un oggetto, ad esempio un utente, in Azure AD, viene creata una nuova istanza dell'oggetto utente. Questa creazione include le proprietà di tale oggetto, note anche come attributi. Inizialmente, i relativi attributi dell'oggetto appena creato sono impostati su valori determinati dalle regole di sincronizzazione. Questi attributi vengono quindi mantenuti aggiornati tramite l'agente di provisioning cloud.

![Provisioning degli oggetti](media/concept-attributes/attribute1.png)

Ad esempio, un utente potrebbe far parte di un reparto Marketing. L'attributo del reparto di Azure AD viene inizialmente creato quando ne viene eseguito il provisioning e il valore è impostato su Marketing.Their Azure AD department attribute is initially created when they're provisioned, and the value is set to Marketing. Sei mesi dopo, se vengono modificati in Vendite, l'attributo del reparto di Active Directory locale viene modificato in Vendite. Questa modifica viene sincronizzata con Azure AD e si riflette nell'oggetto utente di Azure AD.

La sincronizzazione degli attributi potrebbe essere diretta, in cui il valore in Azure AD è impostato direttamente sul valore dell'attributo locale. In alternativa, un'espressione a livello di codice potrebbe gestire la sincronizzazione. Un'espressione a livello di codice è necessaria nei casi in cui è necessario eseguire una logica o una determinazione per popolare il valore.

Ad esempio, se si avessejohn.smith@contoso.coml'attributo mail@contoso.com" " e dovessi rimuovere la porzione " " e scorrere solo il valore "john.smith", si userebbe qualcosa di simile al seguente:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Input/output di esempio:** <br>

* **INPUT** (mail): "john.smith@contoso.com"
* **OUTPUT**: "john.smith"

Per altre informazioni su come scrivere espressioni personalizzate e sulla sintassi, vedere Scrittura di espressioni per i mapping degli [attributi in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)

Nella tabella seguente sono elencati gli attributi comuni e la modalità di sincronizzazione con Azure AD.


|Active Directory locale|Tipo di mappatura|Azure AD|
|-----|-----|-----|
|cn|Connessione diretta|Commonname
|countryCode|Connessione diretta|countryCode|
|displayName|Connessione diretta|displayName|
|givenName|Expression|givenName|
|objectGUID|Connessione diretta|sourceAnchorBinary (informazioni in base a un oggetto source|  
|userprincipalName|Connessione diretta|userPrincipalName|
|ProxyAdress (Indirizzo)|Connessione diretta|ProxyAddress|

## <a name="view-the-schema"></a>Visualizzare lo schema
> [!WARNING]
> La configurazione del provisioning cloud crea un'entità servizio. L'entità servizio è visibile nel portale di Azure.The service principal is visible in the Azure portal. Non modificare i mapping degli attributi usando l'esperienza dell'entità servizio nel portale di Azure.You should not modify the attribute mappings using the service principal experience in the Azure portal.  Questa caratteristica non è supportata.

Per visualizzare lo schema e verificarlo, attenersi alla seguente procedura.

1.  Passare a [Esplora grafica](https://developer.microsoft.com/graph/graph-explorer).
1.  Accedi con il tuo account amministratore globale.
1.  A sinistra, selezionare **Modifica autorizzazioni** e verificare che **Directory.ReadWrite.All** sia *Cond*.
1.  Eseguire la `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')`query . Questa query restituisce un elenco filtrato di entità servizio.
1.  Individuare `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` e annotare il valore di `"id"`.
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. Sostituire `{Service Principal id}` con il valore ed `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`eseguire la query .
1. Individuare `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` e annotare il valore di `"id"`.
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. A questo `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`punto eseguire la query .
 
    Esempio: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   Sostituire `{Service Principal Id}` `{AD2ADD Provisioning Id}` e con i valori.

1. Questa query restituisce lo schema.

   ![Schema restituito](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Passaggi successivi

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
