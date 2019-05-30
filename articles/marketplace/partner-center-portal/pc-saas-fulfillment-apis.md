---
title: API di evasione SaaS | Azure Marketplace
description: Introduce le versioni di evasione offre API che consentono di integrare il SaaS con Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 7896ed77d9dbb3358ddb1c809ca342828280f66a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258925"
---
# <a name="saas-fulfillment-apis"></a>API di evasione ordini SaaS

Le API di evasione SaaS consentono ai fornitori di software indipendenti (ISV) di integrare le proprie applicazioni SaaS con Azure Marketplace. Queste API consentono alle applicazioni di ISV di far parte di tutti i canali di commercio elettronico: diretto, a cura del partner (rivenditori) e a cura del campo.  Sono un requisito per l'elenco transactable offerte SaaS in Azure Marketplace.

> [!WARNING]
> La versione corrente di questa API è la versione 2, che deve essere utilizzata per tutte le nuove offerte SaaS.  Versione 1 dell'API è deprecata e viene mantenuta per supportare offerte esistenti.


## <a name="business-model-support"></a>Supporto del modello di Business

Questa API supporta le seguenti funzionalità di modello; Si può:

* Specificare più piani per un'offerta. Questi piani dispongono di funzionalità differenti e possono essere applicati in modo diverso.
* Fornire un'offerta in una per ogni sito o una per ogni utente di base del modello di fatturazione.
* Fornire mensile e annuale (anticipo a pagamento) opzioni di fatturazione.
* Offrono prezzi privato per un cliente in base a un contratto aziendale negoziato.


## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, registrare l'applicazione SaaS nel [portale di Azure](https://ms.portal.azure.com) come illustrato in [registrare un'applicazione Azure AD](./pc-saas-registration.md).  Successivamente, utilizzare la versione più recente di questa interfaccia per lo sviluppo: [Versione dell'API SaaS evasione 2](./pc-saas-fulfillment-api-v2.md).
