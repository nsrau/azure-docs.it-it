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
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 263f8495b9dbb0a1c5b3c54301b4b4deab425e31
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072370"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Come proteggere le API usando l'autenticazione con certificati client in Gestione API

Gestione API offre la possibilità di proteggere l'accesso alle API (ovvero dal client a Gestione API) mediante certificati client. È possibile convalidare il certificato in ingresso e controllare le proprietà del certificato in base ai valori desiderati utilizzando espressioni di criteri.

Per informazioni sulla protezione dell'accesso al servizio back-end di un'API usando i certificati client (ad esempio, gestione API nel back-end), vedere [come proteggere i servizi back-end usando l'autenticazione con certificati client](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) .

> [!IMPORTANT]
> Per ricevere e verificare i certificati client nel livello di consumo, è necessario innanzitutto attivare l'impostazione "Richiedi certificato client" nel pannello "domini personalizzati", come illustrato di seguito.

![Richiedi certificato client](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Controllo dell'autorità di certificazione e del soggetto

È possibile configurare i criteri riportati di seguito per controllare l'autorità di certificazione e il soggetto di un certificato client:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Per disabilitare il controllo dell'elenco di revoche `context.Request.Certificate.VerifyNoRevocation()` di certificati `context.Request.Certificate.Verify()`, utilizzare invece di.
> Se il certificato client è autofirmato, i certificati della CA radice (o intermedia) devono essere [caricati](api-management-howto-ca-certificates.md) in gestione API per `context.Request.Certificate.Verify()` il `context.Request.Certificate.VerifyNoRevocation()` funzionamento di e.

## <a name="checking-the-thumbprint"></a>Controllo dell'identificazione personale

I criteri riportati di seguito possono essere configurati per controllare l'identificazione personale del certificato client:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Per disabilitare il controllo dell'elenco di revoche `context.Request.Certificate.VerifyNoRevocation()` di certificati `context.Request.Certificate.Verify()`, utilizzare invece di.
> Se il certificato client è autofirmato, i certificati della CA radice (o intermedia) devono essere [caricati](api-management-howto-ca-certificates.md) in gestione API per `context.Request.Certificate.Verify()` il `context.Request.Certificate.VerifyNoRevocation()` funzionamento di e.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Controllo di un'identificazione personale rispetto a certificati caricati in Gestione API

L'esempio seguente illustra come controllare l'identificazione personale di un certificato client rispetto a certificati caricati in Gestione API:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Per disabilitare il controllo dell'elenco di revoche `context.Request.Certificate.VerifyNoRevocation()` di certificati `context.Request.Certificate.Verify()`, utilizzare invece di.
> Se il certificato client è autofirmato, i certificati della CA radice (o intermedia) devono essere [caricati](api-management-howto-ca-certificates.md) in gestione API per `context.Request.Certificate.Verify()` il `context.Request.Certificate.VerifyNoRevocation()` funzionamento di e.

> [!TIP]
> Il problema di deadlock del certificato client descritto in questo [articolo](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) può manifestarsi in diversi modi, ad esempio le richieste bloccate, il risultato del codice `context.Request.Certificate` di `null` `403 Forbidden` stato dopo il timeout è. Questo problema in genere `POST` interessa `PUT` e richiede una lunghezza del contenuto di approssimativamente 60kb o superiore.
> Per evitare che si verifichi questo problema, attivare l'impostazione "Negotiate client certificate" per i nomi host desiderati nel pannello "domini personalizzati", come illustrato di seguito. Questa funzionalità non è disponibile nel livello a consumo.

![Negozia certificato client](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Passaggi successivi

-   [Come proteggere i servizi back-end usando l'autenticazione con certificati client](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Come caricare i certificati](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
