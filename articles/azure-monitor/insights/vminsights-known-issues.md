---
title: Problemi noti di Monitoraggio di Azure per le macchine virtuali (anteprima) | Microsoft Docs
description: Questo articolo descrive problemi noti relativi a Monitoraggio di Azure per le macchine virtuali, una soluzione di Azure che combina il monitoraggio dell'integrità e delle prestazioni del sistema operativo della VM di Azure. Monitoraggio di Azure per le macchine virtuali individua anche automaticamente i componenti dell'applicazione e le dipendenze con altre risorse e mappa la comunicazione tra essi.
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
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: b2a60ddee58a7a9c37582085132ee8a1df767cb4
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190357"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Problemi noti relativi a Monitoraggio di Azure per le macchine virtuali (anteprima)

Questo articolo descrive problemi noti relativi a Monitoraggio di Azure per le macchine virtuali, una soluzione di Azure che combina il monitoraggio dell'integrità e delle prestazioni del sistema operativo della VM di Azure. 

Di seguito sono descritti i problemi noti relativi alla funzione di integrità:

- La funzione di integrità è abilitata per tutte le macchine virtuali connesse all'area di lavoro di Log Analytics. È così anche quando l'azione inizia e termina in una singola macchina virtuale.
- Dopo aver disabilitato il monitoraggio per una macchina virtuale usando i metodi supportati, se si tenta di distribuirla di nuovo, è necessario distribuirla nella stessa area di lavoro. Se si usa una nuova area di lavoro e si tenta di visualizzare lo stato di integrità per quella macchina virtuale, potrebbe mostrare un comportamento anomalo.
- Se una macchina virtuale di Azure viene rimossa o eliminata, viene mostrata nella vista a elenco delle macchine virtuali per un periodo da tre a sette giorni. Facendo clic sullo stato di una macchina virtuale rimossa o eliminata, viene aperta la vista **Diagnostica integrità** e si avvia un ciclo di caricamento. Selezionando il nome di una macchina virtuale eliminata, viene aperto un pannello con un messaggio che dice che la macchina virtuale è stata eliminata.
- In questa versione non è possibile modificare il tempo e la frequenza dei criteri di integrità. 
- I criteri di integrità non possono essere disabilitati. 
- Dopo la distribuzione, può passare un po' di tempo prima che i dati siano visualizzati nel riquadro **Monitoraggio di Azure** > **Macchine virtuali** > **Integrità** o nel riquadro **VM resource** (Risorsa della macchina virtuale)  > **Insights** (Informazioni dettagliate).
- L'esperienza della diagnostica dell'integrità si aggiorna più velocemente rispetto a qualsiasi altra vista. Le informazioni potrebbero subire un ritardo quando si passa tra le viste. 
- Il riepilogo Avvisi che è incluso con Monitoraggio di Azure per le macchine virtuali mostra solo gli avvisi generati da problemi di integrità che vengono rilevati con le macchine virtuali di Azure.
- Il riquadro **Elenco di avvisi** nella singola macchina virtuale e in Monitoraggio di Azure mostra gli avvisi la cui condizione di monitoraggio è impostata su *attivata* negli ultimi 30 giorni. Gli avvisi non sono configurabili. Tuttavia, dopo l'apertura del riquadro **Elenco di avvisi**, è possibile fare clic sul riepilogo per modificare il valore del filtro sia della condizione di monitoraggio che dell'intervallo di tempo.
- Nel riquadro **Elenco di avvisi** si consiglia di non modificare i filtri **Tipo di risorsa**, **Risorsa** e **Servizio di monitoraggio**. Sono stati configurati in modo specifico per la soluzione. Questa vista a elenco mostra più campi rispetto all'elenco **Monitoraggio di Azure** > **Avvisi**.   
- Nelle macchine virtuali Linux la vista **Diagnostica integrità** mostra l'intero nome del dominio della macchina virtuale invece del nome della macchina virtuale definito dall'utente.
- Arrestando le macchine virtuali vengono aggiornati alcuni dei criteri di integrità a *critico* e altri a *integro*. Lo stato della macchina virtuale della rete è mostrato come *critico*.
- La gravità degli avvisi di integrità non può essere modificata. Può solo essere abilitata o disabilitata. Inoltre, alcuni livelli di gravità vengono aggiornati in base allo stato dei criteri di integrità.
- Se si modifica qualsiasi impostazione di un'istanza del criterio di integrità, vengono modificate tutte le istanze del criterio di integrità dello stesso tipo nella macchina virtuale. Se ad esempio viene modificata la soglia dell'istanza del criterio di integrità relativo allo spazio libero su disco che corrisponde al disco logico C:, questa soglia viene applicata a tutti gli altri dischi logici individuati e monitorati per la stessa macchina virtuale.  
- Le soglie per i criteri di integrità per una macchina virtuale Windows non sono modificabili perché i relativi stati di integrità sono impostati su *in esecuzione* o *disponibile*. Quando viene eseguita una query sullo stato di integrità dall'[API di monitoraggio del carico di lavoro](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager), per il valore *comparisonOperator* (operatore di confronto) viene visualizzato **LessThan** (minore di) o **GreaterThan** (maggiore di) con un valore *threshold* (soglia) pari a **4** per il servizio o l'entità se:
   - Integrità del servizio client DNS - Il servizio non è in esecuzione. 
   - Integrità del servizio client DHCP - Il servizio non in esecuzione. 
   - Integrità del servizio RPC - Il servizio non è in esecuzione. 
   - Integrità del servizio Windows Firewall - Il servizio non è in esecuzione.
   - Integrità del servizio log di eventi di Windows - Il servizio non è in esecuzione. 
   - Integrità del servizio server - Il servizio non è in esecuzione. 
   - Integrità del servizio Gestione remota Windows - Il servizio non è in esecuzione. 
   - Errore o danneggiamento del file system - Il disco logico non è disponibile.

