---
title: Proteggere le API usando l&quot;autenticazione con certificato client in Gestione API - Gestione API di Azure | Documentazione Microsoft
description: Informazioni su come proteggere l&quot;accesso alle API usando i certificati client
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: b04ce0fe0db7649cebc7a1eeb2a35f1d53bf9636
ms.openlocfilehash: 1ce9f07c3e91eacdc74ccab3fbb7dc433a25c197

---

# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Come proteggere le API usando l'autenticazione con certificati client in Gestione API

Gestione API offre la possibilità di proteggere l'accesso alle API (ovvero dal client a Gestione API) mediante certificati client. Attualmente è possibile controllare l'identificazione personale del certificato client rispetto a un valore desiderato. È inoltre possibile controllare l'identificazione personale rispetto a certificati esistenti caricati in Gestione API.  

Per informazioni sulla protezione dell'accesso al servizio back-end di un'API mediante certificati client (ovvero da Gestione API al back-end), vedere [Come proteggere i servizi back-end usando l'autenticazione con certificati client](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)

## <a name="checking-a-thumbprint-against-a-desired-value"></a>Controllo di un'identificazione personale rispetto a un valore desiderato

I criteri riportati di seguito possono essere configurati per controllare l'identificazione personale del certificato client:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint-to-validate")" >
        <return-response>
            <set-status code="401" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Controllo di un'identificazione personale rispetto a certificati caricati in Gestione API

L'esempio seguente illustra come controllare l'identificazione personale di un certificato client rispetto a certificati caricati in Gestione API: 

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="401" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Passaggio successivo

*  [Come proteggere i servizi back-end usando l'autenticazione con certificati client](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)
*  [Come caricare i certificati](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)




<!--HONumber=Feb17_HO1-->


