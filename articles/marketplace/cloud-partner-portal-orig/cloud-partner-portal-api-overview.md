---
title: Guida di riferimento all'API del portale per i partner cloud Azure Marketplace
description: Descrizione dei prerequisiti per usare ed elencare le operazioni all'API marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 296c667876807bdd05a6281de461df76207b5490
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288547"
---
<a name="cloud-partner-portal-api-reference"></a>Riferimento all'API del portale Cloud Partner
==================================

Le API REST del portale Cloud Partner consentono il recupero programmatico e la manipolazione di carichi di lavoro, offerte e profili di pubblicazione. Le API usano il controllo degli accessi in base al ruolo (RBAC) per applicare le autorizzazioni corrette in fase di elaborazione.

Questo riferimento fornisce i dettagli tecnici per le API REST del portale Cloud Partner. Gli esempi di payload in questo documento sono solo di riferimento e sono soggetti a modifiche in seguito all'aggiunta di nuove funzionalità.


<a name="prerequisites-and-considerations"></a>Prerequisiti e considerazioni
-------------------------------

Prima di usare le API, è opportuno rivedere:

- L'articolo [Prerequisiti](./cloud-partner-portal-api-prerequisites.md) per informazioni su come aggiungere un'entità servizio al proprio account e ottenere un token di accesso di Azure Active Directory (Azure AD) per l'autenticazione. 
- I due [Controllo della concorrenza](./cloud-partner-portal-api-concurrency-control.md).
strategie disponibili per effettuare chiamate alle API.
- API aggiuntiva [Considerazioni](./cloud-partner-portal-api-considerations.md), ad esempio il controllo delle versioni e la gestione degli errori.


<a name="common-tasks"></a>Attività comuni
------------
Questo riferimento illustra nel dettaglio le API per eseguire le seguenti attività comuni.


### <a name="offers"></a>Offerte

-   [Recuperare tutte le offerte](./cloud-partner-portal-api-retrieve-offers.md)
-   [Recupera un'offerta specifica](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Recupero dello stato dell'offerta](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Creare un'offerta](./cloud-partner-portal-api-creating-offer.md)
-   [Pubblicare un'offerta](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operazioni

-   [Recuperare le operazioni](./cloud-partner-portal-api-retrieve-operations.md)
-   [Annullare le operazioni](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Pubblicare un'app

-   [Passare in produzione](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Altre attività

-   [Impostare i prezzi per le offerte di macchine virtuali](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Risoluzione dei problemi

-   [Risoluzione dei problemi di autenticazione](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
