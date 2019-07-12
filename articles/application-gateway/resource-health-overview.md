---
title: Panoramica su integrità risorse di Gateway applicazione Azure
description: Questo articolo viene fornita una panoramica della funzionalità integrità risorse per il Gateway applicazione di Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659502"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Panoramica su integrità risorse di Gateway applicazione Azure

[Integrità risorse di Azure](../service-health/resource-health-overview.md) aiuta a diagnosticare e ottenere supporto quando un problema del servizio di Azure interessa le risorse. Informa sull'integrità corrente e passata delle risorse e offre il supporto tecnico necessario per attenuare i problemi.

Per il Gateway applicazione, integrità risorse si basa su segnali emessi dal gateway per valutare se è integra o meno. Se il gateway non è integro, integrità risorse analizza informazioni aggiuntive per determinare la causa del problema. Vengono inoltre identificati azioni intraprese da Microsoft o ciò che è possibile eseguire per risolvere il problema.

Per altri dettagli sulla modalità di valutazione dell'integrità, esaminare l'elenco completo dei tipi di risorse e dei controlli di integrità in [Integrità risorse di Azure](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


Lo stato di integrità per il Gateway applicazione viene visualizzato come uno degli stati seguenti:

## <a name="available"></a>Disponibile

Un' **Available** lo stato indica che il servizio non ha rilevato eventi che interessano l'integrità della risorsa. Verrà visualizzato il **risolto recentemente** notifica nei casi in cui il gateway è stato ripristinato dopo il tempo di inattività non pianificato nelle ultime 24 ore.

![Stato di integrità disponibile](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Non disponibile

Un' **disponibile** lo stato indica che il servizio ha rilevato un evento non piattaforma che influisce sull'integrità del gateway o in corso.

### <a name="platform-events"></a>Eventi piattaforma

Questi eventi vengono attivati da più componenti dell'infrastruttura di Azure. Includono le azioni pianificate (ad esempio, la manutenzione pianificata) e gli eventi imprevisti (ad esempio, il riavvio di un host non pianificato).

Integrità risorse fornisce dettagli aggiuntivi sull'evento e sul processo di ripristino. Consente inoltre di contattare il supporto tecnico, anche se non si dispone di un contratto di supporto Microsoft attivo.

![Stato non disponibile](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Sconosciuto

Il **sconosciuto** lo stato di integrità indica l'integrità delle risorse non ha ricevuto informazioni sul gateway per più di 10 minuti. Questo stato non sia un'indicazione definitiva dello stato del gateway. Ma è un punto dati importante nel processo di risoluzione dei problemi.

Se il gateway siano operativi come previsto, lo stato diventa **Available** dopo alcuni minuti.

Se sono stati riscontrati problemi, il **sconosciuto** lo stato di integrità potrebbe suggerire che un evento nella piattaforma influenzano il gateway.

![Stato sconosciuto](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Degraded

Il **Degraded** lo stato di integrità indica il gateway ha rilevato una riduzione delle prestazioni, sebbene sia ancora disponibile per l'utilizzo.

![Stato degrated](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi di Web Application Firewall (WAF) del Gateway applicazione, vedere [risolvere i problemi di Web Application Firewall (WAF) per il Gateway applicazione di Azure](web-application-firewall-troubleshoot.md).