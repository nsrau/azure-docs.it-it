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
ms.openlocfilehash: 6a755ef4d933046377a6a25be76655b44f4bf508
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361376"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Ambiente di intelligenza artificiale e analisi in team basato sulle macchine virtuali di data science 
La [macchina virtuale di data science](overview.md) fornisce un ambiente avanzato nel cloud di Azure con software precompilato per l'intelligenza artificiale e l'analisi dei dati. La macchina virtuale di data science viene usata tradizionalmente come desktop di analisi individuale e i data scientist incrementano la produttività con queste nozioni condivise dell'ambiente di analisi precompilato. Quando team di analisi di grandi dimensioni pianificano i propri ambienti di analisi per i data scientist e gli sviluppatori di intelligenza artificiale, uno dei temi ricorrenti riguarda una infrastruttura condivisa di sviluppo e sperimentazione analitica gestita in linea con i criteri IT aziendali e che agevoli anche la collaborazione e la coerenza nell'intero team di analisi/data science. Un'infrastruttura condivisa consente anche all'IT di usare meglio l'ambiente di analisi. L'infrastruttura di analisi e data science basata sulla collaborazione in team è anche definita da alcune organizzazioni "Sandbox di analisi", che consente ai data scientist di comprendere rapidamente i dati, eseguire esperimenti, convalidare ipotesi, creare modelli predittivi in modo sicuro senza influire sull'ambiente di produzione e avendo accesso a varie risorse di dati. 

Dato che la macchina virtuale di data science opera a livello di infrastruttura di Azure, gli amministratori IT possono facilmente configurarla per operare in conformità ai criteri IT dell'azienda, offrendo la massima flessibilità nell'implementazione di diverse architetture di condivisione con accesso ai dati aziendali in modo controllato. 

Questa sezione illustra alcuni modelli e linee guida che possono essere usati per implementare la macchina virtuale di data science come infrastruttura di data science basata sulla collaborazione in team.  Gli elementi costitutivi di questi modelli provengono direttamente dall'infrastruttura come servizio (IaaS) di Azure e come tali sono applicabili a tutte le macchine virtuali di Azure. L'obiettivo specifico di questa serie di articoli è l'applicazione di queste funzionalità standard dell'infrastruttura di Azure alla macchina virtuale di data science. 

Alcuni degli elementi chiave di un ambiente di analisi di un team aziendale sono:

* [Pool con scalabilità automatica di macchine virtuali di data science](dsvm-pools.md)
* [Identità comune e accesso all'area di lavoro da qualsiasi macchina virtuale di data science nel pool](dsvm-common-identity.md)
* [Accesso sicuro alle origini dati](dsvm-secure-access-keys.md)


Questa serie di articoli fornisce linee guida e indicazioni su ognuno di questi aspetti sopra indicati. Ovviamente, esistono diverse esigenze e considerazioni aggiuntive quando si distribuisce DSVM nelle configurazioni enterprise di grandi dimensioni che non sono ancora direttamente descritte in questa serie di articoli. Ecco alcune altre considerazioni e indicatori di misura per la documentazione di Azure generale che possono essere facilmente usati durante l'implementazione nelle istanze DSVM nell'azienda. 

* [Sicurezza di rete](https://docs.microsoft.com/azure/security/azure-network-security)
* [Monitoraggio](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) e [Gestione](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Registrazione e controllo](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Imposizione e impostazione di criteri](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Anti-malware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Crittografia](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Governance e individuazione dei dati](https://docs.microsoft.com/azure/data-catalog/)

Il [centro architettura di Azure](https://docs.microsoft.com/en-us/azure/architecture/) è anche un'ottima risorsa che fornisce un'architettura end-to-end dettagliata e dei modelli per la compilazione e la gestione dell'infrastruttura analitica basata su cloud. 
