---
title: Documentazione di Azure Operations Management Suite (OMS) | Documentazione Microsoft
description: "Microsoft Operations Management Suite (OMS) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud. Questo articolo identifica i diversi servizi inclusi in OMS e fornisce i collegamenti al contenuto dettagliato."
services: operations-management-suite
author: czeumault
manager: carolz
layout: LandingPage
ms.assetid: 
ms.service: operations-management-suite
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
ms.openlocfilehash: 12f959376d4923e4e2481e37108ade632ac14902
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-operations-management-suite-oms"></a>Informazioni su Operations Management Suite (OMS)
Microsoft Operations Management Suite (OMS) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud.  Poiché OMS viene implementato come servizio basato sul cloud, è possibile renderlo operativo rapidamente con un investimento minimo nei servizi di infrastruttura.  Le nuove funzionalità vengono rese disponibili automaticamente, evitando così i costi di manutenzione e di aggiornamento.

Oltre a offrire importanti servizi in sé, OMS può essere integrato con i componenti di System Center, come System Center Operations Manager, per estendere al cloud gli investimenti per la gestione già esistenti.  System Center e OMS possono essere usati insieme per offrire un'esperienza di gestione ibrida completa.

Le sezioni seguenti forniscono una descrizione di alto livello delle varie aree di valore di OMS e dei servizi che le implementano.  È possibile fare riferimento all'architettura di OMS per una panoramica dei diversi componenti di OMS prima di esaminare la documentazione dettagliata di ciascuno.

## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Insight & Analytics](media/operations-management-suite-overview/icon-insight-analytics.png) Insight & Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) consente di raccogliere, correlare e cercare i dati di prestazioni e log generati da sistemi operativi e applicazioni e di agire su tali dati. Questa analisi consente di ottenere informazioni operative in tempo reale tramite funzionalità di ricerca integrate e dashboard personalizzati per analizzare rapidamente milioni di record in tutti i carichi di lavoro e i server indipendentemente dalla loro posizione fisica.

È possibile aggiungere facilmente a Log Analytics soluzioni che definiscono i dati da raccogliere e la logica per l'analisi.  Le soluzioni possono includere funzionalità aggiuntive che vengono rese automaticamente disponibili agli agenti con una configurazione minima o nulla.  Oltre a usare gli strumenti di analisi forniti dalle singole soluzioni, è possibile eseguire ricerche personalizzate nell'intero set di dati per correlare i dati tra diversi sistemi e applicazioni.  

## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Automation & Control](media/operations-management-suite-overview/icon-automation-control.png) Automation & Control
Automazione di Azure consente di automatizzare i processi amministrativi con [runbook](../automation/automation-runbook-types.md) basati su PowerShell ed eseguiti nel cloud di Azure.  I runbook possono accedere a qualsiasi prodotto o servizio che può essere gestito con PowerShell, incluse risorse in altri cloud come Amazon Web Services (AWS).  I runbook possono inoltre essere eseguiti in un server nel data center locale per gestire le risorse locali.

Automazione di Azure consente la gestione della configurazione con [PowerShell DSC](../automation/automation-dsc-overview.md).  È possibile creare e gestire risorse DSC ospitate in Azure e applicarle a sistemi cloud e locali per definire e implementare automaticamente la loro configurazione.

## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![Protezione e ripristino](media/operations-management-suite-overview/icon-protection-recovery.png) Protezione e ripristino di emergenza
[Backup di Azure](http://azure.microsoft.com/documentation/services/backup) protegge i dati delle applicazioni e li conserva per anni, senza investimenti di capitali e con costi operativi minimi.  Consente di eseguire il backup dei dati da server Windows fisici e virtuali, oltre che dei carichi di lavoro di applicazioni come SQL Server e SharePoint.  Può inoltre essere usato da System Center Data Protection Manager (DPM) per replicare i dati protetti in Azure per la ridondanza e l'archiviazione a lungo termine.

[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) favorisce la continuità aziendale e una strategia di ripristino di emergenza coordinando le operazioni di replica, failover e ripristino di macchine virtuali Hyper-V locali, macchine virtuali VMware e server fisici Windows o Linux. È possibile replicare le macchine in un data center secondario o estendere il data center replicandole in Azure. Site Recovery fornisce inoltre funzionalità per semplificare il failover e il ripristino dei carichi di lavoro. Si integra con i meccanismi di ripristino di emergenza come SQL Server AlwaysOn e prevede piani di ripristino per il failover semplificato dei carichi di lavoro organizzati su più livelli in diversi computer.

## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![Sicurezza e conformità di OMS](media/operations-management-suite-overview/icon-security-compliance.png) Sicurezza e conformità
Le funzionalità di sicurezza e conformità consentono di identificare, valutare e ridurre i rischi di sicurezza per l'infrastruttura.  Queste funzionalità di OMS vengono implementate tramite diverse soluzioni di Log Analytics che analizzano i dati dei log e la configurazione dai sistemi degli agenti per offrire assistenza allo scopo di garantire costantemente la sicurezza dell'ambiente.

* La [soluzione Sicurezza e controllo](oms-security-getting-started.md) raccoglie e analizza gli eventi di sicurezza nei sistemi gestiti per identificare eventuali attività sospette.
* La [soluzione Antimalware](../log-analytics/log-analytics-malware.md) segnala lo stato della protezione antimalware nei sistemi gestiti.  
* La soluzione System Updates esegue un'analisi degli aggiornamenti per la sicurezza e degli altri aggiornamenti nei sistemi gestiti, in modo da facilitare l'identificazione dei sistemi che richiedono l'applicazione di patch.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)
* Informazioni su [Automazione di Azure](../automation/automation-intro.md)
* Informazioni su [Backup di Azure](http://azure.microsoft.com/documentation/services/backup)
* Informazioni su [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery)