- Le soglie per i criteri di integrità di Linux seguenti non possono essere modificate, perché il relativo stato di integrità è già impostato su *true*. Quando viene eseguita una query sullo stato di integrità dall'API di monitoraggio del carico di lavoro per l'entità in base al contesto, per lo stato di integrità viene visualizzato il valore di *comparisonOperator* (operatore di confronto) **LessThan** (minore di) con un valore *threshold* (soglia) pari a **1**:
   - Stato disco logico - Il disco logico non è online/disponibile
   - Stato disco - Il disco non è online/disponibile
   - Stato scheda di rete - Scheda di rete disabilitata  

- Il criterio di integrità *utilizzo CPU totale* in Windows mostra la soglia **diverso da 4** sia nel portale sia nell'API di monitoraggio del carico di lavoro. La soglia viene raggiunta quando l'*utilizzo CPU totale* è maggiore del 95% e la lunghezza della coda del sistema è maggiore di 15. Questo criterio di integrità non può essere modificato in questa versione. 
- L'applicazione delle modifiche di configurazione, ad esempio l'aggiornamento di una soglia, può richiedere fino a 30 minuti, anche se l'API di monitoraggio del carico di lavoro o il portale potrebbe aggiornarle immediatamente. 
- I criteri di integrità a livello di processore singolo e processore logico non sono disponibili in Windows. Per le macchine virtuali Windows è disponibile solo l'utilizzo CPU totale. 
- Le regole di avviso definite per ogni criterio di integrità non sono esposte nel portale di Azure. È possibile abilitare o disabilitare una regola di avviso per l'integrità solo nell'[API di monitoraggio del carico di lavoro](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager). 
- Non è possibile assegnare un [gruppo di azioni di Monitoraggio di Azure](../../azure-monitor/platform/action-groups.md) per gli avvisi di integrità nel portale di Azure. Si può usare solo l'API di impostazione delle notifiche per configurare un gruppo di azioni in modo che venga attivato ogni volta che viene generato un avviso di integrità. Attualmente è possibile assegnare gruppi di azioni a una macchina virtuale, in modo che tutti gli *avvisi di integrità* generati per la macchina virtuale attivino gli stessi gruppi di azioni. A differenza degli avvisi tradizionali di Azure, non è possibile usare un gruppo di azioni distinto per ogni regola di avviso di integrità. Inoltre, quando vengono generati avvisi di integrità, sono supportati solo i gruppi di azioni configurati per inviare notifiche tramite posta elettronica o SMS. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sui requisiti e i metodi per abilitare il monitoraggio delle macchine virtuali, vedere [Come eseguire l'onboarding di Monitoraggio di Azure per le macchine virtuali](vminsights-onboard.md).
