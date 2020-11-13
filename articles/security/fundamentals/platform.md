---
title: Integrità e sicurezza della piattaforma Azure-sicurezza di Azure
description: Panoramica tecnica sull'integrità e sulla sicurezza della piattaforma Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 4755bc19a645d196487f0b8e0f4d1ef2120723ca
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557981"
---
# <a name="platform-integrity-and-security-overview"></a>Panoramica dell'integrità e della sicurezza della piattaforma
La flotta di Azure è costituita da milioni di server (host) con migliaia più aggiunte su base giornaliera. Migliaia di host vengono inoltre sottoposti a manutenzione su base giornaliera tramite riavvii, aggiornamenti del sistema operativo o riparazioni. Prima che un host possa partecipare alla flotta e iniziare ad accettare i carichi di lavoro dei clienti, Microsoft verifica che l'host sia in uno stato sicuro e attendibile. Questa verifica garantisce che non si siano verificate modifiche dannose o involontarie nei componenti della sequenza di avvio durante i flussi di lavoro della supply chain o della manutenzione.

## <a name="securing-azure-hardware-and-firmware"></a>Protezione dell'hardware e del firmware di Azure
Questa serie di articoli descrive il modo in cui Microsoft garantisce l'integrità e la sicurezza degli host attraverso diverse fasi del ciclo di vita, dalla produzione al tramonto. L'articolo descrive gli aspetti seguenti:
 
- [Sicurezza del firmware](firmware.md)
- [Avvio protetto UEFI](secure-boot.md)
- [Attestazione di avvio e host misurata](measured-boot-host-attestation.md)
- [Progetto Cerberus](project-cerberus.md)
- [Crittografia di dati inattivi](encryption-atrest.md)
- [Sicurezza hypervisor](hypervisor.md)
 
## <a name="next-steps"></a>Passaggi successivi

- Scopri in che modo Microsoft collabora attivamente all'interno dell'ecosistema hardware cloud per [migliorare i miglioramenti della sicurezza del firmware](firmware.md)continuo.

- Comprendere la [responsabilità condivisa nel cloud](shared-responsibility.md).