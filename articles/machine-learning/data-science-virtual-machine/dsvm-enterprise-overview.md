---
title: Introduzione agli ambienti di collaborazione in team basati su Data Science Virtual Machine - Azure | Microsoft Docs
description: Modelli per la distribuzione di Data Science VM in un ambiente aziendale di collaborazione in team.
keywords: apprendimento avanzato, AI, strumenti di data science, macchina virtuale per data science, analisi geospaziale, processo di data science del team
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: vijetaj
ms.openlocfilehash: 5f34498fbdacf7fc6e62788913c795ab70ceef23
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991622"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Ambiente di intelligenza artificiale e analisi di collaborazione in team basato su Data Science Virtual Machine 
Il [Data Science Virtual Machine](overview.md) (DSVM) fornisce un ambiente completo sulla piattaforma Azure, con software predefinito per intelligenza artificiale e analisi dei dati.

DSVM è sempre stata usata come desktop di analisi individuale. I singoli data scientist ottengono la produttività con questo ambiente analitico condiviso e precompilato. Poiché i team di analisi di grandi dimensioni pianificano gli ambienti per i propri data scientist e sviluppatori di intelligenza artificiale, uno dei temi ricorrenti è un'infrastruttura di analisi condivisa per lo sviluppo e la sperimentazione. Questa infrastruttura viene gestita in linea con i criteri IT aziendali che facilitano la collaborazione e la coerenza tra i team di data science e di analisi.

Un'infrastruttura condivisa consente un migliore utilizzo IT dell'ambiente di analisi. Alcune organizzazioni chiamano l'infrastruttura data science/Analytics basata sul team di una *sandbox di analisi*. Consente ai data scientist di accedere a vari asset di dati per comprendere rapidamente i dati. Questo ambiente sandbox consente inoltre ai data scientist di eseguire esperimenti, convalidare le ipotesi e creare modelli predittivi senza influire sull'ambiente di produzione.

Dato che DSVM opera a livello di infrastruttura di Azure, gli amministratori IT possono facilmente configurarla per operare in conformità ai criteri IT dell'azienda. Il DSVM offre la massima flessibilità nell'implementazione di diverse architetture di condivisione, offrendo al tempo stesso l'accesso agli asset di dati aziendali in modo controllato.

Questa sezione illustra alcuni modelli e linee guida che è possibile usare per distribuire DSVM come infrastruttura di data science basata sulla collaborazione in team. Poiché i blocchi predefiniti per questi modelli provengono dall'infrastruttura distribuita come servizio (IaaS) di Azure, si applicano a qualsiasi VM di Azure. Questa serie di articoli è incentrata sull'applicazione di queste funzionalità di infrastruttura di Azure standard a DSVM.

I blocchi predefiniti di un ambiente Enterprise team Analytics includono:

* [Un pool di Dsvm con scalabilità automatica](dsvm-pools.md)
* [Identità comune e accesso a un'area di lavoro da qualsiasi DSVM nel pool](dsvm-common-identity.md)
* [Accesso sicuro alle origini dati](dsvm-secure-access-keys.md)


Questa serie fornisce linee guida e puntatori per ciascuno degli argomenti precedenti. Non copre tutte le considerazioni e i requisiti per la distribuzione di Dsvm in configurazioni aziendali di grandi dimensioni. Di seguito sono riportate altre risorse di Azure che è possibile usare durante l'implementazione di istanze DSVM nell'azienda:

* [Sicurezza di rete](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Monitoraggio](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) e [gestione](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Registrazione e controllo](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Imposizione e impostazione di criteri](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Crittografia](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Governance e individuazione dei dati](https://docs.microsoft.com/azure/data-catalog/)

Infine, il [centro architetture di Azure](https://docs.microsoft.com/azure/architecture/) fornisce un'architettura end-to-end dettagliata e modelli per la creazione e la gestione dell'infrastruttura di analisi basata sul cloud.
