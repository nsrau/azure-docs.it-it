---
title: Introduzione agli ambienti di collaborazione in team basati sulle macchine virtuali di data science - Azure | Microsoft Docs
description: Modelli per la distribuzione delle macchine virtuali di data science come ambiente aziendale di collaborazione in team.
keywords: apprendimento avanzato, AI, strumenti di data science, macchina virtuale per data science, analisi geospaziale, processo di data science del team
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 911d6484421cc9fddad0530bf8d9ab4f01d48bf8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Ambiente di intelligenza artificiale e analisi in team basato sulle macchine virtuali di data science 
La [macchina virtuale di data science](overview.md) fornisce un ambiente avanzato nel cloud di Azure con software precompilato per l'intelligenza artificiale e l'analisi dei dati. La macchina virtuale di data science viene usata tradizionalmente come desktop di analisi individuale e i data scientist incrementano la produttività con queste nozioni condivise dell'ambiente di analisi precompilato. Quando team di analisi di grandi dimensioni pianificano i propri ambienti di analisi per i data scientist e gli sviluppatori di intelligenza artificiale, uno dei temi ricorrenti riguarda una infrastruttura condivisa di sviluppo e sperimentazione analitica gestita in linea con i criteri IT aziendali e che agevoli anche la collaborazione e la coerenza nell'intero team di analisi/data science. Un'infrastruttura condivisa consente anche all'IT di usare meglio l'ambiente di analisi. L'infrastruttura di analisi e data science basata sulla collaborazione in team è anche definita da alcune organizzazioni "Sandbox di analisi", che consente ai data scientist di comprendere rapidamente i dati, eseguire esperimenti, convalidare ipotesi, creare modelli predittivi in modo sicuro senza influire sull'ambiente di produzione e avendo accesso a varie risorse di dati. 

Dato che la macchina virtuale di data science opera a livello di infrastruttura di Azure, gli amministratori IT possono facilmente configurarla per operare in conformità ai criteri IT dell'azienda, offrendo la massima flessibilità nell'implementazione di diverse architetture di condivisione con accesso ai dati aziendali in modo controllato. 

Questa sezione illustra alcuni modelli e linee guida che possono essere usati per implementare la macchina virtuale di data science come infrastruttura di data science basata sulla collaborazione in team.  Gli elementi costitutivi di questi modelli provengono direttamente dall'infrastruttura come servizio (IaaS) di Azure e come tali sono applicabili a tutte le macchine virtuali di Azure. L'obiettivo specifico di questa serie di articoli è l'applicazione di queste funzionalità standard dell'infrastruttura di Azure alla macchina virtuale di data science. 

Alcuni degli elementi chiave di un ambiente di analisi di un team aziendale sono:

* [Pool con scalabilità automatica di macchine virtuali di data science](dsvm-pools.md)
* [Identità comune e accesso all'area di lavoro da qualsiasi macchina virtuale di data science nel pool](dsvm-common-identity.md)
* [Accesso sicuro alle origini dati](dsvm-secure-access-keys.md)
* Governance e individuazione di set di dati aziendali e aperti

Questa serie di articoli fornisce linee guida e indicazioni su ognuno di questi aspetti. [Centro architetture Azure](https://docs.microsoft.com/en-us/azure/architecture/) fornisce un'architettura end-to-end più dettagliata per l'infrastruttura di analisi.  
