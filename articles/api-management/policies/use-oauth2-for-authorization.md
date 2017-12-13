---
title: 'Esempio di criteri di Gestione API di Azure: usare OAuth2 per l''autorizzazione tra gateway e back-end | Microsoft Docs'
description: Esempio di criteri di Gestione API di Azure che illustra come usare OAuth2 per l'autorizzazione tra il gateway e un back-end. Mostra come ottenere un token di accesso da AAD e inoltrarlo al back-end.
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: e0aeec66f23033f916c782c8a895e725b0735b62
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Usare OAuth2 per l'autorizzazione tra gateway e back-end

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come usare OAuth2 per l'autorizzazione tra il gateway e un back-end. Mostra come ottenere un token di accesso da AAD e inoltrarlo al back-end. Per impostare o modificare il codice dei criteri, seguire la procedura descritta in [Impostare o modificare un criterio](../set-edit-policies.md). Per altri esempi, vedere [Esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criteri

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

