---
title: Avvisi di sicurezza nel centro sicurezza di Azure | Microsoft Docs
description: In questo argomento vengono illustrati gli avvisi di sicurezza e i diversi tipi disponibili nel centro sicurezza di Azure.
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
ms.date: 8/20/2019
ms.author: v-mohabe
ms.openlocfilehash: d905096432c6e05e5386631480e5cc45b5c890c5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013287"
---
# <a name="security-alerts-in-azure-security-center"></a>Avvisi di sicurezza nel Centro sicurezza di Azure

Nel centro sicurezza di Azure sono disponibili diversi avvisi per molti tipi di risorse diversi. Il Centro sicurezza genera avvisi per le risorse distribuite in Azure e anche per le risorse distribuite in ambienti locali e cloud ibridi. 

## <a name="what-are-security-alerts"></a>Informazioni sugli avvisi di sicurezza

Gli avvisi sono le notifiche generate dal centro sicurezza quando rileva le minacce sulle risorse. Vengono classificate in ordine di priorità ed elenchi gli avvisi, insieme alle informazioni necessarie per analizzare rapidamente il problema. Il Centro sicurezza fornisce anche indicazioni su come correggere un attacco.

## <a name="how-does-security-center-detect-threats"></a>In che modo il Centro sicurezza rileva le minacce?

Per rilevare le minacce reali e ridurre i falsi positivi, il Centro sicurezza raccoglie, analizza e integra i dati di log dalle risorse di Azure e dalla rete. Funziona anche con soluzioni partner connesse, ad esempio soluzioni di protezione endpoint e firewall. Il Centro sicurezza analizza queste informazioni, correlando spesso le informazioni provenienti da più origini, per identificare le minacce.

Il Centro sicurezza monitora le risorse in qualsiasi ambiente. Per altre informazioni sul rilevamento e la risposta alle minacce, vedere [come il Centro sicurezza rileva e risponde alle minacce](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Tipi di avviso di sicurezza

Gli argomenti seguenti illustrano i diversi avvisi in base ai tipi di risorse:

* [Avvisi server e VM IaaS](security-center-alerts-iaas.md)
* [Avvisi di calcolo nativi](security-center-alerts-compute.md)
* [Avvisi di servizi dati](security-center-alerts-data-services.md)

Gli argomenti seguenti illustrano come il Centro sicurezza usa i diversi dati di telemetria raccolti dall'integrazione con l'infrastruttura di Azure, in modo da applicare livelli di protezione aggiuntivi per le risorse distribuite in Azure:

* [Avvisi del livello di servizio](security-center-alerts-service-layer.md)
* [Integrazione con i prodotti di sicurezza di Azure](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>Che cosa sono gli eventi imprevisti della sicurezza?

Un evento imprevisto di sicurezza è una raccolta di avvisi correlati, anziché elencare singolarmente ogni avviso. Il Centro sicurezza usa la correlazione degli avvisi [cloud](security-center-alerts-cloud-smart.md) per correlare avvisi diversi e segnali di fedeltà bassa in eventi imprevisti della sicurezza.

Con gli eventi imprevisti, il Centro sicurezza offre un'unica visualizzazione di una campagna di attacco e di tutti gli avvisi correlati. Questa visualizzazione consente di comprendere rapidamente le azioni intraprese dall'utente malintenzionato e le risorse interessate. Per ulteriori informazioni, vedere [correlazione tra Smart Alert cloud](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Introduzione agli avvisi

Ecco gli articoli che consentono di comprendere meglio le risorse monitorate dal centro sicurezza e forniscono linee guida su come rispondere agli avvisi presentati.

* [Funzionalità e piattaforme supportate dal Centro sicurezza di Azure](security-center-os-coverage.md)  
* [Come gestire gli eventi imprevisti della sicurezza nel centro sicurezza di Azure](security-center-incident.md) 
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md)
* [Convalida degli avvisi nel Centro sicurezza di Azure](security-center-alert-validation.md)  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Eseguire l'aggiornamento al livello standard per i rilevamenti avanzati

Per configurare le funzionalità di rilevamento avanzato, eseguire l'aggiornamento al livello Standard del Centro sicurezza di Azure. 

1. Dal menu **Centro sicurezza** selezionare criteri di **sicurezza**.
2. Per le sottoscrizioni che si desidera spostare nel livello standard, selezionare **Modifica impostazioni**. 
3. Dalla pagina Impostazioni selezionare piano **tariffario**. 
   È disponibile una versione di valutazione gratuita per un mese. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati illustrati i diversi tipi di avvisi disponibili nel centro sicurezza. Per altre informazioni, vedere:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Domande frequenti sul Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-faq)

