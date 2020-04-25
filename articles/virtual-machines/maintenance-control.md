---
title: Panoramica del controllo di manutenzione per le macchine virtuali di Azure con il portale di Azure
description: Informazioni su come controllare quando viene applicata la manutenzione alle VM di Azure usando il controllo di manutenzione.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 4c5e30d0607db2d529ae41ebab6dc82e925ff2a8
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82139199"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Gestione degli aggiornamenti della piattaforma con il controllo di manutenzione 

Gestire gli aggiornamenti della piattaforma, che non richiedono un riavvio, usando il controllo di manutenzione. Azure aggiorna spesso l'infrastruttura per migliorare l'affidabilità, le prestazioni, la sicurezza o avviare nuove funzionalità. La maggior parte degli aggiornamenti è trasparente per gli utenti. Alcuni carichi di lavoro sensibili, ad esempio giochi, flussi multimediali e transazioni finanziarie, non possono tollerare persino pochi secondi di blocco o disconnessione di una macchina virtuale per la manutenzione. Il controllo di manutenzione offre la possibilità di attendere gli aggiornamenti della piattaforma e di applicarli in una finestra in sequenza di 35 giorni. 

Il controllo della manutenzione consente di decidere quando applicare gli aggiornamenti alle VM isolate e agli host dedicati di Azure.

Con il controllo di manutenzione, è possibile:
- Aggiornamenti batch in un unico pacchetto di aggiornamento.
- Attendere fino a 35 giorni per l'applicazione degli aggiornamenti. 
- Automatizzare gli aggiornamenti della piattaforma per la finestra di manutenzione con funzioni di Azure.
- Le configurazioni di manutenzione funzionano tra le sottoscrizioni e i gruppi di risorse. 

## <a name="limitations"></a>Limitazioni

- Le macchine virtuali devono trovarsi in un [host dedicato](./linux/dedicated-hosts.md)o essere create con una [dimensione di macchina virtuale isolata](./linux/isolation.md).
- Dopo 35 giorni, verrà applicato automaticamente un aggiornamento.
- L'utente deve disporre dell'accesso **collaboratore risorse** .

## <a name="management-options"></a>Opzioni di gestione

È possibile creare e gestire le configurazioni di manutenzione usando una delle opzioni seguenti:

- [Interfaccia della riga di comando di Azure](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure portal](maintenance-control-portal.md)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [manutenzione e aggiornamenti](maintenance-and-updates.md).
