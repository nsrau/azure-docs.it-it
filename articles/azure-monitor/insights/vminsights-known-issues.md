---
title: Problemi noti di Monitoraggio di Azure per le macchine virtuali (anteprima) | Microsoft Docs
description: Questo articolo descrive problemi noti relativi a Monitoraggio di Azure per le macchine virtuali, una soluzione di Azure che combina l'integrità, l'individuazione delle dipendenze dell'applicazione e il monitoraggio delle prestazioni del sistema operativo della VM di Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: magoedte
ms.openlocfilehash: 5fdee4edce0301a5a4524725879f599b0a2e3532
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795067"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Problemi noti relativi a Monitoraggio di Azure per le macchine virtuali (anteprima)

Questo articolo descrive problemi noti relativi a Monitoraggio di Azure per le macchine virtuali, una soluzione di Azure che combina l'integrità, l'individuazione dei componenti dell'applicazione e il monitoraggio delle prestazioni del sistema operativo della VM di Azure. 

## <a name="health"></a>Integrità 
Di seguito sono descritti i problemi noti relativi alla versione corrente della funzione di integrità:

- Il pannello delle proprietà della macchina virtuale visualizza il sistema operativo Windows Server 2019 come Windows Server 2016. Questo problema verrà risolto in una versione futura.
- Se una macchina virtuale di Azure viene rimossa o eliminata, viene visualizzata nella visualizzazione elenco delle macchine virtuali per un periodo. Facendo clic sullo stato di una macchina virtuale rimossa o eliminata, viene aperta la vista **Diagnostica integrità** e si avvia un ciclo di caricamento. Selezionando il nome di una macchina virtuale eliminata, viene aperto un pannello con un messaggio che dice che la macchina virtuale è stata eliminata.
- L'applicazione delle modifiche di configurazione, ad esempio l'aggiornamento di una soglia, può richiedere fino a 30 minuti, anche se l'API di monitoraggio del carico di lavoro o il portale potrebbe aggiornarle immediatamente. 
- L'esperienza della diagnostica di integrità si aggiorna più velocemente rispetto alle altre viste. Le informazioni potrebbero subire un ritardo quando si passa da una vista all'altra. 
- Per le macchine virtuali Linux, il titolo della pagina che elenca i criteri di integrità per la visualizzazione di una singola macchina virtuale ha il nome di dominio completo della macchina virtuale invece del nome della macchina virtuale definito dall'utente. 
- Dopo aver disabilitato il monitoraggio per una macchina virtuale usando uno dei metodi supportati, se si tenta di distribuirla di nuovo, è necessario distribuirla nella stessa area di lavoro. Se si usa un'area di lavoro diversa e si prova a visualizzare lo stato di integrità per tale macchina virtuale, potrebbe verificarsi un comportamento incoerente.
- Il criterio di integrità di utilizzo CPU totale per Windows presenta una soglia *non uguale a* **4**: ciò significa che l'utilizzo della CPU è maggiore del 95% e la lunghezza della coda di sistema è maggiore di 15. Questo criterio di integrità non è configurabile in questa versione di anteprima.  
- Dopo aver rimosso i componenti della soluzione dall'area di lavoro, è possibile continuare a visualizzare lo stato di integrità dalle macchine virtuali di Azure, in particolare dati sulle prestazioni e mappa, quando si passa a qualsiasi altra visualizzazione nel portale. Dopo qualche istante, i dati non verranno più visualizzati nella visualizzazione delle prestazioni e della mappa; la visualizzazione dell'integrità continua a mostrare lo stato di integrità delle macchine virtuali. L'opzione**Prova adesso** sarà disponibile per eseguire nuovamente l'onboarding solo dalle viste Prestazioni e Mappa.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sui requisiti e i metodi per abilitare il monitoraggio delle macchine virtuali, vedere [Come eseguire l'onboarding di Monitoraggio di Azure per le macchine virtuali](vminsights-onboard.md).
