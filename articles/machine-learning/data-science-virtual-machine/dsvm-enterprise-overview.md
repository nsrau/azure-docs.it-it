---
title: Introduzione agli ambienti di collaborazione in team basati su Data Science Virtual Machine - Azure | Microsoft Docs
description: Modelli per la distribuzione di Data Science VM in un ambiente aziendale di collaborazione in team.
keywords: apprendimento avanzato, AI, strumenti di data science, macchina virtuale per data science, analisi geospaziale, processo di data science del team
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 84dfef65c341ca8023b741a98c26081da36e9ff7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085128"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Ambiente di intelligenza artificiale e analisi di collaborazione in team basato su Data Science Virtual Machine 
[Data Science Virtual Machine](overview.md) (DSVM) fornisce un ambiente avanzato nella piattaforma Azure con software precompilato per l'intelligenza artificiale e l'analisi dei dati. 

DSVM è sempre stata usata come desktop di analisi individuale. I data scientist incrementano la produttività con queste nozioni condivise dell'ambiente di analisi precompilato. Quando team di analisi di grandi dimensioni pianificano i propri ambienti di analisi per i data scientist e gli sviluppatori di intelligenza artificiale, uno dei temi ricorrenti riguarda un'infrastruttura di analisi condivisa per sviluppo e sperimentazione. Questa infrastruttura viene gestita in linea con i criteri IT aziendali che agevolano anche la collaborazione e la coerenza nell'intero team di analisi/data science. 

Un'infrastruttura condivisa consente anche all'IT di usare meglio l'ambiente di analisi. Alcune organizzazioni chiamano l'infrastruttura di analisi e data science basata sulla collaborazione in team "sandbox di analisi". Consente ai data scientist di accedere a varie risorse dati per comprendere rapidamente i dati, eseguire esperimenti, convalidare ipotesi e creare modelli predittivi senza influire sull'ambiente di produzione. 

Dato che DSVM opera a livello di infrastruttura di Azure, gli amministratori IT possono facilmente configurarla per operare in conformità ai criteri IT dell'azienda. DSVM offre la massima flessibilità nell'implementazione di diverse architetture di condivisione con accesso ai dati aziendali in modo controllato. 

Questa sezione illustra alcuni modelli e linee guida che è possibile usare per distribuire DSVM come infrastruttura di data science basata sulla collaborazione in team. Gli elementi costitutivi di questi modelli provengono dall'infrastruttura come servizio (IaaS) di Azure e si applicano quindi a tutte le macchine virtuali di Azure. L'obiettivo di questa serie di articoli è l'applicazione di queste funzionalità standard dell'infrastruttura di Azure a Data Science VM. 

Alcuni degli elementi chiave di un ambiente di analisi di un team aziendale sono:

* [Pool con scalabilità automatica di Data Science Virtual Machine](dsvm-pools.md)
* [Identità comune e accesso a un'area di lavoro da qualsiasi DSVM nel pool](dsvm-common-identity.md)
* [Accesso sicuro alle origini dati](dsvm-secure-access-keys.md)


Questa serie di articoli fornisce linee guida e indicazioni per ognuno degli elementi precedenti. Non illustra tutte le esigenze e le considerazioni relative alla distribuzione di DSVM nelle configurazioni aziendali di grandi dimensioni. Ecco altri esempi di documentazione su Azure che è possibile usare durante l'implementazione di istanze di DSVM nell'azienda: 

* [Sicurezza di rete](https://docs.microsoft.com/azure/security/azure-network-security)
* [Monitoraggio](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) e [gestione](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Registrazione e controllo](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Imposizione e impostazione di criteri](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Crittografia](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Governance e individuazione dei dati](https://docs.microsoft.com/azure/data-catalog/)

[Centro architetture di Azure](https://docs.microsoft.com/azure/architecture/) fornisce un'architettura end-to-end dettagliata e modelli per la compilazione e la gestione dell'infrastruttura di analisi basata sul cloud. 
