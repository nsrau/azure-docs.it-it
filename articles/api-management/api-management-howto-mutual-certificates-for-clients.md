---
title: Proteggere le API usando l'autenticazione con certificato client in Gestione API - Gestione API di Azure | Documentazione Microsoft
description: Informazioni su come proteggere l'accesso alle API usando i certificati client
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: ac9910358cf19eac3f704f1bf3e259e9a1543dcc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66141527"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Come proteggere le API usando l'autenticazione con certificati client in Gestione API

Gestione API offre la possibilità di proteggere l'accesso alle API (ovvero dal client a Gestione API) mediante certificati client. Attualmente è possibile controllare l'identificazione personale del certificato client rispetto a un valore desiderato. È inoltre possibile controllare l'identificazione personale rispetto a certificati esistenti caricati in Gestione API.  

Per informazioni sulla protezione dell'accesso al servizio back-end di un'API tramite certificati client (ovvero da Gestione API al back-end), vedere [Come proteggere i servizi back-end usando l'autenticazione con certificati client](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

## <a name="checking-the-expiration-date"></a>Controllo della data di scadenza

È possibile configurare i criteri riportati di seguito per controllare se il certificato è scaduto:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>Controllo dell'autorità di certificazione e del soggetto

È possibile configurare i criteri riportati di seguito per controllare l'autorità di certificazione e il soggetto di un certificato client:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>Controllo dell'identificazione personale

I criteri riportati di seguito possono essere configurati per controllare l'identificazione personale del certificato client:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Controllo di un'identificazione personale rispetto a certificati caricati in Gestione API

L'esempio seguente illustra come controllare l'identificazione personale di un certificato client rispetto a certificati caricati in Gestione API: 

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Passaggio successivo

*  [Come proteggere i servizi back-end usando l'autenticazione con certificati client](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [Come caricare i certificati](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

