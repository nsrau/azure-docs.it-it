---
title: Sicurezza del firmware-sicurezza di Azure
description: Informazioni sul modo in cui Microsoft protegge hardware e firmware di Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 8233cc714d977083f4d55716d35c0b7094a069ea
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557957"
---
# <a name="firmware-security"></a>Sicurezza del firmware
Questo articolo descrive il modo in cui Microsoft protegge l'ecosistema hardware cloud e le catene di approvvigionamento.

## <a name="securing-the-cloud-hardware-ecosystem"></a>Protezione dell'ecosistema hardware cloud
Microsoft collabora attivamente all'interno dell'ecosistema hardware cloud per migliorare i miglioramenti della sicurezza:

- Collaborazione con i partner hardware e firmware di Azure (ad esempio produttori di componenti e integratori di sistemi) per soddisfare i requisiti di sicurezza hardware e firmware di Azure.

- Consentire ai partner di eseguire valutazioni e miglioramento continui del comportamento di sicurezza dei propri prodotti usando i requisiti definiti da Microsoft in aree quali:

  - Avvio protetto del firmware
  - Ripristino sicuro del firmware
  - Aggiornamento sicuro del firmware
  - Crittografia del firmware
  - Hardware bloccato
  - Telemetria di debug granulari
  - Supporto del sistema per l'hardware TPM 2,0 per abilitare l'avvio con misurazione

- Coinvolgere e contribuire al progetto di sicurezza [Open Compute Project (OCP)](https://www.opencompute.org/wiki/Security) attraverso lo sviluppo di specifiche. Le specifiche promuovono la coerenza e la chiarezza per la progettazione e l'architettura sicure nell'ecosistema.

   > [!NOTE]
   > Un esempio di contributo al progetto di sicurezza OCP è la specifica dell' [avvio protetto da hardware](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0) .

## <a name="securing-hardware-and-firmware-supply-chains"></a>Protezione delle catene di approvvigionamento di hardware e firmware
I fornitori e i fornitori di hardware cloud per Azure devono inoltre rispettare i requisiti e i processi di sicurezza della supply chain sviluppati da Microsoft. I processi di sviluppo e distribuzione di hardware e firmware sono necessari per seguire i processi di Microsoft [Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl) (SDL), ad esempio:

- Modellazione delle minacce
- Revisioni della progettazione protette
- Revisioni del firmware e test di penetrazione
- Ambienti di compilazione e test sicuri
- Gestione delle vulnerabilità di sicurezza e risposta agli eventi imprevisti

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulle operazioni da eseguire per garantire l'integrità e la sicurezza della piattaforma, vedere:

- [Avvio protetto](secure-boot.md)
- [Attestazione di avvio e host misurata](measured-boot-host-attestation.md)
- [Progetto Cerberus](project-cerberus.md)
- [Crittografia di dati inattivi](encryption-atrest.md)
- [Sicurezza hypervisor](hypervisor.md)