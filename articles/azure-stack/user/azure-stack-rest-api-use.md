---
title: Utilizzare l'API dello Stack di Azure | Documenti Microsoft
description: Informazioni su come recuperare un tipo di autenticazione da Azure per effettuare richieste API allo Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: e8a9489a3f487a45303bac45f805381b41427b4b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Utilizzare l'API dello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile utilizzare il Azure Stack interfaccia API (Application Programming) per automatizzare le operazioni, ad esempio la diffusione di elementi del marketplace.

L'API richiede che il client autenticare l'endpoint dell'account di accesso di Microsoft Azure. L'endpoint restituisce un token da usare nell'intestazione di ogni richiesta inviata all'API di Stack di Azure. Microsoft Azure Usa Oauth 2.0.

In questo articolo vengono forniti esempi che usano il **cURL** utilità per creare le richieste dello Stack di Azure. L'applicazione, cURL, è uno strumento da riga di comando con una libreria per il trasferimento dei dati. In questi esempi vengono illustrano il processo di recupero di un token per accedere all'API di Stack di Azure. La maggior parte dei linguaggi di programmazione forniscono librerie Oauth 2.0, che dispongono di attività di gestione e gestire token affidabile tali il token di aggiornamento.

Esaminare l'intero processo di tramite l'API REST di Azure Stack con un client REST generico, ad esempio **cURL**, per aiutare a comprendere sottostante richieste e viene illustrato cosa può aspettarsi di ricevere in un payload di risposta.

In questo articolo non Esplora tutte le opzioni disponibili per il recupero dei token, ad esempio account di accesso interattivo o la creazione di ID App dedicato. Per ottenere informazioni su questi argomenti, vedere [riferimento all'API REST di Azure](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>Ottenere un token da Azure

Creare un corpo della richiesta formattata utilizzando il tipo di contenuto x-www-form-urlencoded per ottenere un token di accesso. Registra la richiesta all'endpoint di autenticazione REST di Azure e all'accesso.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**ID tenant** è:

 - Il dominio del tenant, ad esempio `fabrikam.onmicrosoft.com`
 - ID del tenant, ad esempio `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
 - Valore predefinito per le chiavi indipendenti dal tenant: `common`

### <a name="post-body"></a>Corpo post

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Per ogni valore:

 - **grant_type**  
    Il tipo di schema di autenticazione verrà utilizzando. In questo esempio, il valore è `password`

 - **resource**  
    La risorsa accede al token. È possibile trovare la risorsa eseguendo una query all'endpoint di metadati di gestione di Azure Stack. Esaminare i **destinatari** sezione

 - **Endpoint di gestione di Azure Stack**  
    ```
    https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
    ```

  > [!NOTE]  
  > Se si è un amministratore tenta di accedere al tenant API deve assicurarsi che usare endpoint tenant, ad esempio: `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`  

  Ad esempio, con il Kit di sviluppo dello Stack Azure come endpoint:

    ```bash
    curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
    ```

  Risposta:

  ```
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
      "loginEndpoint":"https://login.windows.net/",
      "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
      }
  }
  ```

### <a name="example"></a>Esempio

  ```
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

  **client_id**

  Questo valore è hardcoded su un valore predefinito:

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Opzioni alternative sono disponibili per scenari specifici:

  
  | Applicazione | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **username**

  Ad esempio, l'account di Azure AAD dello Stack:

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **password**

  Password dell'amministratore Azure Stack AAD.

### <a name="example"></a>Esempio

Richiesta:

```
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Risposta:

```
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>Query dell'API

Dopo aver ottenuto il token di accesso, è necessario aggiungerlo come un'intestazione a ognuna delle richieste di API. Per eseguire questa operazione, è necessario creare un'intestazione **authorization** con valore: `Bearer <access token>`. Ad esempio: 

Richiesta:

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Risposta:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>Sintassi di struttura e query di URL

È costituito da un URI di richiesta generica: {schema URI} :// {URI-host} / {resource-path}? {query-string}

- **Schema URI**:  
URI che indica il protocollo utilizzato per inviare la richiesta. Ad esempio, `http` o `https`.
- **Host dell'URI**:  
L'host specifica il nome di dominio o l'indirizzo IP del server in cui l'endpoint REST del servizio è ospitato, ad esempio `graph.microsoft.com` o `adminmanagement.local.azurestack.external`.
- **Percorso della risorsa**:  
Il percorso Specifica la risorsa o raccolta di risorse, che può includere più segmenti utilizzati dal servizio per determinare la selezione di tali risorse. Ad esempio: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` può essere usato per query sull'elenco dei proprietari di un'applicazione specifica all'interno della raccolta di applicazioni.
- **Stringa di query**:  
La stringa fornisce semplici di parametri aggiuntivi, ad esempio i criteri di selezione API versione o una risorsa.

## <a name="azure-stack-request-uri-construct"></a>Azure costrutto URI richiesta di Stack

```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>Sintassi dell'URI

```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Esempio di URI di query

```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo gli endpoint REST di Azure, vedere [riferimento all'API REST di Azure](https://docs.microsoft.com/rest/api/).
