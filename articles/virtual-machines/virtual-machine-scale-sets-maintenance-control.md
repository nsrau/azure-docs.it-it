---
title: Panoramica del controllo manutenzione per gli aggiornamenti delle immagini del sistema operativo nei set di scalabilità di macchine virtuali di Azure
description: Informazioni su come controllare quando vengono implementati gli aggiornamenti automatici delle immagini del sistema operativo nei set di scalabilità di macchine virtuali di Azure usando il controllo di manutenzione.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 55cbc5db534dd1b05f91a24e0104b1f2dc110547
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532637"
---
# <a name="preview-maintenance-control-for-azure-virtual-machine-scale-sets"></a>Anteprima: controllo di manutenzione per i set di scalabilità di macchine virtuali di Azure 

Gestire gli [aggiornamenti automatici delle immagini del sistema operativo](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) per i set di scalabilità di macchine virtuali usando il controllo di manutenzione

Il controllo della manutenzione consente di decidere quando applicare gli aggiornamenti ai dischi del sistema operativo nei set di scalabilità di macchine virtuali tramite un'esperienza più semplice e prevedibile. 

Le configurazioni di manutenzione funzionano tra le sottoscrizioni e i gruppi di risorse.

L'intero flusso di lavoro viene visualizzato in questa procedura: 
- Creare una configurazione di manutenzione.
- Associare un set di scalabilità di macchine virtuali a una configurazione di manutenzione.
- Abilitare gli aggiornamenti automatici del sistema operativo.

> [!IMPORTANT]
> Il controllo di manutenzione per gli aggiornamenti delle immagini del sistema operativo nei set di scalabilità di macchine virtuali di Azure è attualmente in anteprima pubblica
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="limitations"></a>Limitazioni

- Le macchine virtuali devono trovarsi in un set di scalabilità.
- L'utente deve disporre dell'accesso **collaboratore risorse** .
- La durata della manutenzione deve essere di 5 ore o più nella configurazione di manutenzione.
- La ricorrenza della manutenzione deve essere impostata su' Day ' nella configurazione di manutenzione


## <a name="management-options"></a>Opzioni di gestione

È possibile creare e gestire le configurazioni di manutenzione usando una delle opzioni seguenti:

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come controllare quando viene applicata la manutenzione ai set di scalabilità di macchine virtuali di Azure usando il controllo di manutenzione e PowerShell.

> [!div class="nextstepaction"]
> [Controllo di manutenzione del set di scalabilità di macchine virtuali tramite PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)
