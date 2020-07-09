---
title: Argomenti partner di Griglia di eventi di Azure
description: Inviare eventi da partner SaaS e PaaS di Griglia di eventi di terze parti direttamente a servizi di Azure con Griglia di eventi di Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 97829f262a00e2353624b2a3fa9df210518ad3a0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115996"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Argomenti del partner in griglia di eventi di Azure (anteprima)
Con gli argomenti dei partner, è possibile connettere origini eventi di terze parti direttamente a griglia di eventi di Azure. Questa integrazione consente di sottoscrivere gli eventi dei partner nello stesso modo in cui si sottoscrivono gli eventi dei servizi di Azure. 

## <a name="available-partners"></a>Partner disponibili
Il primo partner disponibile tramite gli argomenti del partner di griglia di eventi è Auth0. È possibile usare l' [argomento partner Auth0](auth0-overview.md) per connettere gli account Auth0 e Azure. L'integrazione consente di reagire, registrare e monitorare gli eventi Auth0 in tempo reale.

[Per provarlo](auth0-how-to.md), accedere all'account Auth0 e creare un'integrazione di griglia di eventi. Dopo aver selezionato **Crea** in Auth0, viene visualizzato un argomento Auth0 in sospeso nell'account Azure. Selezionare **Activate (attiva**) ed è possibile creare sottoscrizioni di griglia di eventi per indirizzare, filtrare e recapitare gli eventi in modo analogo a qualsiasi altra origine evento.

## <a name="pricing"></a>Prezzi
Per gli argomenti partner viene addebitata la stessa tariffa operativa degli argomenti di sistema.

## <a name="limits"></a>Limiti
Gli argomenti del partner sono disponibili in anteprima pubblica. Durante l'anteprima pubblica, gli argomenti dei partner sono soggetti agli [stessi limiti](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) degli argomenti di sistema e degli argomenti personalizzati.

## <a name="how-do-i-become-an-event-grid-partner"></a>Come si diventa partner di Griglia di eventi?
L'infrastruttura creata per supportare questo lancio rende facile e veloce per i nuovi partner l'integrazione delle funzionalità di gestione degli eventi personalizzate con Griglia di eventi. Per ulteriori informazioni, vedere la [documentazione sull'onboarding dei partner](partner-onboarding-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- [Argomento del partner Auth0](auth0-overview.md)
- [Come usare l'argomento partner Auth0](auth0-how-to.md)
- [Diventare partner di Griglia di eventi](partner-onboarding-overview.md)