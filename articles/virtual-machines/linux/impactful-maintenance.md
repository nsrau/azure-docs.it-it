---
title: Riavvio per manutenzione delle macchine virtuali Linux in Azure | Microsoft Docs
description: Riavvio per manutenzione delle macchine virtuali Linux.
services: virtual-machines-linux
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: c02b2878462afb107c81317c9ea0a317cb9ce838
ms.contentlocale: it-it
ms.lasthandoff: 04/18/2017


---

# <a name="vm-restarting-maintenance"></a>Riavvio per manutenzione delle macchine virtuali

In pochi casi può verificarsi che le macchine virtuali vengano riavviate per interventi di manutenzione pianificati che interessano l'infrastruttura sottostante. Poiché queste operazioni producono un impatto sulla disponibilità delle macchine virtuali ospitate in Azure, sono ora disponibili le funzionalità seguenti:

-   Notifica inviata almeno 30 giorni prima dell'impatto.

-   Visibilità delle finestre di manutenzione per ogni macchina virtuale.

-   Flessibilità e controllo sull'impostazione dell'ora esatta in cui gli interventi di manutenzione produrranno un impatto sulle macchine virtuali.

Le operazioni di manutenzione in Microsoft Azure sono pianificate secondo iterazioni. Le iterazioni iniziali hanno un ambito limitato in modo da ridurre i rischi derivanti dall'implementazione di nuove correzioni e funzionalità. Le iterazioni successive possono estendersi a più aree (mai della stessa coppia di aree). Una macchina virtuale è inclusa in una singola iterazione di manutenzione. Se un'iterazione viene interrotta, le macchine virtuali rimanenti verranno incluse in un'altra interazione successiva.

L'iterazione di manutenzione pianificata prevede due fasi: finestra di manutenzione preventiva e finestra di manutenzione pianificata.

La **finestra di manutenzione preventiva** offre la flessibilità necessaria per avviare la manutenzione sulle macchine virtuali. In questo modo, è possibile determinare quando produrre l'impatto sulle macchine virtuali, la sequenza di aggiornamento e l'intervallo di tempo tra le operazioni di manutenzione di ogni macchina virtuale. È possibile eseguire una query su ogni macchina virtuale per verificare se è previsto un intervento di manutenzione e per controllare il risultato dell'ultima richiesta di manutenzione avviata.

La **finestra di manutenzione pianificata** viene visualizzata quando Azure ha pianificato la manutenzione delle macchine virtuali. Durante questa finestra temporale, che segue la finestra di manutenzione preventiva, è ancora possibile eseguire una query sulla finestra di manutenzione, ma non orchestrare la manutenzione.

## <a name="availability-considerations-during-planned-maintenance"></a>Considerazioni sulla disponibilità durante la manutenzione pianificata 

### <a name="paired-regions"></a>Aree abbinate

