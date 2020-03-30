---
title: Panoramica di Azure Application Gateway Resource HealthAzure Application Gateway Resource Health overview
description: Questo articolo è una panoramica della funzionalità di integrità delle risorse per il gateway applicazione di AzureThis article is an overview of the resource health feature for Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67659502"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Panoramica di Azure Application Gateway Resource HealthAzure Application Gateway Resource Health overview

[Integrità risorse di Azure](../service-health/resource-health-overview.md) aiuta a diagnosticare gli eventuali problemi dei servizi di Azure con effetti negativi sulle risorse e a ottenere il supporto necessario. Informa sull'integrità corrente e passata delle risorse e offre il supporto tecnico necessario per attenuare i problemi.

Per il gateway applicazione, Integrità risorse si basa sui segnali generati dal gateway per valutare se è integro o meno. Se il gateway non è integro, Integrità risorse analizza informazioni aggiuntive per determinare l'origine del problema. Identifica inoltre le azioni che Microsoft sta intraprendendo o cosa puoi fare per risolvere il problema.

Per altri dettagli sulla modalità di valutazione dell'integrità, esaminare l'elenco completo dei tipi di risorse e dei controlli di integrità in [Integrità risorse di Azure](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


Lo stato di integrità del gateway applicazione viene visualizzato come uno dei seguenti stati:

## <a name="available"></a>Disponibile

Lo stato **Disponibile** indica che il servizio non ha rilevato eventi che influiscono sull'integrità della risorsa. Verrà visualizzata la notifica **Risolto di** recente nei casi in cui il gateway è stato ripristinato da tempi di inattività non pianificati nelle ultime 24 ore.

![Stato di integrità disponibile](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Non disponibile

Uno stato **Non disponibile** indica che il servizio ha rilevato un evento piattaforma o non piattaforma in corso che influisce sull'integrità del gateway.

### <a name="platform-events"></a>Eventi piattaforma

Questi eventi vengono attivati da più componenti dell'infrastruttura di Azure. Includono le azioni pianificate (ad esempio, la manutenzione pianificata) e gli eventi imprevisti (ad esempio, il riavvio di un host non pianificato).

Integrità risorse fornisce dettagli aggiuntivi sull'evento e sul processo di ripristino. Consente inoltre di contattare il supporto tecnico, anche se non si dispone di un contratto di supporto Microsoft attivo.

![Stato non disponibile](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Unknown

Lo stato di integrità **Sconosciuto** indica che Integrità risorse non ha ricevuto informazioni sul gateway per più di 10 minuti. Questo stato non è un'indicazione definitiva dello stato del gateway. Ma è un punto dati importante nel processo di risoluzione dei problemi.

Se il gateway è in esecuzione come previsto, lo stato diventa **Disponibile** dopo alcuni minuti.

Se si verificano problemi, lo stato di integrità **Sconosciuto** potrebbe suggerire che un evento nella piattaforma influisca sul gateway.

![Stato sconosciuto](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Degraded

Lo stato di integrità **Degraded** indica che il gateway ha rilevato una perdita di prestazioni, anche se è ancora disponibile per l'utilizzo.

![Stato rivalutato](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla risoluzione dei problemi relativi a Application Gateway Web Application Firewall (WAF), vedere Risolvere i problemi relativi a [Web Application Firewall (WAF) per il gateway applicazione di Azure.](web-application-firewall-troubleshoot.md)