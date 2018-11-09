---
title: Problemi noti di Monitoraggio di Azure per le macchine virtuali (Anteprima) | Microsoft Docs
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
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: eba9e26f12fd9e1862727adec4f8c8f594e8f659
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091675"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Problemi noti relativi a Monitoraggio di Azure per le macchine virtuali (Anteprima)

Di seguito vengono descritti i problemi noti relativi alla funzionalità di integrità di Monitoraggio di Azure per le macchine virtuali:

- L'onboarding della funzionalità di integrità viene eseguito in tutte le macchine virtuali connesse all'area di lavoro di Log Analytics, anche quando l'operazione viene avviata e completata da una singola macchina virtuale.
- Se una macchina virtuale di Azure non è più presente perché è stata rimossa o eliminata, rimane visualizzata nella visualizzazione elenco delle macchine virtuali per un periodo da tre a sette giorni. Facendo clic sullo stato di una macchina virtuale rimossa o eliminata, inoltre, viene aperta la visualizzazione **Diagnostica integrità** per tale macchina, che entra quindi in un ciclo di caricamento. Selezionando il nome di una macchina virtuale eliminata, viene aperto un pannello con un messaggio che indica che la macchina virtuale è stata eliminata.
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
- Le soglie per i criteri di integrità seguenti per una macchina virtuale Windows non possono essere modificate, perché il relativo stato di integrità è già impostato su **in esecuzione** o **disponibile**. Quando viene eseguita una query sullo stato di integrità dall'[API di monitoraggio del carico di lavoro](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager), per lo stato di integrità viene visualizzato il valore di *OperatoreDiConfronto* **MinoreDi** o **MaggioreDi** con un valore di *soglia* pari a **4** per il servizio o l'entità se:
   - Integrità servizio client DNS - Servizio non in esecuzione 
   - Integrità servizio client DHCP - Servizio non in esecuzione 
   - Integrità servizio RPC - Servizio non in esecuzione 
   - Integrità servizio Windows Firewall - Servizio non in esecuzione
   - Integrità servizio Registro eventi di Windows - Servizio non in esecuzione 
   - Integrità servizio server - Servizio non in esecuzione 
   - Integrità servizio Gestione remota Windows - Servizio non in esecuzione 
   - Errore o danneggiamento file system - Disco logico non disponibile

- Le soglie per i criteri di integrità Linux seguenti non possono essere modificate, perché il relativo stato di integrità è già impostato su **true**.  Quando viene eseguita una query sullo stato di integrità dall'API di monitoraggio del carico di lavoro per l'entità in base al contesto, per lo stato di integrità viene visualizzato il valore di *OperatoreDiConfronto* **MinoreDi** con un valore di *soglia* pari a **1**:
   - Stato disco logico - Disco logico non online/disponibile
   - Stato disco - Disco non online/disponibile
   - Stato scheda di rete - Scheda di rete disabilitata  

- Il criterio di integrità **Utilizzo CPU totale** in Windows mostra una soglia **diverso da 4** nel portale e quando sottoposto a query dall'API di monitoraggio del carico di lavoro quando l'utilizzo della CPU è superiore al 95% e la lunghezza della coda di sistema è superiore a 15. Questo criterio di integrità non può essere modificato in questa versione.  
- Le modifiche alla configurazione, ad esempio l'aggiornamento di una soglia, richiedono fino a 30 minuti per l'applicazione, anche se l'API di monitoraggio del carico di lavoro o il portale potrebbe venire aggiornato immediatamente.  
- I criteri di integrità per il livello di singoli processori e processori logici non sono disponibili in Windows e per le macchine virtuali Windows è disponibile solo il criterio **Utilizzo CPU totale**.  
- Le regole di avviso definite per ogni criterio di integrità non vengono esposte nel portale di Azure. Possono essere configurate solo dall'[API di monitoraggio del carico di lavoro](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) per abilitare o disabilitare una regola di avviso per l'integrità.  
- Non è possibile assegnare un [gruppo di azioni di Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-action-groups.md) per gli avvisi di integrità dal portale di Azure. È necessario usare l'API di impostazione delle notifiche per configurare un gruppo di azioni in modo che venga attivato ogni volta che viene generato un avviso di integrità. Attualmente, i gruppi di azioni possono essere assegnati a una macchina virtuale, in modo che tutti gli *avvisi di integrità* generati per la macchina virtuale attivino gli stessi gruppi di azione. Non è possibile usare un gruppo di azione distinto per ogni regola di avviso di integrità, come con gli avvisi di Azure tradizionali. Inoltre, quando vengono generati avvisi di integrità, sono supportati solo i gruppi di azioni configurati per inviare una notifica tramite posta elettronica o SMS. 

## <a name="next-steps"></a>Passaggi successivi
Vedere [Eseguire l'onboarding di Monitoraggio di Azure per le macchine virtuali](monitoring-vminsights-onboard.md) per informazioni sui requisiti e i metodi per abilitare il monitoraggio delle macchine virtuali.