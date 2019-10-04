---
title: Ambiente di intelligenza artificiale e analisi di collaborazione in team
titleSuffix: Azure Data Science Virtual Machine
description: Modelli per la distribuzione di Data Science VM in un ambiente aziendale di collaborazione in team.
keywords: apprendimento avanzato, AI, strumenti di data science, macchina virtuale per data science, analisi geospaziale, processo di data science del team
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 06d05d6d410af13bfbe85f3cb66523c1d48cb77c
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195660"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Ambiente di intelligenza artificiale e analisi di collaborazione in team basato su Data Science Virtual Machine 
[Data Science Virtual Machine](overview.md) (DSVM) fornisce un ambiente avanzato nella piattaforma Azure, con software precompilato per l'intelligenza artificiale e l'analisi dei dati.

DSVM è sempre stata usata come desktop di analisi individuale. I data scientist incrementano la produttività con questo ambiente di analisi condiviso e precompilato. Quando team di analisi di grandi dimensioni pianificano i propri ambienti per i data scientist e gli sviluppatori di intelligenza artificiale, uno dei temi ricorrenti riguarda un'infrastruttura di analisi condivisa per sviluppo e sperimentazione. Questa infrastruttura viene gestita in linea con i criteri IT aziendali che agevolano anche la collaborazione e la coerenza nell'intero team di analisi e data science.

Un'infrastruttura condivisa consente anche un migliore utilizzo dell'ambiente di analisi in ambito IT. In alcune organizzazioni l'infrastruttura di analisi e data science basata sulla collaborazione in team viene definita *sandbox di analisi*. Questo ambiente sandbox consente ai data scientist di accedere a varie risorse dati per comprendere rapidamente i dati, nonché di eseguire esperimenti, convalidare ipotesi e creare modelli predittivi senza influire sull'ambiente di produzione.

Dato che DSVM opera a livello di infrastruttura di Azure, gli amministratori IT possono facilmente configurarla per operare in conformità ai criteri IT dell'azienda. DSVM offre la massima flessibilità nell'implementazione di diverse architetture di condivisione offrendo al contempo accesso controllato ai dati aziendali.

Questa sezione illustra alcuni modelli e linee guida che è possibile usare per distribuire DSVM come infrastruttura di data science basata sulla collaborazione in team. Dal momento che gli elementi costitutivi di questi modelli provengono dall'infrastruttura come servizio (IaaS) di Azure, applicano a tutte le macchine virtuali di Azure. L'obiettivo di questa serie di articoli è l'applicazione di queste funzionalità standard dell'infrastruttura di Azure alla DSVM.

Gli elementi costitutivi chiave di un ambiente di analisi di un team aziendale includono:

* [Un pool di Data Science Virtual Machine con scalabilità automatica](dsvm-pools.md)
* [Identità comune e accesso a un'area di lavoro da qualsiasi DSVM nel pool](dsvm-common-identity.md)
* [Accesso sicuro alle origini dati](dsvm-secure-access-keys.md)


Questa serie di articoli fornisce linee guida e indicazioni per ognuno degli argomenti precedenti. Non illustra tutti i requisiti e le considerazioni relative alla distribuzione di Data Science Virtual Machine nelle configurazioni aziendali di grandi dimensioni. Ecco altre risorse di Azure che è possibile usare durante l'implementazione di istanze di Data Science Virtual Machine nell'azienda:

* [Sicurezza di rete](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Monitoraggio](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) e [gestione](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Registrazione e controllo](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Imposizione e impostazione di criteri](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Crittografia](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Governance e individuazione dei dati](https://docs.microsoft.com/azure/data-catalog/)

[Centro architetture di Azure](https://docs.microsoft.com/azure/architecture/) fornisce infine un'architettura end-to-end dettagliata e modelli per la compilazione e la gestione dell'infrastruttura di analisi basata sul cloud.