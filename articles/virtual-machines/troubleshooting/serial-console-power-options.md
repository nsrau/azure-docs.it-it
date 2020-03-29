---
title: Opzioni di risparmio energia della console seriale di Azure Documenti Microsoft
description: Opzioni di alimentazione della macchina virtuale disponibili nella console seriale di AzureVM power options available within the Azure Serial Console
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451192"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Opzioni di risparmio energia disponibili nella console seriale di Azure

La console seriale di Azure offre diversi potenti strumenti per la gestione dell'alimentazione nella macchina virtuale o nel set di scalabilità di macchine virtuali. Queste opzioni di risparmio energia possono essere fonte di confusione per alcuni, quindi questa è una panoramica di ogni strumento e il suo caso d'uso previsto.

Funzionalità console seriale | Descrizione | Caso d'uso
:----------------------|:------------|:---------
Riavvio di una macchina virtuale | Riavvio normale dell'istanza del set di scalabilità della macchina virtuale o della macchina virtuale. Questa operazione equivale a chiamare la funzionalità di riavvio disponibile nella pagina Panoramica.This operation is the same as calling the restart feature available in the Overview page. | Nella maggior parte dei casi, questa opzione dovrebbe essere il primo strumento nel tentativo di riavviare la macchina virtuale. La connessione alla console seriale subirà una breve interruzione e riprenderà automaticamente non appena la macchina virtuale viene riavviata.
Reimpostare una VM | Un ciclo di alimentazione forzato della macchina virtuale o della scalabilità della macchina virtuale impostata dalla piattaforma Azure.A forceful power cycle of your VM or virtual machine scale set by the Azure platform. | Questa opzione viene utilizzata per riavviare immediatamente il sistema operativo indipendentemente dallo stato corrente. Poiché questa operazione non è graziosa, esiste un rischio di perdita o danneggiamento dei dati. La connessione alla console seriale non si verifica alcuna interruzione, che può essere utile per l'invio di comandi all'inizio dell'avvio (ad esempio, per accedere a GRUB in una macchina virtuale Linux o in modalità provvisoria in una macchina virtuale Windows).
SysRq - Riavvio (b) | Una richiesta di sistema per forzare il riavvio di un guest. | Questa funzionalità è applicabile solo ai sistemi operativi Linux e richiede [l'abilitazione di SysRq](./serial-console-nmi-sysrq.md#system-request-sysrq) nel sistema operativo. Se il sistema operativo è configurato correttamente per SysRq, questo comando causerà il riavvio del sistema operativo.
NMI (Interrupt non mascherabile) | Un comando di interrupt, che verrà recapitato al sistema operativo | Questa operazione è disponibile per macchine virtuali [Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) e [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) e richiede l'abilitazione di NMI. L'invio di un NMI in genere causa l'arresto anomalo del sistema operativo. È possibile configurare il sistema operativo per creare un file di dump e quindi riavviare dopo aver ricevuto l'nMI, che può essere utile nel debug di basso livello.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla console seriale di Azure per le macchine virtuali LinuxLearn more about the [Azure Serial Console for Linux VMs](./serial-console-linux.md)
* Altre informazioni sulla console seriale di Azure per le macchine virtuali WindowsLearn more about [the Azure Serial Console for Windows VMs](./serial-console-windows.md)