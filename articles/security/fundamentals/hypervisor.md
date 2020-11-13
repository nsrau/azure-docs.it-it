---
title: Sicurezza hypervisor sulla flotta di Azure-sicurezza di Azure
description: Panoramica tecnica sulla sicurezza dell'hypervisor sulla flotta di Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 1f81285e869bd2c65cce29766de0b2bd39a627f6
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557969"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>Sicurezza hypervisor sulla flotta di Azure

Il sistema hypervisor di Azure è basato su Windows Hyper-V. Il sistema hypervisor consente all'amministratore del computer di specificare le partizioni guest con spazi di indirizzi distinti. Gli spazi di indirizzi separati consentono di caricare un sistema operativo e le applicazioni che operano in parallelo del sistema operativo (host) eseguito nella partizione radice del computer. Il sistema operativo host (noto anche come partizione radice con privilegi) ha accesso diretto a tutti i dispositivi fisici e le periferiche del sistema (controller di archiviazione, adattatori di rete). Il sistema operativo host consente alle partizioni guest di condividere l'uso di questi dispositivi fisici esponendo "dispositivi virtuali" a ogni partizione guest. Pertanto, un sistema operativo in esecuzione in una partizione guest ha accesso alle periferiche virtualizzate fornite dai servizi di virtualizzazione in esecuzione nella partizione radice.

L'hypervisor di Azure viene creato tenendo presenti gli obiettivi di sicurezza seguenti:

| Obiettivo | Source (Sorgente) |
|--|--|
| Isolamento | Un criterio di sicurezza non impone il trasferimento di informazioni tra le macchine virtuali. Questo vincolo richiede funzionalità nell'Virtual Machine Manager (VMM) e nell'hardware per l'isolamento della memoria, dei dispositivi, della rete e delle risorse gestite, ad esempio i dati salvati in modo permanente. |
| Integrità di VMM | Per ottenere l'integrità complessiva del sistema, viene stabilita e mantenuta l'integrità dei singoli componenti dell'hypervisor. |
| Integrità della piattaforma | L'integrità dell'hypervisor dipende dall'integrità dell'hardware e del software su cui si basa. Sebbene l'hypervisor non abbia il controllo diretto sull'integrità della piattaforma, Azure si basa su meccanismi hardware e firmware come il chip [Cerberus](project-cerberus.md) per proteggere e rilevare l'integrità della piattaforma sottostante. Non è consentito l'esecuzione di VMM e Guest se l'integrità della piattaforma è compromessa. |
| Accesso limitato | Le funzioni di gestione vengono esercitate solo dagli amministratori autorizzati connessi tramite connessioni sicure. Un principio dei privilegi minimi viene applicato dai meccanismi di controllo degli accessi in base al ruolo (RBAC). |
| Audit | Azure consente alle funzionalità di controllo di acquisire e proteggere i dati relativi a ciò che accade in un sistema, in modo che possa essere ispezionato in un secondo momento. |

L'approccio di Microsoft alla protezione avanzata dell'hypervisor di Azure e del sottosistema di virtualizzazione può essere suddiviso nelle tre categorie seguenti.

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>Limiti di sicurezza fortemente definiti applicati dall'hypervisor

L'hypervisor di Azure impone più limiti di sicurezza tra:

- Partizioni "Guest" virtualizzate e partizione con privilegi ("host")
- Più Guest
- E l'host
- Stesso e tutti i Guest

La riservatezza, l'integrità e la disponibilità sono sicure per i limiti di sicurezza dell'hypervisor. I limiti difendono da un intervallo di attacchi, tra cui le perdite di informazioni del canale laterale, il Denial of Service e l'elevazione dei privilegi.

Il limite di sicurezza dell'hypervisor fornisce anche la segmentazione tra i tenant per il traffico di rete, i dispositivi virtuali, l'archiviazione, le risorse di calcolo e tutte le altre risorse della macchina virtuale.

## <a name="defense-in-depth-exploit-mitigations"></a>Prevenzione degli exploit di difesa in profondità

Nel caso improbabile in cui un limite di sicurezza presenta una vulnerabilità, l'hypervisor di Azure include più livelli di mitigazione, tra cui:

- Isolamento del processo basato su host che ospita componenti tra macchine virtuali
- Sicurezza basata sulla virtualizzazione (VBS) per garantire l'integrità dei componenti in modalità utente e kernel da un ambiente sicuro
- Più livelli di mitigazione degli exploit. Le mitigazioni includono la sequenza casuale del layout dello spazio degli indirizzi (ASLR), la prevenzione dell'esecuzione dei dati (DEP), la protezione arbitraria del flusso di controllo e la prevenzione del danneggiamento dei dati
- Inizializzazione automatica delle variabili dello stack a livello del compilatore
- API kernel che inizializzano automaticamente le allocazioni dell'heap del kernel effettuate da Hyper-V

Queste attenuazioni sono progettate per rendere impossibile lo sviluppo di un exploit per una vulnerabilità tra macchine virtuali.

## <a name="strong-security-assurance-processes"></a>Processi di controllo di sicurezza sicuri

La superficie di attacco correlata all'hypervisor include la rete software, i dispositivi virtuali e tutte le superfici tra macchine virtuali. La superficie di attacco viene rilevata tramite l'integrazione automatica della compilazione, che attiva le verifiche periodiche sulla sicurezza.

Tutte le superfici di attacco della VM sono modellate in base a minacce, codice Recensito, fuzzy e testato dal RED team per violazioni dei limiti di sicurezza. Microsoft dispone di un [programma di Bounty dei bug](https://www.microsoft.com/msrc/bounty-hyper-v) che paga un premio per le vulnerabilità rilevanti nelle versioni dei prodotti idonee per Microsoft Hyper-V.

> [!NOTE]
> Scopri di più sui [processi di controllo di sicurezza sicuri](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes) in Hyper-V.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulle operazioni da eseguire per garantire l'integrità e la sicurezza della piattaforma, vedere:

- [Sicurezza del firmware](firmware.md)
- [Avvio protetto](secure-boot.md)
- [Attestazione di avvio e host misurata](measured-boot-host-attestation.md)
- [Progetto Cerberus](project-cerberus.md)
- [Crittografia di dati inattivi](encryption-atrest.md)