---
title: Avvisi di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
description: Questo argomento viene illustrato che cosa sono gli avvisi di sicurezza e i diversi tipi disponibili nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 06f41bbfd97d5deb59e7bfd08615b2f28e256070
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571673"
---
# <a name="security-alerts-in-azure-security-center"></a>Avvisi di sicurezza nel Centro sicurezza di Azure

Questo articolo presenta i diversi tipi di avvisi di sicurezza disponibili in Azure Security Center (Centro sicurezza di AZURE). Esistono una serie di avvisi per i molti tipi di risorse diverso. Genera avvisi per entrambe le risorse distribuite in Azure nel Centro sicurezza di AZURE e le risorse distribuite in locale e ibrida ambienti cloud. 

## <a name="what-are-security-alerts"></a>Informazioni sugli avvisi di sicurezza

Gli avvisi sono notifiche che il Centro sicurezza viene generato l'errore quando rileva le minacce sulle risorse. Si assegna la priorità e vengono elencati gli avvisi e le informazioni necessarie per poter analizzare rapidamente il problema. Il Centro sicurezza fornisce inoltre consigli per come è possibile risolvere un attacco.

## <a name="how-does-security-center-detect-threats"></a>In che modo Centro sicurezza di rilevare le minacce?

Per rilevare le minacce reali e ridurre i falsi positivi, il Centro sicurezza raccoglie, analizza e integra i dati di log da risorse di Azure, la rete e soluzioni dei partner connesse, ad esempio firewall e soluzioni di protezione endpoint. Il Centro sicurezza analizza queste informazioni, correlando spesso quelle raccolte da più origini, per identificare le minacce.

Centro sicurezza di AZURE consente di monitorare le risorse se distribuito in Azure o distribuita in altri siti locali e ibridi ambienti cloud. Per altre informazioni su come rilevare e rispondere alle minacce, vedere [come il Centro sicurezza rileva e risponde alle minacce](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Tipi di avviso di sicurezza

Gli argomenti seguenti consentono di eseguire i diversi avvisi Centro sicurezza di AZURE in base ai tipi di risorse:

* [Avvisi delle macchine virtuali IaaS & server](security-center-alerts-iaas.md)
* [Avvisi di calcolo nativo](security-center-alerts-compute.md)
* [Servizi dati-avvisi](security-center-alerts-data-services.md)

Gli argomenti seguenti illustrano come il Centro sicurezza Usa i dati di telemetria diversi da esso raccolti dall'integrazione con l'infrastruttura di Azure per poter applicare i livelli di protezione aggiuntiva per le risorse distribuite in Azure:

* [Avvisi di livello di servizio](security-center-alerts-service-layer.md)
* [Integrazione con prodotti di sicurezza di Azure](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>Quali sono gli eventi imprevisti degli avvisi?

Un evento imprevisto della sicurezza è una raccolta di avvisi correlati, anziché elencare ogni avviso singolarmente. Centro sicurezza Usa fusion da correlare a ogni avviso e i segnali di bassa fedeltà in eventi imprevisti della sicurezza.

Usando gli eventi imprevisti, il Centro sicurezza offre una singola visualizzazione di una campagna di attacco e di tutti gli avvisi correlati. In questa vista consente di comprendere rapidamente le azioni dall'utente malintenzionato e le risorse interessate. Per altre informazioni, vedere [correlazione degli avvisi di Cloud intelligente](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Iniziare con gli avvisi

Vedere gli argomenti seguenti, per comprendere a fondo le risorse monitorate dal Centro sicurezza di AZURE e per le linee guida su come rispondere agli avvisi presentati dal Centro sicurezza di AZURE.

* Per informazioni su quali piattaforme e le funzionalità sono protette dal Centro sicurezza di AZURE, vedere [funzionalità supportate dal Centro sicurezza di Azure e piattaforme](security-center-os-coverage.md).  
* Per comprendere quali sono gli incidenti di sicurezza e modalità di risposta di Centro sicurezza di AZURE ad essi, vedere [modalità di gestione eventi imprevisti della sicurezza nel Centro sicurezza Azure](security-center-incident.md). 
* Per informazioni su come gestire gli avvisi viene visualizzato, vedere [gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md).
* Per informazioni su come verificare che il Centro sicurezza sia configurato correttamente e per simulare un avviso di test, vedere [la convalida degli avvisi nel Centro sicurezza di Azure](security-center-alert-validation.md).  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Eseguire l'aggiornamento al piano Standard per funzionalità di rilevamento avanzato

Per configurare le funzionalità di rilevamento avanzato, eseguire l'aggiornamento al livello Standard del Centro sicurezza di Azure. 

1. Dal menu del Centro sicurezza, selezionare **criteri di sicurezza**.
2. Per le sottoscrizioni che si desidera passare al livello Standard, fare clic su **modificare le impostazioni**. 
3. Nella pagina Impostazioni, scegliere **piano tariffario**. 
   Una versione di valutazione gratuita è disponibile per un mese. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso che cosa sono gli avvisi di sicurezza e i diversi tipi di avvisi disponibili nel Centro sicurezza. Per altre informazioni, vedere i seguenti argomenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Domande frequenti sul Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-faq): Domande frequenti sull'uso del servizio.

