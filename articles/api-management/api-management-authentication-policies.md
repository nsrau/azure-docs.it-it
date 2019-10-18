---
title: Criteri di autenticazione di Gestione API di Azure | Documentazione Microsoft
description: Informazioni sui criteri di autenticazione disponibili per l'uso in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 572d8c4b59622156e8b3aca4565bbc206367f6d4
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514847"
---
# <a name="api-management-authentication-policies"></a>Criteri di autenticazione di Gestione API di Azure
Questo argomento fornisce un riferimento per i seguenti criteri di Gestione API. Per informazioni sull'aggiunta e sulla configurazione dei criteri, vedere [Criteri di Gestione API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AuthenticationPolicies"></a> Criteri di autenticazione

-   [Autenticazione con base](api-management-authentication-policies.md#Basic): consente di eseguire l'autenticazione con un servizio back-end tramite l'autenticazione di base.

-   [Autenticazione con certificato](api-management-authentication-policies.md#ClientCertificate): consente di eseguire l'autenticazione con un servizio back-end tramite certificati client.

-   [Eseguire l'autenticazione con identità gestita](api-management-authentication-policies.md#ManagedIdentity) : eseguire l'autenticazione con l' [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per il servizio gestione API.

##  <a name="Basic"></a> Autenticazione con base
 Usare il criterio `authentication-basic` per eseguire l'autenticazione con un servizio di back-end tramite l'autenticazione di base. Questo criterio imposta l'intestazione di autorizzazione HTTP sul valore corrispondente alle credenziali specificate nei criteri.

### <a name="policy-statement"></a>Istruzione del criterio

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Esempio

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elementi

|name|Description|Obbligatoria|
|----------|-----------------|--------------|
|authentication-basic|Elemento radice.|SÌ|

### <a name="attributes"></a>Attributi

|name|Description|Obbligatoria|Predefinito|
|----------|-----------------|--------------|-------------|
|Nome utente|Specifica il nome utente della credenziale di base.|SÌ|N/D|
|password|Specifica la password della credenziale di base.|SÌ|N/D|

### <a name="usage"></a>Utilizzo
 Questo criterio può essere usato nelle [sezioni](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.

-   **Sezioni del criterio:** in ingresso

-   **Ambiti del criterio:** tutti gli ambiti

##  <a name="ClientCertificate"></a> Autenticazione con certificato client
 Usare il criterio `authentication-certificate` per eseguire l'autenticazione con un servizio di back-end tramite il certificato client. Il certificato deve essere prima [installato in Gestione API](https://go.microsoft.com/fwlink/?LinkID=511599) e viene identificato tramite la relativa identificazione personale.

### <a name="policy-statement"></a>Istruzione del criterio

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>esempi

In questo esempio il certificato client viene identificato dalla relativa identificazione personale.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
In questo esempio il certificato client viene identificato in base al nome della risorsa.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementi  
  
|name|Description|Obbligatoria|  
|----------|-----------------|--------------|  
|authentication-certificate|Elemento radice.|SÌ|  
  
### <a name="attributes"></a>Attributi  
  
|name|Description|Obbligatoria|Predefinito|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Identificazione personale del certificato client.|È necessario che sia presente `thumbprint` o `certificate-id`.|N/D|  
|ID certificato|Nome della risorsa del certificato.|È necessario che sia presente `thumbprint` o `certificate-id`.|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** in ingresso  
  
-   **Ambiti del criterio:** tutti gli ambiti  

##  <a name="ManagedIdentity"></a>Eseguire l'autenticazione con identità gestita  
 Usare i criteri di `authentication-managed-identity` per eseguire l'autenticazione con un servizio back-end usando l'identità gestita del servizio gestione API. Questo criterio USA essenzialmente l'identità gestita per ottenere un token di accesso da Azure Active Directory per l'accesso alla risorsa specificata. Dopo aver ottenuto il token, il criterio imposterà il valore del token nell'intestazione `Authorization` usando lo schema di `Bearer`.
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Esempio  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Usare l'identità gestita per l'autenticazione con un servizio back-end
```xml  
<authentication-managed-identity resource="https://graph.windows.net"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Usare l'identità gestita nei criteri Send-request
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementi  
  
|name|Description|Obbligatoria|  
|----------|-----------------|--------------|  
|autenticazione-gestita-identità |Elemento radice.|SÌ|  
  
### <a name="attributes"></a>Attributi  
  
|name|Description|Obbligatoria|Predefinito|  
|----------|-----------------|--------------|-------------|  
|resource|Stringa. URI ID app dell'API Web di destinazione (risorsa protetta) in Azure Active Directory.|SÌ|N/D|  
|output-token-variabile-nome|Stringa. Nome della variabile di contesto che riceverà il valore del token come tipo di oggetto `string`. |No|N/D|  
|ignore-error|Booleano. Se è impostato su `true`, la pipeline dei criteri continuerà a essere eseguita anche se non viene ottenuto un token di accesso.|No|false|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** in ingresso  
  
-   **Ambiti del criterio:** tutti gli ambiti

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso di questi criteri, vedere:

+ [Criteri di Gestione API](api-management-howto-policies.md)
+ [API Transform](transform-api.md)
+ [Informazioni di riferimento per i criteri](api-management-policy-reference.md) per un elenco completo di istruzioni dei criteri e delle relative impostazioni
+ [Esempi di criteri](policy-samples.md)
