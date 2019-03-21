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
ms.date: 02/27/2019
ms.author: pbutlerm
ms.openlocfilehash: 9c3fbe7cd7ebd41f59be360f40d66b8d38dbce5e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57319276"
---
# <a name="saas-fulfillment-apis"></a>API di evasione degli ordini di SaaS

L'API di evasione SaaS consente ai fornitori di software indipendenti (ISV) di integrare le proprie applicazioni SaaS con Azure Marketplace. Questa API consente alle applicazioni di ISV di far parte di tutti i canali di commercio elettronico: diretto, a cura del partner (rivenditori) e a cura del campo.  Questa API è un requisito per l'elenco che SaaS transactable alle offerte in Azure Marketplace.

La versione corrente di questa API è versione 2, che devono essere usate per tutti i nuovi SaaS offre.  Versione 1 dell'API è deprecata e viene mantenuta per supportare offerte esistenti.


## <a name="business-model-support"></a>Supporto del modello di Business

Questa API supporta le seguenti funzionalità di modello; Si può:

* Specificare più piani per un'offerta. Questi piani dispongono di funzionalità differenti e possono essere applicati in modo diverso.
* Fornire un'offerta in una per ogni sito o una per ogni utente di base del modello di fatturazione.
* Fornire mensile e annuale (anticipo a pagamento) opzioni di fatturazione.
* Offrono prezzi privato per un cliente in base a un contratto aziendale negoziato.


## <a name="next-steps"></a>Passaggi successivi

Usare la versione più recente di questa interfaccia per lo sviluppo: [Versione dell'API SaaS evasione 2](./cpp-saas-fulfillment-api-v2.md).
