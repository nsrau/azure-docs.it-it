---
title: Informazioni aggiuntive su Azure | Documentazione Microsoft
description: Procedure consigliate e informazioni aggiuntive su Azure
services: 
documentationcenter: na
author: bennage
manager: marksou
editor: 
tags: 
ms.assetid: de94c74a-fea7-4815-8484-553e421a7490
ms.service: guidance
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: christb
translationtype: Human Translation
ms.sourcegitcommit: 5f3ced657cf3d6587a63789b3dd3ca41cd2856f0
ms.openlocfilehash: 0061e1ff2ae2d6b8ed7b7c3bb60405e76d4cc91b


---
# <a name="azure-guidance"></a>Informazioni aggiuntive su Azure
[!INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Il team Microsoft Patterns & Practices fa parte del team Azure Customer Advisory e si propone di aiutare sviluppatori, architetti e professionisti IT a usare in modo ottimale la piattaforma Microsoft Azure. Il team produce informazioni aggiuntive che illustrano le procedure consigliate per la creazione di soluzioni cloud in Azure.

## <a name="checklists"></a>Checklists
Questi elenchi costituiscono un riferimento rapido per la verifica degli aspetti fondamentali della disponibilità e della scalabilità. 

* [Elenco di controllo della disponibilità][AvailabilityChecklist] 
  
    Riepilogo delle procedure consigliate per assicurare la disponibilità.
* [Elenco di controllo per la scalabilità][ScalabilityChecklist]
  
    Riepilogo delle procedure consigliate per la progettazione e l'implementazione di servizi scalabili e per la gestione dati.

## <a name="best-practices-articles"></a>Articoli sulle procedure consigliate
Questi articoli forniscono un'analisi approfondita dei concetti principali associati comunemente al cloud computing. 

* [Progettazione API][APIDesign] 
  
    Analisi dei problemi di progettazione da tenere in considerazione durante la progettazione di un'API Web.
* [Implementazione API][APIImplementation] 
  
    Insieme di procedure consigliate per l'implementazione e la pubblicazione di un'API Web.
* [Informazioni aggiuntive sulla sicurezza delle API](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 
  
    Analisi dei problemi relativi all'autenticazione e all'autorizzazione, ad esempio i tipi di token, i protocolli di autorizzazione, i flussi di autorizzazione e l'attenuazione delle minacce.
* [Informazioni aggiuntive sulla scalabilità automatica][AutoscalingGuidance] 
  
    Riepilogo delle considerazioni sulle soluzioni di scalabilità che non richiedono intervento manuale.
* [Informazioni aggiuntive sui processi in background][BackgroundJobsGuidance] 
  
    Descrizione delle opzioni disponibili e delle procedure consigliate per l'implementazione delle attività che devono essere eseguite in background, indipendentemente da eventuali operazioni in primo piano o interattive.
* [Informazioni aggiuntive sulla rete CDN][CDNGuidance] 
  
    Informazioni aggiuntive generali e procedure consigliate sull'uso della rete CDN per ridurre al minimo il carico sulle applicazioni e massimizzare la disponibilità e le prestazioni.
* [Informazioni aggiuntive sulla memorizzazione nella cache][CachingGuidance] 
  
    Riepilogo di indicazioni su come usare la memorizzazione nella cache per migliorare le prestazioni e la scalabilità di un sistema.
* [Informazioni aggiuntive sul partizionamento di dati][DataPartitioningGuidance]
  
    Strategie utili per partizionare i dati in modo da migliorare la scalabilità, ridurre i conflitti e ottimizzare le prestazioni.
* [Informazioni aggiuntive su monitoraggio e diagnostica][MonitoringandDiagnosticsGuidance] 
  
    Informazioni aggiuntive per rilevare il modo in cui gli utenti usano il sistema, tracciare l'utilizzo delle risorse e, in generale, monitorare l'integrità e le prestazioni del sistema stesso.
* [Convenzioni di denominazione consigliate][naming-conventions] 
  
    Convenzioni di denominazione consigliate per le risorse di Azure.
* [Informazioni aggiuntive generali sulla ripetizione di tentativi][RetryGeneralGuidance] 
  
    Analisi dei concetti generali per la gestione degli errori temporanei.
* [Informazioni aggiuntive specifiche sul servizio per la ripetizione di tentativi][RetryServiceSpecificGuidance]
  
    Riepilogo delle funzionalità di ripetizione dei tentativi per molti servizi di Azure, incluse informazioni utili per usare, adattare o estendere il meccanismo di ripetizione dei tentativi per ogni servizio.

## <a name="scenario-guides"></a>Guide relative agli scenari
* [Esecuzione di Elasticsearch in Azure][elasticsearch] 
  
    Elasticsearch è un motore di ricerca e un database open source altamente scalabile. È adatto a situazioni che richiedono velocità nell'analisi e nell'individuazione di informazioni in set di dati di grandi dimensioni. Queste informazioni aggiuntive esaminano alcuni aspetti importanti da considerare durante la progettazione di un cluster Elasticsearch.
* [Gestione delle identità per le applicazioni multi-tenant][identity-multitenant] 
  
    La multi-tenancy è un'architettura in cui più tenant condividono un'applicazione ma sono isolati gli uni dagli altri. Queste informazioni aggiuntive illustrano come gestire le entità utente in un'applicazione multi-tenant usando [Azure Active Directory][AzureAD] per gestire l'accesso e l'autenticazione.
* [Sviluppo di soluzioni Big Data](https://msdn.microsoft.com/library/dn749874.aspx)
  
    Questa guida esamina l'uso di HDInsight per scenari quali l'esplorazione iterativa, un data warehouse, per processi ETL e l'integrazione in sistemi di Business Intelligence esistenti. Include anche informazioni aggiuntive utili per comprendere i concetti relativi ai Big Data, alla pianificazione e alla progettazione di soluzioni Big Data e all'implementazione di queste soluzioni.

## <a name="patterns"></a>Modelli
* [Modelli di progettazione cloud: informazioni aggiuntive prescrittive sull'architettura per le applicazioni cloud](https://msdn.microsoft.com/library/dn568099.aspx)
  
    Cloud Design Patterns è una libreria di modelli di progettazione e di argomenti informativi correlati. Illustra i vantaggi dell'applicazione di modelli descrivendo il ruolo specifico di ogni elemento nelle architetture per le applicazioni cloud.
* [Ottimizzazione delle prestazioni per le applicazioni cloud](https://github.com/mspnp/performance-optimization)
  
    Queste informazioni aggiuntive illustrano gli anti-modelli comuni che impediscono la scalabilità delle app in condizioni di carico. Include esempi che illustrano otto anti-modelli, una [panoramica dell'analisi delle prestazioni](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) e una guida per la [valutazione delle prestazioni rispetto a metriche chiave](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Architetture di riferimento
Le architetture di riferimento sono organizzate in base allo scenario.
Ogni singola architettura include procedure consigliate, procedure complete e un componente eseguibile che incorpora i suggerimenti.

La libreria corrente di architetture di riferimento è disponibile all'indirizzo [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>Informazioni aggiuntive sulla resilienza
Questi argomenti illustrano come progettare applicazioni con resilienza in caso di errore in un ambiente cloud distribuito.   

* [Panoramica della resilienza][ResiliencyOvervew]
  
     Illustra come sviluppare applicazioni nella piattaforma Azure in grado di recuperare in caso di errore e di continuare a funzionare. Descrive un approccio strutturato per ottenere la resilienza, dalla progettazione all'implementazione, alla distribuzione e alle operazioni.
* [Elenco di controllo per la resilienza][resiliency-checklist]
  
    Elenco di controllo di raccomandazioni utili per pianificare svariate modalità di errore che possono verificarsi.
* [Analisi della modalità di errore][resiliency-fma] 
  
    L'analisi della modalità di errore è un processo che consente di ottenere la resilienza in un sistema, identificando possibili punti di errore. Come punto di partenza per il processo di analisi della modalità di errore, questo articolo include un catalogo di potenziali modalità di errore e delle relative attenuazioni. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md




<!--HONumber=Dec16_HO1-->


