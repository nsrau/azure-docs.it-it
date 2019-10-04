---
title: Panoramica di Integrità risorse di Azure | Microsoft Docs
description: Panoramica su Integrità risorse di Azure
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 05/10/2019
ms.openlocfilehash: 9c2096f94f38d13288c6ce3742252bc6d576835a
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854220"
---
# <a name="resource-health-overview"></a>Panoramica di Integrità risorse
 
Integrità risorse di Azure consente di diagnosticare e ottenere supporto per i problemi del servizio che interessano le risorse di Azure. Segnala l'integrità corrente e passata delle risorse.

Report [sullo stato di Azure](https://status.azure.com) sui problemi del servizio che interessano una vasta gamma di clienti di Azure. Integrità risorse offre un dashboard personalizzato dell'integrità delle risorse. Integrità risorse Mostra tutte le volte in cui le risorse non sono state disponibili a causa di problemi dei servizi di Azure. Questi dati semplificano la verifica della violazione di un contratto di contratto.

## <a name="resource-definition-and-health-assessment"></a>Definizione di risorsa e valutazione dell'integrità

Una *risorsa* è un'istanza specifica di un servizio di Azure, ad esempio una macchina virtuale, un'app Web o un database SQL. Integrità risorse si basa sui segnali di diversi servizi di Azure per valutare se una risorsa è integra. Se una risorsa non è integra, Integrità risorse analizza informazioni aggiuntive per determinare l'origine del problema. Segnala anche le azioni intraprese da Microsoft per risolvere il problema e identifica le operazioni che è possibile eseguire per risolverlo.

Per ulteriori informazioni sulla valutazione dell'integrità, vedere l'elenco dei tipi di risorse e dei controlli di integrità in [integrità risorse di Azure](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Stato di integrità

L'integrità di una risorsa viene visualizzata come uno degli stati seguenti.

### <a name="available"></a>Disponibile

*Disponibile* significa che non sono stati rilevati eventi che influiscono sull'integrità della risorsa. Nei casi in cui la risorsa è stata ripristinata da un tempo di inattività non pianificato nelle ultime 24 ore, verrà visualizzata una notifica di "risolto di recente".

![Stato * disponibile * per una macchina virtuale con una notifica di "risolto recentemente"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Non disponibile

Non *disponibile* significa che il servizio ha rilevato una piattaforma o un evento non della piattaforma in corso che influiscono sull'integrità della risorsa.

#### <a name="platform-events"></a>Eventi piattaforma

Questi eventi vengono attivati da più componenti dell'infrastruttura di Azure. Includono le azioni pianificate (ad esempio, la manutenzione pianificata) e gli eventi imprevisti (ad esempio, il riavvio di un host non pianificato).

Integrità risorse fornisce dettagli aggiuntivi sull'evento e sul processo di ripristino. Consente inoltre di contattare supporto tecnico Microsoft anche se non si dispone di un contratto di supporto attivo.

![Stato * non disponibile * per una macchina virtuale a causa di un evento Platform](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventi non piattaforma

Gli eventi non di piattaforma vengono attivati dalle azioni dell'utente. Gli esempi includono l'arresto di una macchina virtuale o il raggiungimento del numero massimo di connessioni alla cache di Azure per Redis.

![Stato "non disponibile" per una macchina virtuale a causa di un evento non piattaforma](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Sconosciuto

*Unknown* significa che integrità risorse non ha ricevuto informazioni sulla risorsa per più di 10 minuti. Sebbene questo stato non sia un'indicazione definitiva dello stato della risorsa, è un punto dati importante per la risoluzione dei problemi.

Se la risorsa viene eseguita come previsto, lo stato della risorsa verrà aggiornato in *Disponibile* dopo alcuni minuti.

Se si verificano problemi con la risorsa, lo stato di integrità *sconosciuto* potrebbe indicare che un evento nella piattaforma influisce sulla risorsa.

![Stato * sconosciuto * per una macchina virtuale](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degraded

Ridotto *significa che* la risorsa ha rilevato una perdita di prestazioni, sebbene sia ancora disponibile per l'uso.

Diverse risorse hanno criteri propri quando segnalano che sono degradate.

![Stato * danneggiato * per una macchina virtuale](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Segnalazione di uno stato non corretto

Se si ritiene che lo stato di integrità corrente non sia corretto, è possibile indicare selezionando **segnala stato di integrità non corretto**. Nei casi in cui si influisce su un problema di Azure, si consiglia di contattare il supporto tecnico da Integrità risorse.

![Modulo per l'invio di informazioni sullo stato errato](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Informazioni cronologia

È possibile accedere a 14 giorni di cronologia nella sezione relativa alla **cronologia di integrità** di integrità risorse.

![Elenco di eventi di Integrità risorse nelle ultime due settimane](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Attività iniziali

Per aprire Integrità risorse per una risorsa:

1. Accedere al portale di Azure.
2. Passare alla risorsa.
3. Nel menu della risorsa nel riquadro a sinistra selezionare **Integrità risorsa**.

![Apertura di Integrità risorse dalla visualizzazione delle risorse](./media/resource-health-overview/from-resource-blade.png)

È anche possibile accedere a Integrità risorse selezionando **Tutti i servizi** e digitando **Integrità risorse** nella casella di testo di filtro. Nel riquadro **Guida e supporto** selezionare [Integrità risorse](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Apertura di Integrità risorse da "Tutti i servizi"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Integrità risorse, consultare i riferimenti seguenti:
-  [Tipi di risorse e controlli di integrità in Integrità risorse di Azure](resource-health-checks-resource-types.md)
-  [Domande frequenti su Integrità risorse di Azure](resource-health-faq.md)
