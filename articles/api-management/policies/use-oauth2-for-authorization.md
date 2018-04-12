---
title: "Esempio di criteri di Gestione API di Azure: usare OAuth2 per l'autorizzazione tra gateway e back-end | Microsoft Docs"
description: Esempio di criteri di Gestione API di Azure che illustra come usare OAuth2 per l'autorizzazione tra il gateway e un back-end. Mostra come ottenere un token di accesso da AAD e inoltrarlo al back-end.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: fc896656a4725475fc78cadb5bab54a27cfc02a2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2018
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Usare OAuth2 per l'autorizzazione tra gateway e back-end

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come usare OAuth2 per l'autorizzazione tra il gateway e un back-end. Mostra come ottenere un token di accesso da AAD e inoltrarlo al back-end. 

Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

Lo script seguente usa le proprietà incluse in {{property}}. Per informazioni sulle proprietà e su come usarle nei criteri di Gestione API, vedere [questo](../api-management-howto-properties.md) argomento.
 
## <a name="policy"></a>Criterio

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

