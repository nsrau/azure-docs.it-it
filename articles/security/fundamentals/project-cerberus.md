---
title: Integrità del firmware-sicurezza di Azure
description: Informazioni sulle misurazioni crittografiche per garantire l'integrità del firmware.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f085858a9d550623704efd4f051ed525e55a37e0
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557856"
---
# <a name="project-cerberus"></a>Progetto Cerberus

Cerbero è una radice hardware conforme a NIST 800-193 con un'identità che non può essere clonata. Cerbero è progettato per aumentare ulteriormente il comportamento di sicurezza dell'infrastruttura di Azure, offrendo un solido ancoraggio di attendibilità per l'integrità del firmware.

## <a name="enabling-an-anchor-of-trust"></a>Abilitazione di un ancoraggio di attendibilità
Ogni chip Cerberus ha un'identità di crittografia univoca stabilita usando una catena di certificati firmata con una radice di un'autorità di certificazione Microsoft (CA). Le misurazioni ottenute da Cerbero possono essere usate per convalidare l'integrità dei componenti, ad esempio:

- Host
- Baseboard Management Controller (BMC)
- Tutte le periferiche, incluse le schede di interfaccia di rete e [System-on-a-chip](https://en.wikipedia.org/wiki/System_on_a_chip) (SOC)

Questo ancoraggio di attendibilità contribuisce a difendere il firmware della piattaforma da:

- File binari del firmware compromessi in esecuzione sulla piattaforma
- Malware e hacker che sfruttano i bug del sistema operativo, dell'applicazione o dell'hypervisor
- Determinati tipi di attacchi Supply Chain (Manufacturing, assembly, Transit)
- Insider dannosi con privilegi amministrativi o accesso all'hardware

## <a name="cerberus-attestation"></a>Attestazione Cerberus
Cerbero autentica l'integrità del firmware per i componenti server usando un manifesto della piattaforma firmware (PFM). PFM definisce un elenco di versioni del firmware autorizzato e fornisce una misurazione della piattaforma per il [servizio di attestazione host](measured-boot-host-attestation.md)di Azure. Il servizio di attestazione host convalida le misurazioni e determina la possibilità di aggiungere solo gli host attendibili ai carichi di lavoro dei clienti host e della flotta di Azure.

Insieme al servizio di attestazione host, le funzionalità di Cerbero migliorano e innalzano di livello un'infrastruttura di produzione di Azure a sicurezza elevata.

> [!NOTE]
> Per altre informazioni, vedere le informazioni sul [progetto Cerberus](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) su GitHub.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulle operazioni da eseguire per garantire l'integrità e la sicurezza della piattaforma, vedere:

- [Sicurezza del firmware](firmware.md)
- [Avvio protetto](secure-boot.md)
- [Attestazione di avvio e host misurata](measured-boot-host-attestation.md)
- [Crittografia di dati inattivi](encryption-atrest.md)
- [Sicurezza hypervisor](hypervisor.md)