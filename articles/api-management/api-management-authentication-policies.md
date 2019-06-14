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
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: c0f8da779ca656cf357c418b8766a53307643695
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708786"
---
# <a name="api-management-authentication-policies"></a>Criteri di autenticazione di Gestione API di Azure
Questo argomento fornisce un riferimento per i seguenti criteri di Gestione API. Per informazioni sull'aggiunta e sulla configurazione dei criteri, vedere [Criteri di Gestione API](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Criteri di autenticazione  
  
-   [Autenticazione con base](api-management-authentication-policies.md#Basic): consente di eseguire l'autenticazione con un servizio back-end tramite l'autenticazione di base.  
  
-   [Autenticazione con certificato](api-management-authentication-policies.md#ClientCertificate): consente di eseguire l'autenticazione con un servizio back-end tramite certificati client.  

-   [Eseguire l'autenticazione con identità gestita](api-management-authentication-policies.md#ManagedIdentity) -l'autenticazione con il [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per il servizio Gestione API.  
  
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
  
|Name|Descrizione|Obbligatorio|  
|----------|-----------------|--------------|  
|authentication-basic|Elemento radice.|Yes|  
  
### <a name="attributes"></a>Attributi  
  
|Name|Descrizione|Obbligatorio|Predefinito|  
|----------|-----------------|--------------|-------------|  
|username|Specifica il nome utente della credenziale di base.|Yes|N/D|  
|password|Specifica la password della credenziale di base.|Yes|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound  
  
-   **Ambiti del criterio:** API  
  
##  <a name="ClientCertificate"></a> Autenticazione con certificato client  
 Usare il criterio `authentication-certificate` per eseguire l'autenticazione con un servizio di back-end tramite il certificato client. Il certificato deve essere prima [installato in Gestione API](https://go.microsoft.com/fwlink/?LinkID=511599) e viene identificato tramite la relativa identificazione personale.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>  
```  
  
### <a name="examples"></a>Esempi  
  
In questo client di esempio certificato è identificato dalla relativa identificazione personale.
```xml  
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />  
``` 
In questo esempio il certificato client è identificato dal nome di risorsa.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementi  
  
|Name|Descrizione|Obbligatorio|  
|----------|-----------------|--------------|  
|authentication-certificate|Elemento radice.|Yes|  
  
### <a name="attributes"></a>Attributi  
  
|Name|Descrizione|Obbligatorio|Predefinito|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Identificazione personale del certificato client.|Sia `thumbprint` o `certificate-id` deve essere presente.|N/D|  
|certificate-id|Nome della risorsa certificato.|Sia `thumbprint` o `certificate-id` deve essere presente.|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound  
  
-   **Ambiti del criterio:** API  

##  <a name="ManagedIdentity"></a> Eseguire l'autenticazione con identità gestita  
 Usare il `authentication-managed-identity` criteri per l'autenticazione con un servizio back-end usando l'identità del servizio Gestione API gestita. Il criterio Usa in modo efficace l'identità gestita per ottenere un token di accesso da Azure Active Directory per l'accesso alla risorsa specificata. 
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Esempio  
  
```xml  
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" /> 
```  
  
### <a name="elements"></a>Elementi  
  
|Name|Descrizione|Obbligatorio|  
|----------|-----------------|--------------|  
|authentication-managed-identity |Elemento radice.|Yes|  
  
### <a name="attributes"></a>Attributi  
  
|Name|Descrizione|Obbligatorio|Predefinito|  
|----------|-----------------|--------------|-------------|  
|resource|Stringa. L'URI ID App dell'API (risorsa protetta) web destinazione in Azure Active Directory.|Yes|N/D|  
|output-token-variable-name|Stringa. Nome della variabile di contesto che riceverà il valore token come un tipo di oggetto `string`.|No|N/D|  
|ignore-error|Booleano. Se impostato su `true`, la pipeline di criterio continuerà a essere eseguita anche se non viene ottenuto un token di accesso.|No|false|  
  
### <a name="usage"></a>Uso  
 Questo criterio può essere usato nelle [sezioni](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso di questi criteri, vedere:

+ [Criteri di Gestione API](api-management-howto-policies.md)
+ [Trasformare le API](transform-api.md)
+ [Informazioni di riferimento sui criteri](api-management-policy-reference.md) per un elenco completo delle istruzioni dei criteri e delle relative impostazioni
+ [Esempi di criteri](policy-samples.md)   
