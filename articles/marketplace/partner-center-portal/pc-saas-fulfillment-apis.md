---
title: API di adempimento SaaS Azure Marketplace
description: Introduce le versioni delle API di evasione che consentono di integrare le offerte SaaS con Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275731"
---
# <a name="saas-fulfillment-apis"></a>API di evasione ordini SaaS

Le API SaaS Fulfillment consentono ai fornitori di software indipendenti (ISV) di integrare le applicazioni SaaS con Azure Marketplace. Queste API consentono alle applicazioni ISV di partecipare a tutti i canali abilitati per il commercio: diretti, guidati da partner (rivenditore) e guidati sul campo.  Sono un requisito per elencare le offerte SaaS transazionali in Azure Marketplace.They are a requirement for listing transactable SaaS offers on the Azure Marketplace.

> [!WARNING]
> La versione corrente di questa API è la versione 2, che deve essere utilizzata per tutte le nuove offerte SaaS.  La versione 1 dell'API è deprecata ed è in fase di manutenzione per supportare le offerte esistenti.


## <a name="business-model-support"></a>Supporto del modello di business

Questa API supporta le seguenti funzionalità del modello di business; Si può:

* Specificare più piani per un'offerta. Questi piani hanno funzionalità diverse e possono avere un prezzo diverso.
* Fornire un'offerta per sito o per modello di fatturazione per utente.
* Fornire opzioni di fatturazione mensile e annuale (a anticipo anticipato a pagamento).
* Fornire prezzi privati a un cliente in base a un contratto commerciale negoziato.


## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, registrare l'applicazione SaaS nel portale di [Azure](https://ms.portal.azure.com) come illustrato in [Registrare un'applicazione Azure AD](./pc-saas-registration.md).  Successivamente, utilizzare la versione più recente di questa interfaccia per lo sviluppo: [SaaS Fulfillment API versione 2](./pc-saas-fulfillment-api-v2.md).
