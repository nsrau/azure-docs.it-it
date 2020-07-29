---
title: "Criteri di gestione API di esempio: filtrare in base all'indirizzo IP quando si usa il gateway applicazione"
titleSuffix: Azure API Management
description: "Esempio di criteri di gestione API di Azure: viene illustrato come filtrare in base all'indirizzo IP della richiesta quando si usa un gateway applicazione."
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75942477"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filtrare in base all'indirizzo IP della richiesta quando si usa un gateway applicazione

Questo articolo illustra un esempio di criteri di gestione API di Azure che illustra come filtrare l'indirizzo IP della richiesta quando l'istanza di gestione API è accessibile tramite un gateway applicazione o un altro intermediario. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Policy

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di restrizione di accesso](../api-management-access-restriction-policies.md)
+ [Esempi di criteri](../policy-samples.md)
