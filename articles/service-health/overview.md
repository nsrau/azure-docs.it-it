---
title: Che cos'è Integrità dei servizi di Azure?
description: Informazioni personalizzate su come le app di Azure sono interessate dalla manutenzione e dai problemi attuali e futuri dei servizi di Azure.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: 44b819a88baec383d2faf80ab37edb903b0a5f4d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654087"
---
# <a name="what-is-azure-service-health"></a>Che cos'è Integrità dei servizi di Azure?

Azure offre una serie di esperienze per mantenere l'utente informato sull'integrità delle sue risorse cloud. Queste informazioni includono problemi correnti e imminenti, ad esempio eventi che hanno impatto sui servizi, manutenzione pianificata e altre modifiche che potrebbero influire sulla disponibilità.

Integrità dei servizi di Azure è una combinazione di tre servizi distinti più piccoli.

[Stato di Azure](azure-status-overview.md) segnala le interruzioni dei servizi in Azure nella **[pagina Stato di Azure](https://status.azure.com)** . La pagina offre una visualizzazione globale dell'integrità di tutti i servizi di Azure in tutte le aree di Azure. La pagina Stato è un riferimento valido per gli eventi imprevisti con un impatto di ampio respiro, ma è consigliabile usare Integrità dei servizi di Azure per tenersi informati su eventi imprevisti e attività di manutenzione di Azure.

[Integrità dei servizi di Azure](service-health-overview.md) fornisce una visualizzazione personalizzata dell'integrità dei servizi e delle aree di Azure in uso. Si tratta della risorsa ideale da consultare per le comunicazioni sugli eventi che influiscono sui servizi, come interruzioni, attività di manutenzione pianificata e altri consigli sull'integrità, perché l'esperienza autenticata di Integrità dei servizi di Azure riconosce i servizi e le risorse in uso. Il modo migliore per usare Integrità dei servizi consiste nel configurare gli appositi avvisi per ricevere notifiche tramite i canali di comunicazione preferiti quando i problemi dei servizi, le attività di manutenzione pianificata o altre modifiche potrebbero interessare i servizi e le aree di Azure in uso.

[Integrità risorse di Azure](resource-health-overview.md) fornisce informazioni sull'integrità delle singole risorse cloud, ad esempio una specifica istanza di macchina virtuale. Usando Monitoraggio di Azure, è anche possibile configurare gli avvisi per ricevere notifiche sulle modifiche di disponibilità delle risorse cloud. Integrità risorse di Azure e Monitoraggio di Azure consentono di tenersi informati sulla disponibilità delle risorse minuto per minuto e di valutare rapidamente se un problema è dovuto a un errore locale o a un evento della piattaforma Azure.

Insieme, queste esperienze offrono una visualizzazione completa dello stato di integrità di Azure, al livello di granularità più idoneo per l'utente.

**Guardare una panoramica della pagina Stato di Azure, Integrità dei servizi di Azure e Integrità risorse di Azure**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]