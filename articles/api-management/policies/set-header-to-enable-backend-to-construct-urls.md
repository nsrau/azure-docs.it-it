---
title: "Esempio di criteri di gestione API di Azure: aggiungere un'intestazione trasferita | Microsoft Docs"
description: "Esempio di criteri di gestione API di Azure: illustrare come aggiungere un'intestazione trasferita nella richiesta in ingresso per consentire all'API di back-end di costruire URL corrette."
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: e525029ae8eab086d11126a4e18958423e207aa1
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067493"
---
# <a name="add-a-forwarded-header"></a>Aggiungere un'intestazione Forwarded

In questo articolo viene descritto un esempio di criteri di Gestione API di Azure che illustra come aggiungere un'intestazione Forwarded nella richiesta in ingresso per consentire all'API back-end di costruire URL corretti. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="code"></a>Codice

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)
