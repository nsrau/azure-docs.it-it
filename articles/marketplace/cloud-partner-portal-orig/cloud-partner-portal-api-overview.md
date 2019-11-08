---
title: Informazioni di riferimento sull'API portale Cloud Partner | Azure Marketplace
description: Descrizione dei prerequisiti per usare ed elencare le operazioni all'API marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b6591e1780d03cbfaff70fbd19ec3dfd274fae79
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819638"
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
-   [Recuperare lo stato di un'offerta](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Creare un'offerta](./cloud-partner-portal-api-creating-offer.md)
-   [Pubblicare un'offerta](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operazioni

-   [Recuperare le operazioni](./cloud-partner-portal-api-retrieve-operations.md)
-   [Annullare le operazioni](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Pubblicare un'app

-   [Passare alla fase operativa](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Altre attività

-   [Impostare i prezzi per le offerte di macchine virtuali](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Risoluzione dei problemi

-   [Risoluzione dei problemi di autenticazione](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
