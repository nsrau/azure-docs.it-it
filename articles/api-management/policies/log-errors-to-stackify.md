---
title: 'Esempio di criteri di Gestione API di Azure: inviare errori a Stackify per la registrazione | Microsoft Docs'
description: Esempio di criteri di Gestione API di Azure che illustra come aggiungere criteri di registrazione degli errori per inviare gli errori a Stackify per la registrazione.
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
ms.openlocfilehash: 07cc83830fe2d467c611622bb66dfbb8c9429c2d
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52864877"
---
# <a name="send-errors-to-stackify-for-logging"></a>Inviare errori a Stackify per la registrazione

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come aggiungere criteri di registrazione degli errori per inviare gli errori a Stackify per la registrazione. Per impostare o modificare il codice dei criteri, seguire la procedura descritta in [Impostare o modificare un criterio](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criterio

Incollare il codice nel blocco **on-error**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

