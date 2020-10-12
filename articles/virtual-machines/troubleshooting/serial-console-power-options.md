---
title: Opzioni di risparmio energia per la console seriale di Azure | Microsoft Docs
description: Opzioni di risparmio energia della macchina virtuale disponibili nella console seriale di Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: 11c2549d7282bae5654ede1ac34d63a43cd0f059
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75451192"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Opzioni di risparmio energia disponibili nella console seriale di Azure

La console seriale di Azure offre diversi strumenti avanzati per il risparmio di energia nella VM o nel set di scalabilità di macchine virtuali. Queste opzioni di risparmio energia potrebbero causare confusione ad alcuni, pertanto si tratta di una panoramica di ogni strumento e del caso d'uso previsto.

Funzionalità console seriale | Descrizione | Caso d'uso
:----------------------|:------------|:---------
Riavvio di una macchina virtuale | Riavvio normale della VM o dell'istanza del set di scalabilità di macchine virtuali. Questa operazione equivale alla chiamata della funzionalità di riavvio disponibile nella pagina panoramica. | Nella maggior parte dei casi, questa opzione dovrebbe essere il primo strumento nel tentativo di riavviare la macchina virtuale. La connessione alla console seriale rileverà una breve interruzione e riprenderà automaticamente non appena la VM verrà riavviata.
Reimpostare una VM | Un ciclo di alimentazione forzato della VM o del set di scalabilità di macchine virtuali dalla piattaforma Azure. | Questa opzione consente di riavviare immediatamente il sistema operativo indipendentemente dallo stato corrente. Poiché questa operazione non è corretta, sussiste il rischio di perdita o danneggiamento dei dati. Non si verificano interruzioni nella connessione alla console seriale, che può essere utile per l'invio di comandi all'inizio del tempo di avvio (ad esempio, per ottenere GRUB in una VM Linux o in modalità sicura in una macchina virtuale Windows).
SysRq-riavvia (b) | Richiesta di sistema per forzare un riavvio guest. | Questa funzionalità è applicabile solo ai sistemi operativi Linux e richiede l' [Abilitazione di sysrq](./serial-console-nmi-sysrq.md#system-request-sysrq) nel sistema operativo. Se il sistema operativo è configurato correttamente per SysRq, questo comando provocherà il riavvio del sistema operativo.
NMI (interrupt non mascherabile) | Un comando interrupt, che verrà recapitato al sistema operativo | Questa operazione è disponibile per le macchine virtuali [Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) e [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) e richiede l'abilitazione di NMI. Se si invia un NMI, il sistema operativo verrà arrestato in modo anomalo. È possibile configurare il sistema operativo per creare un file di dump e quindi riavviare la ricezione di NMI, che può essere utile per il debug di basso livello.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [console seriale di Azure per macchine virtuali Linux](./serial-console-linux.md)
* Altre informazioni sulla [console seriale di Azure per le macchine virtuali Windows](./serial-console-windows.md)