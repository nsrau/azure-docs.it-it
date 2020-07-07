---
title: Panoramica Integrità risorse del gateway applicazione Azure
description: Questo articolo è una panoramica della funzionalità di integrità delle risorse per applicazione Azure gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67659502"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Panoramica Integrità risorse del gateway applicazione Azure

[Integrità risorse di Azure](../service-health/resource-health-overview.md) aiuta a diagnosticare gli eventuali problemi dei servizi di Azure con effetti negativi sulle risorse e a ottenere il supporto necessario. Informa sull'integrità corrente e passata delle risorse e offre il supporto tecnico necessario per attenuare i problemi.

Per il gateway applicazione, Integrità risorse si basa su segnali emessi dal gateway per valutare se è integro o meno. Se il gateway non è integro, Integrità risorse analizza informazioni aggiuntive per determinare l'origine del problema. Identifica anche le azioni intraprese da Microsoft o le operazioni che è possibile eseguire per risolvere il problema.

Per altri dettagli sulla modalità di valutazione dell'integrità, esaminare l'elenco completo dei tipi di risorse e dei controlli di integrità in [Integrità risorse di Azure](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


Lo stato di integrità del gateway applicazione viene visualizzato come uno degli Stati seguenti:

## <a name="available"></a>Disponibile

Uno stato **disponibile** indica che il servizio non ha rilevato eventi che hanno effetto sull'integrità della risorsa. Verrà visualizzata la notifica **risolta di recente** nei casi in cui il gateway è stato recuperato da un tempo di inattività non pianificato nelle ultime 24 ore.

![Stato di integrità disponibile](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Non disponibile

Uno stato non **disponibile** indica che il servizio ha rilevato un evento piattaforma o non piattaforma in corso che influisca sull'integrità del gateway.

### <a name="platform-events"></a>Eventi piattaforma

Questi eventi vengono attivati da più componenti dell'infrastruttura di Azure. Includono le azioni pianificate (ad esempio, la manutenzione pianificata) e gli eventi imprevisti (ad esempio, il riavvio di un host non pianificato).

Integrità risorse fornisce dettagli aggiuntivi sull'evento e sul processo di ripristino. Consente inoltre di contattare il supporto tecnico, anche se non si dispone di un contratto di supporto Microsoft attivo.

![Stato non disponibile](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Sconosciuto

Lo stato di integrità **sconosciuto** indica che integrità risorse non ha ricevuto informazioni sul gateway per più di 10 minuti. Questo stato non è un'indicazione definitiva dello stato del gateway. Ma si tratta di un punto dati importante nel processo di risoluzione dei problemi.

Se il gateway viene eseguito come previsto, lo stato diventa **disponibile** dopo alcuni minuti.

Se si verificano problemi, lo stato di integrità **sconosciuto** potrebbe indicare che un evento nella piattaforma influisce sul gateway.

![Stato sconosciuto](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Degraded

Lo stato di integrità **danneggiato** indica che il gateway ha rilevato una perdita di prestazioni, sebbene sia ancora disponibile per l'utilizzo.

![Stato degrattugiato](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla risoluzione dei problemi relativi al Web Application Firewall (WAF) del gateway applicazione, vedere [risolvere i problemi relativi a Web Application Firewall (WAF) per applicazione Azure gateway](web-application-firewall-troubleshoot.md).