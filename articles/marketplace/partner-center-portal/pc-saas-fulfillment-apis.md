---
title: API di evasione SaaS | Azure Marketplace
description: Introduce le versioni delle API di evasione che consentono di integrare le offerte SaaS con Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275731"
---
# <a name="saas-fulfillment-apis"></a>API di evasione ordini SaaS

Le API di evasione SaaS consentono ai fornitori di software indipendenti (ISV) di integrare le applicazioni SaaS con Azure Marketplace. Queste API consentono alle applicazioni ISV di partecipare a tutti i canali abilitati per il commercio: diretto, partner-LED (rivenditore) e LED di campo.  Sono un requisito per elencare le offerte SaaS transazionali in Azure Marketplace.

> [!WARNING]
> La versione corrente di questa API è la versione 2, che deve essere usata per tutte le nuove offerte SaaS.  La versione 1 dell'API è deprecata e viene mantenuta per supportare le offerte esistenti.


## <a name="business-model-support"></a>Supporto del modello aziendale

Questa API supporta le funzionalità del modello aziendale seguenti: Si può:

* Specificare più piani per un'offerta. Questi piani hanno funzionalità diverse e possono avere un prezzo diverso.
* Fornire un'offerta in base al modello di fatturazione per sito o per utente.
* Fornire opzioni di fatturazione mensili e annuali (a pagamento).
* Fornire prezzi privati a un cliente in base a un contratto aziendale negoziato.


## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, registrare l'applicazione SaaS nel [portale di Azure](https://ms.portal.azure.com) come descritto in [registrare un'applicazione Azure ad](./pc-saas-registration.md).  Usare successivamente la versione più recente di questa interfaccia per lo sviluppo: [API di evasione Saas versione 2](./pc-saas-fulfillment-api-v2.md).
