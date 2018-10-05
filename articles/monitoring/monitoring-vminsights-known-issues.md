---
title: Problemi noti di Monitoraggio di Azure per le macchine virtuali | Microsoft Docs
description: Monitoraggio di Azure per le macchine virtuali è una soluzione di Azure che combina il monitoraggio dell'integrità e delle prestazioni del sistema operativo delle macchine virtuali di Azure, nonché l'individuazione automatica dei componenti e delle dipendenze delle applicazioni con altre risorse e mappa la comunicazione tra questi elementi. Questo articolo illustra i problemi noti.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: 819c3e74355cf80c7a998abb8b02b10c9e077059
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47062769"
---
# <a name="known-issues-with-azure-monitor-for-vms"></a>Problemi noti relativi a Monitoraggio di Azure per le macchine virtuali

Di seguito vengono descritti i problemi noti relativi alla funzionalità di integrità di Monitoraggio di Azure per le macchine virtuali:

- In questa versione non è possibile modificare il tempo e la frequenza dei criteri di integrità. 
- I criteri di integrità non possono essere disabilitati. 
- Dopo l'onboarding, la visualizzazione dei dati in Monitoraggio di Azure -> Macchine virtuali -> Integrità o dal pannello della risorsa della VM -> Insights può richiedere tempo.
- La vista Diagnostica integrità viene aggiornata più velocemente rispetto a qualsiasi altra vista, pertanto è possibile che si verifichi un ritardo nella visualizzazione delle informazioni quando si passa tra le viste.  
- I riepiloghi degli avvisi in Monitoraggio di Azure per l'integrità della macchina virtuale riguardano solo gli avvisi generati per problemi di integrità rilevati con macchine virtuali di Azure monitorate.
- La pagina della vista **Elenco di avvisi** nella singola macchina virtuale e in Monitoraggio di Azure mostra gli avvisi la cui condizione di monitoraggio è impostata su "attivata" negli ultimi 30 giorni.  Gli avvisi non sono configurabili. Dopo avere fatto clic sul riepilogo, tuttavia, dopo che è stata avviata la pagina della vista **Elenco di avvisi**, è possibile modificare il valore del filtro sia della condizione di monitoraggio che dell'intervallo di tempo.
- Nella pagina della vista **Elenco di avvisi** è consigliabile non modificare i filtri **Tipo di risorsa**, **Risorsa** e **Servizio di monitoraggio** in quanto sono stati configurati specificatamente per la soluzione. Questo elenco di avvisi mostra alcuni campi aggiuntivi rispetto alla vista di Monitoraggio di Azure -> Elenco di avvisi.    
- Nelle macchine virtuali Linux la vista **Diagnostica integrità** include l'intero nome del dominio della macchina virtuale invece del nome della macchina virtuale definito dall'utente.
- L'arresto delle macchine virtuali aggiornerà alcuni dei criteri di integrità allo stato critico e altri allo stato di integrità con uno stato netto della macchina virtuale in uno stato critico.
- La gravità degli avvisi di integrità non può essere modificata. Gli avvisi possono solo essere abilitati o disabilitati.  Alcuni livelli di gravità inoltre vengono aggiornati in base allo stato dei criteri di integrità.
- Se si modifica un'impostazione di un'istanza di criteri di integrità, la stessa impostazione verrà modificata in tutte le istanze di criteri di integrità dello stesso tipo nella macchina virtuale. Se ad esempio viene modificata la soglia dell'istanza dei criteri di integrità dello spazio libero su disco corrispondente al disco logico C:, questa soglia verrà applicata a tutti gli altri dischi logici che vengono individuati e monitorati per la stessa macchina virtuale.   
- Le soglie per alcuni criteri di integrità di Windows come l'integrità del servizio client DNS non sono modificabili, perché il relativo stato di integrità è già bloccato sullo stato **in esecuzione**, **disponibile** del servizio o dell'entità a seconda del contesto.  Il valore è invece rappresentato dal numero 4. Verrà convertito nella stringa di visualizzazione effettiva in una versione futura.  
- Le soglie per alcuni criteri di integrità di Linux, ad esempio l'integrità del disco logico, non sono modificabili in quanto sono già impostate sull'attivazione in caso di stato di non integrità.  Queste soglie indicano se qualche componente è online/offline, acceso o spento e vengono rappresentate e indicano la stessa informazione mostrando il valore 1 o 0.
- Se si aggiorna il filtro Gruppo di risorse in un qualsiasi gruppo di risorse mentre si usa la vista di qualsiasi elenco in scala in Monitoraggio di Azure -> Macchine virtuali -> Integrità con la sottoscrizione e il gruppo di risorse preselezionati, la vista elenco mostrerà **nessun risultato**.  Tornare alla scheda Monitoraggio di Azure -> Macchine virtuali -> Integrità e selezionare la sottoscrizione e il gruppo di risorse desiderati, quindi passare alla vista elenco.

## <a name="next-steps"></a>Passaggi successivi
Vedere [Eseguire l'onboarding di Monitoraggio di Azure per le macchine virtuali](monitoring-vminsights-onboard.md) per informazioni sui requisiti e i metodi per abilitare il monitoraggio delle macchine virtuali.