Ogni area di Azure è associata a un'altra area con la stessa ubicazione geografica, che insieme formano una coppia di aree. Quando si esegue la manutenzione, Azure aggiorna solo le istanze della macchina virtuale in una singola area della relativa coppia. Ad esempio, quando si aggiornano le macchine virtuali negli Stati Uniti centro-settentrionali, Azure non aggiorna contemporaneamente le macchine virtuali negli Stati Uniti centro-meridionali. Questa operazione viene pianificata in un secondo momento, consentendo il failover o il bilanciamento del carico tra le aree. Tuttavia, altre aree, ad esempio Europa settentrionale, possono essere sottoposte a manutenzione contemporaneamente a Stati Uniti orientali.
Leggere altre informazioni sulle [coppie di aree di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

### <a name="single-instance-vms-vs-availability-set-or-vm-scale-set"></a>Macchine virtuali a istanza singola e set di disponibilità o set di scalabilità di una macchina virtuale

Quando si distribuisce un carico di lavoro usando le macchine virtuali in Azure, è possibile creare le VM all'interno di un set di disponibilità per garantire un'elevata disponibilità per l'applicazione. Questa configurazione assicura che in caso di interruzione o di eventi di manutenzione sia disponibile almeno una macchina virtuale.

All'interno di un set di disponibilità, le singole macchine virtuali vengono distribuite su un massimo di 20 domini di aggiornamento. Durante la manutenzione pianificata, l'impatto prodotto interessa un solo dominio di aggiornamento in un dato momento e inoltre l'ordine dei domini di aggiornamento interessati dall'impatto potrebbe non essere sequenziale. Per le macchine virtuali a singola istanza (che non fanno parte del set di disponibilità) non è possibile prevedere o determinare quali e quante macchine virtuali saranno contemporaneamente coinvolte.

I set di scalabilità delle macchine virtuali sono una risorsa di calcolo di Azure che consente di distribuire e gestire un set di macchine virtuali identiche come una singola risorsa.
Il set di scalabilità offre garanzie simili a quelle del set di disponibilità sotto forma di domini di aggiornamento. 

Per ulteriori informazioni sulla configurazione delle macchine virtuali per la disponibilità elevata, vedere [*Gestire la disponibilità delle macchine virtuali Windows*](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="scheduled-events"></a>Eventi pianificati

Il Servizio metadati di Azure consente di scoprire informazioni sulla macchina virtuale dell'utente ospitata in Azure. La funzione degli eventi pianificati è una delle categorie esposte e presenta informazioni sugli eventi imminenti, ad esempio i riavvii, in modo che l'applicazione possa prepararsi di conseguenza e limitare le interruzioni.

Per ulteriori informazioni sugli eventi pianificati, fare riferimento a [Servizio metadati di Azure - Eventi pianificati](../virtual-machines-scheduled-events.md).

## <a name="maintenance-discovery-and-notifications"></a>Individuazione degli interventi di manutenzione e notifiche

La pianificazione degli interventi di manutenzione è visibile ai clienti al livello delle singole macchine virtuali. È possibile usare il portale, l'API, il modulo PowerShell o l'interfaccia della riga di comando di Azure per eseguire query sulle finestre di manutenzione preventiva e pianificata. L'utente dovrebbe inoltre ricevere una notifica (tramite posta elettronica) nel caso in cui una o più macchine virtuali siano interessate dal processo.

Entrambe le fasi di manutenzione, preventiva e pianificata, iniziano con una notifica. L'utente dovrebbe ricevere una singola notifica per ogni sottoscrizione di Azure. Per impostazione predefinita, la notifica viene inviata all'amministratore e al coamministratore della sottoscrizione. È anche possibile configurare il gruppo di destinatari della notifica di manutenzione.

### <a name="view-the-maintenance-window-in-the-portal"></a>Visualizzare la finestra di manutenzione nel portale 

È possibile usare il portale di Azure per cercare le macchine virtuali pianificate per la manutenzione.

1.  Accedere al portale di Azure.

2.  Fare clic e aprire il pannello **Macchine virtuali**.

3.  Fare clic sul pulsante **Colonne** per aprire l'elenco delle colonne disponibili da cui selezionare

4.  Selezionare e aggiungere le colonne **Finestra di manutenzione**. Le macchine virtuali pianificate per la manutenzione espongono le finestre di manutenzione. Quando la manutenzione viene completata o interrotta, la finestra di manutenzione non è più visibile.

### <a name="query-maintenance-details-using-the-azure-api"></a>Eseguire una query sui dettagli di manutenzione tramite l'API di Azure

Utilizzare l'[API per ottenere informazioni su una macchina virtuale](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) e cercare la visualizzazione dell'istanza per individuare i dettagli di manutenzione su una singola macchina virtuale. La risposta include gli elementi seguenti:

  - isCustomerInitiatedMaintenanceAllowed: indica se ora è possibile avviare la ridistribuzione preventiva sulla macchina virtuale.

  - preMaintenanceWindowStartTime: l'ora di inizio della finestra di manutenzione preventiva.

  - preMaintenanceWindowEndTime: l'ora di fine della finestra di manutenzione preventiva. Dopo tale periodo, non sarà più possibile avviare interventi di manutenzione sulla macchina virtuale.
    
  - maintenanceWindowStartTime: l'ora di inizio della finestra di manutenzione pianificata in cui verrà prodotto un impatto sulla macchina virtuale.

  - maintenanceWindowEndTime: l'ora di fine della finestra di manutenzione pianificata.
  
  - lastOperationResultCode: il risultato dell'ultima operazione di manutenzione-ridistribuzione.
 
  - lastOperationMessage: messaggio che descrive il risultato dell'ultima operazione di manutenzione-ridistribuzione.


## <a name="pre-emptive-redeploy"></a>Ridistribuzione preventiva

L'azione di ridistribuzione preventiva offre la flessibilità necessaria per controllare l'intervallo di tempo in cui verrà applicata la manutenzione alle macchine virtuali in Azure. La manutenzione pianificata inizia con una finestra di manutenzione preventiva in cui è possibile stabilire l'ora esatta per il riavvio di ciascuna macchina virtuale. Di seguito sono indicati i casi di uso in cui tale funzionalità è utile:

-   È necessario coordinare la manutenzione con il cliente finale.

-   La finestra di manutenzione pianificata mostrata da Azure non è sufficiente.
    Può verificarsi che la finestra venga visualizzata nel momento di maggiore traffico della settimana o sia troppo lunga.

-   Per applicazioni a istanze multiple o a più livelli, è necessario un tempo sufficiente tra due macchine virtuali o seguire una determinata sequenza.

Quando si richiede la ridistribuzione preventiva su una macchina virtuale, quest'ultima sposta la macchina virtuale su un nodo già aggiornato e lo riattiva, conservando tutte le opzioni di configurazione e le risorse associate. Durante questa operazione, il disco temporaneo viene perso e gli indirizzi IP dinamici associati all'interfaccia di rete virtuale vengono aggiornati.

La ridistribuzione preventiva viene abilitata una sola volta per ogni macchina virtuale. Se si verifica un errore durante il processo, l'operazione viene interrotta, non viene prodotto alcun impatto sulla macchina virtuale e questa viene esclusa dall'iterazione di manutenzione pianificata. L'utente verrà contattato in un secondo momento per una nuova pianificazione e potrà nuovamente pianificare e sequenziare l'impatto sulle proprie macchine virtuali.

