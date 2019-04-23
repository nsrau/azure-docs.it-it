---
title: 'API di evasione SaaS: Azure Marketplace | Microsoft Docs'
description: Introduce le versioni di evasione offre API che consentono di integrare il SaaS con Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: pbutlerm
ms.openlocfilehash: c7da46984d592abc6ed97d7490fde732bf26b0ba
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798734"
---
# <a name="saas-fulfillment-apis"></a>API di evasione ordini SaaS

Le API di evasione SaaS consentono ai fornitori di software indipendenti (ISV) di integrare le proprie applicazioni SaaS con Azure Marketplace. Queste API consentono alle applicazioni di ISV di far parte di tutti i canali di commercio elettronico: diretto, a cura del partner (rivenditori) e a cura del campo.  Sono un requisito per l'elenco transactable offerte SaaS in Azure Marketplace.

> [!WARNING]
> La versione corrente di questa API è versione 2, che devono essere usate per tutti i nuovi SaaS offre.  Versione 1 dell'API è deprecata e viene mantenuta per supportare offerte esistenti.


## <a name="business-model-support"></a>Supporto del modello di Business

Questa API supporta le seguenti funzionalità di modello; Si può:

* Specificare più piani per un'offerta. Questi piani dispongono di funzionalità differenti e possono essere applicati in modo diverso.
* Fornire un'offerta in una per ogni sito o una per ogni utente di base del modello di fatturazione.
* Fornire mensile e annuale (anticipo a pagamento) opzioni di fatturazione.
* Offrono prezzi privato per un cliente in base a un contratto aziendale negoziato.


## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, registrare l'applicazione SaaS nel [portale di Azure](https://ms.portal.azure.com) come illustrato in [registrare un'applicazione Azure AD](./cpp-saas-registration.md).  Successivamente, utilizzare la versione più recente di questa interfaccia per lo sviluppo: [Versione dell'API SaaS evasione 2](./cpp-saas-fulfillment-api-v2.md).
