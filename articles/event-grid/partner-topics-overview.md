---
title: Argomenti partner di Griglia di eventi di Azure
description: Inviare eventi da partner SaaS e PaaS di Griglia di eventi di terze parti direttamente a servizi di Azure con Griglia di eventi di Azure.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 4546874b8a6cd8a7e45c3e6957a5181d66c7433f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83690037"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Argomenti partner in Griglia di eventi di Azure (anteprima)
Gli argomenti partner consentono di connettere origini evento di terze parti direttamente a Griglia di eventi. Questa integrazione consente di sottoscrivere gli eventi dei partner nello stesso modo in cui si sottoscrivono gli eventi dei servizi di Azure. 

## <a name="available-partners"></a>Partner disponibili
Il primo partner disponibile tramite gli argomenti partner di Griglia di eventi è Auth0. L'[argomento partner Auth0](auth0-overview.md) consente di connettere gli account Auth0 e Azure. Grazie a questa integrazione è possibile reagire agli eventi Auth0, registrarli e monitorarli in tempo reale.

È possibile [provare subito](auth0-how-to.md) accedendo all'account Auth0 e creando un'integrazione di Griglia di eventi. Quando si fa clic su Crea in Auth0, nell'account di Azure viene visualizzato un argomento Auth0 in sospeso. Fare clic su Attiva per creare sottoscrizioni di eventi e indirizzare, filtrare e recapitare gli eventi in modo analogo a qualsiasi altra origine evento.

## <a name="pricing"></a>Prezzi
Per gli argomenti partner viene addebitata la stessa tariffa operativa degli argomenti di sistema.

## <a name="limits"></a>Limiti
Gli argomenti partner sono disponibili in anteprima pubblica. Durante la fase di anteprima pubblica, gli argomenti partner sono soggetti agli [stessi limiti](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) degli argomenti di sistema e degli argomenti personalizzati.

## <a name="how-do-i-become-an-event-grid-partner"></a>Come si diventa partner di Griglia di eventi?
L'infrastruttura creata per supportare questo lancio rende facile e veloce per i nuovi partner l'integrazione delle funzionalità di gestione degli eventi personalizzate con Griglia di eventi. Per altre informazioni, [leggere la documentazione sull'onboarding dei partner](partner-onboarding-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- [Argomento partner Auth0](auth0-overview.md)
- [Come usare l'argomento partner Auth0](auth0-how-to.md)
- [Diventare partner di Griglia di eventi](partner-onboarding-overview.md)