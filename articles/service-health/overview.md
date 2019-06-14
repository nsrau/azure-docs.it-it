---
title: Che cos'è Azure Service Health?
description: Informazioni personalizzate su come le app di Azure sono interessate dalla manutenzione e dai problemi attuali e futuri dei servizi di Azure.
author: stephbaron
ms.author: stbaron
services: service-health
ms.service: service-health
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: 058a171766680f09eaf4de14ddd25235020b1ba4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079574"
---
# <a name="what-is-azure-service-health"></a>Che cos'è Azure Service Health?

Azure offre una suite di esperienze per rimanere informati sull'integrità delle risorse cloud. Queste informazioni includono i problemi correnti e futuri, ad esempio servizio conseguenze per gli eventi di manutenzione pianificata e altre modifiche che potrebbero influire sulla disponibilità.

Integrità servizio di Azure è una combinazione di tre servizi più piccoli distinti.

[Stato di Azure](azure-status-overview.md) informa l'utente di interruzioni del servizio in Azure nel  **[pagina stato di Azure](https://status.azure.com)** . La pagina è una visualizzazione globale dell'integrità di tutti i servizi di Azure in tutte le aree di Azure. La pagina dello stato è un buon riferimento per gli eventi imprevisti con un impatto diffuso, ma è consigliabile che gli utenti di Azure correnti usare integrità servizio di Azure per rimanere informati sugli eventi imprevisti di Azure e la manutenzione.

[Integrità dei servizi Azure](service-health-overview.md) fornisce una visualizzazione personalizzata dell'integrità dei servizi Azure e le aree in uso. Si tratta della risorsa ideale da consultare per le comunicazioni sugli eventi che influiscono sui servizi, come interruzioni, attività di manutenzione pianificata e altri consigli sull'integrità, perché l'esperienza autenticata di Integrità dei servizi di Azure riconosce i servizi e le risorse in uso. Il modo migliore per usare Integrità dei servizi consiste nel configurare gli appositi avvisi per ricevere notifiche tramite i canali di comunicazione preferiti quando i problemi dei servizi, le attività di manutenzione pianificata o altre modifiche potrebbero interessare i servizi e le aree di Azure in uso.

[Integrità risorse di Azure](resource-health-overview.md) fornisce informazioni sull'integrità delle risorse cloud singole, ad esempio un'istanza di macchina virtuale specifica. Usando Monitoraggio di Azure, è anche possibile configurare gli avvisi per ricevere notifiche sulle modifiche di disponibilità delle risorse cloud. Integrità risorse di Azure e Monitoraggio di Azure consentono di tenersi informati sulla disponibilità delle risorse minuto per minuto e di valutare rapidamente se un problema è dovuto a un errore locale o a un evento della piattaforma Azure.

Insieme, queste esperienze offrono una visualizzazione completa dello stato di integrità di Azure, al livello di granularità più idoneo per l'utente.

**Guarda una panoramica della pagina stato di Azure, integrità dei servizi Azure e integrità risorse di Azure**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]
