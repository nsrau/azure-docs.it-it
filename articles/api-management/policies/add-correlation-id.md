---
title: "Esempio di criteri di gestione delle API di Azure: aggiungere un'intestazione con un ID di correlazione | Microsoft Docs"
description: "Esempio di criteri di gestione delle API di Azure: illustra come aggiungere un'intestazione con un ID di correlazione alla richiesta in ingresso."
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 126701674b6dc529404f5a7854cda9b31c336170
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868430"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Aggiungere un'intestazione con un ID di correlazione

Questo articolo illustra un esempio di criteri di gestione delle API di Azure che illustra come aggiungere un'intestazione con un ID di correlazione alla richiesta in ingresso. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criterio

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

