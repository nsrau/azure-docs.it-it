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
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 5427c4050b6b70c18da7a1899d16e448c41e81c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427331"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Come proteggere le API usando l'autenticazione con certificati client in Gestione API

Gestione API offre la possibilità di proteggere l'accesso alle API (ovvero dal client a Gestione API) mediante certificati client. È possibile convalidare il certificato in ingresso e controllare le proprietà del certificato in base ai valori desiderati utilizzando espressioni di criteri.

Per informazioni sulla protezione dell'accesso al servizio back-end di un'API usando i certificati client (ovvero da Gestione API al back-end), vedere [come proteggere i servizi back-end client usando l'autenticazione del certificato](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Per ricevere e verificare i certificati client nel livello di consumo è prima necessario attivare "Richiesta di certificato client" impostazione nel pannello "Domini personalizzati" come illustrato di seguito.

![Richiesta di certificato client](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

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
> Per disabilitare l'uso di elenco di controllo certificato revoche `context.Request.Certificate.VerifyNoRevocation()` invece di `context.Request.Certificate.Verify()`.
> Se il certificato client autofirmato, radice (o a livello intermedio) devono essere uno o più certificati CA [caricati](api-management-howto-ca-certificates.md) in Gestione API per `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` a funzionare.

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
> Per disabilitare l'uso di elenco di controllo certificato revoche `context.Request.Certificate.VerifyNoRevocation()` invece di `context.Request.Certificate.Verify()`.
> Se il certificato client autofirmato, radice (o a livello intermedio) devono essere uno o più certificati CA [caricati](api-management-howto-ca-certificates.md) in Gestione API per `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` a funzionare.

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
> Per disabilitare l'uso di elenco di controllo certificato revoche `context.Request.Certificate.VerifyNoRevocation()` invece di `context.Request.Certificate.Verify()`.
> Se il certificato client autofirmato, radice (o a livello intermedio) devono essere uno o più certificati CA [caricati](api-management-howto-ca-certificates.md) in Gestione API per `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` a funzionare.

> [!TIP]
> Problema relativo al deadlock certificato client descritto in questo [articolo](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) può manifestarsi in diversi modi, ad esempio, le richieste blocca, il risultato di richieste `403 Forbidden` codice di stato dopo il timeout, `context.Request.Certificate` è `null`. Si verifica questo problema in genere `POST` e `PUT` richieste con lunghezza del contenuto pari a circa 60 KB o superiori.
> Per evitare questo problema che si verifichi attiva impostazione "Certificato client Negotiate" per i nomi host desiderati nel pannello "Domini personalizzati" come illustrato di seguito. Questa funzionalità non è disponibile nel livello di consumo.

![Certificato client di negoziare](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Passaggi successivi

-   [Come proteggere i servizi back-end usando l'autenticazione con certificati client](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Come caricare i certificati](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
