---
title: "Esempio di criteri di Gestione API di Azure: aggiungere funzionalità a un servizio back-end | Microsoft Docs"
description: "Esempio di criteri di Gestione API di Azure che illustra come aggiungere funzionalità a un servizio back-end, ad esempio per accettare il nome di un luogo anziché latitudine e longitudine in un'API di previsioni meteo."
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
ms.openlocfilehash: d500df0f0e48134ac9c1397795d65706d2e56ff9
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="add-capabilities-to-a-backend-service"></a>Aggiungere funzionalità a un servizio back-end

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come aggiungere funzionalità a un servizio back-end, ad esempio per accettare il nome di un luogo anziché latitudine e longitudine in un'API di previsioni meteo. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criterio

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

