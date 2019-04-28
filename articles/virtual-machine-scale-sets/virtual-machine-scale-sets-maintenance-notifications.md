---
title: Notifiche di manutenzione per set di scalabilità di macchine virtuali in Azure | Microsoft Docs
description: Visualizzare le notifiche di manutenzione e avviare la manutenzione self-service per i set di scalabilità di macchine virtuali in Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: shants
ms.openlocfilehash: 31d4829c6adaf4bd5392ef393dcaefbeb7dc6255
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618456"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Notifiche di manutenzione pianificata per set di scalabilità di macchine virtuali


Azure esegue periodicamente aggiornamenti per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host per le macchine virtuali. Gli aggiornamenti potrebbero includere l'applicazione di patch all'ambiente di hosting o l'aggiornamento e la rimozione di componenti hardware. La maggior parte degli aggiornamenti non influisce sulle macchine virtuali ospitate. Tuttavia, gli aggiornamenti interessano le macchine virtuali in questi scenari:

- Se la manutenzione non richiede un riavvio, Azure usa la migrazione sul posto per sospendere la macchina virtuale mentre l'host viene aggiornato. Le operazioni di manutenzione che non richiedono un riavvio vengono applicate per ogni dominio di errore. L'operazione viene interrotta in caso di ricezione di segnali di integrità di avviso.

- Se la manutenzione richiede un riavvio, si riceve una notifica che indica quando è pianificata la manutenzione. In questi casi, viene anche indicata una finestra temporale in cui avviare la manutenzione manualmente, in un momento ottimale per l'utente.


La manutenzione pianificata che richiede un riavvio viene pianificata in cicli. Ogni ciclo ha un ambito diverso (aree),

- e inizia con un avviso ai clienti. Per impostazione predefinita, la notifica viene inviata ai proprietari della sottoscrizione e ai comproprietari. È possibile aggiungere destinatari e opzioni di messaggistica, come messaggi di posta elettronica, SMS e webhook, alle notifiche tramite gli [avvisi del log attività](../azure-monitor/platform/activity-logs-overview.md) di Azure.  
- Con la notifica viene resa disponibile una *finestra self-service*. Durante questa finestra, è possibile trovare le macchine virtuali incluse nel ciclo. È possibile avviare la manutenzione in modo proattivo in base alle proprie esigenze di pianificazione.
- Dopo l'intervallo in modalità self-service, viene avviato un *intervallo di manutenzione pianificato*. In un determinato momento di questo intervallo, Azure pianifica e applica la manutenzione necessaria alla macchina virtuale. 

La presenza di due finestre consente di avere tempo sufficiente per iniziare la manutenzione e riavviare la macchina virtuale, sapendo comunque quando Azure avvierà automaticamente la manutenzione.

Per richiedere le finestre di manutenzione delle macchine virtuali del set di scalabilità e avviare la manutenzione self-service, è possibile usare il portale di Azure, PowerShell, l'API REST e l'interfaccia della riga di comando di Azure.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>La manutenzione deve iniziare durante l'intervallo in modalità self-service?  

Le linee guida seguenti sono utili per decidere se avviare la manutenzione in un orario scelto.

> [!NOTE] 
> La manutenzione in modalità self-service potrebbe non essere disponibile per tutte le macchine virtuali. Per determinare se per la macchina virtuale è disponibile la ridistribuzione proattiva, cercare l'opzione **Start now** (Inizia ora) nello stato di manutenzione. Attualmente, la manutenzione self-service non è disponibile per Servizi cloud di Azure (ruolo Web o di lavoro) e per Azure Service Fabric.


La manutenzione self-service non è consigliata per distribuzioni che usano *set di disponibilità*. I set di disponibilità sono configurazioni a disponibilità elevata in cui è interessato un solo dominio di aggiornamento alla volta. Per i set di disponibilità:

- Lasciare che Azure attivi la manutenzione. Per la manutenzione che richiede un riavvio, la manutenzione viene eseguita per un dominio di aggiornamento alla volta. I domini di aggiornamento non ricevono necessariamente la manutenzione in modo sequenziale. È prevista una pausa di 30 minuti tra i domini di aggiornamento.
- Se una perdita di capacità temporanea (1/numero domini di aggiornamento) rappresenta un problema, è possibile compensare facilmente con l'allocazione di istanze aggiuntive durante il periodo di manutenzione.
- Per la manutenzione che non richiede un riavvio, gli aggiornamenti vengono applicati a livello di dominio di errore. 
    
**Non** usare la manutenzione self-service negli scenari seguenti: 

- Se si arrestano di frequente le macchine virtuali, manualmente, tramite DevTest Labs, con l'arresto automatico o in base a una pianificazione. La manutenzione self-service in questi scenari potrebbe ripristinare lo stato di manutenzione con un conseguente aumento del tempo di inattività.
- Nelle VM di breve durata che si è certi saranno eliminate prima della fine del ciclo di manutenzione. 
- In carichi di lavoro con uno stato di grandi dimensioni archiviato nel disco locale (temporaneo) che si vuole mantenere dopo l'aggiornamento. 
- Se si ridimensiona la macchina virtuale spesso. Questo scenario potrebbe ripristinare lo stato di manutenzione. 
- Nel caso in cui siano stati adottati eventi pianificati che consentono il failover proattivo o l'arresto normale del carico di lavoro, 15 minuti prima dell'inizio dell'arresto della manutenzione.

**Usare** la manutenzione self-service se si prevede di eseguire la macchina virtuale senza interruzioni durante la fase di manutenzione pianificata e nessuna delle controindicazioni precedenti è applicabile. 

È consigliabile usare la manutenzione self-service nei casi seguenti:

- Necessità di comunicare un intervallo di manutenzione esatto ai propri responsabili o ai clienti finali. 
- Necessità di completare la manutenzione entro una data specifica. 
- Necessità di controllare la sequenza di manutenzione, ad esempio, in un'applicazione a più livelli per garantire un recupero sicuro.
- Necessità di oltre 30 minuti di tempo di recupero per le macchine virtuali tra due domini di aggiornamento. Per controllare l'intervallo tra i domini di aggiornamento, è necessario attivare la manutenzione sulle macchine virtuali di un dominio di aggiornamento alla volta.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Visualizzare set di scalabilità di macchine virtuali interessati dalla manutenzione nel portale

Dopo la programmazione di un'ondata di manutenzione pianificata è possibile visualizzare l'elenco dei set di scalabilità di macchine virtuali interessati dalla prossima ondata tramite il portale di Azure. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra selezionare **Tutti i servizi** e quindi selezionare **Set di scalabilità di macchine virtuali**.
3. In **Set di scalabilità di macchine virtuali** selezionare **Modifica colonne** per aprire l'elenco delle colonne disponibili.
4. Nella sezione **Colonne disponibili** selezionare **Manutenzione self-service** e quindi spostare la voce nell'elenco **Colonne selezionate**. Selezionare **Applica**.  

    Per rendere la voce **Manutenzione self-service** più facile da trovare, è possibile modificare l'opzione selezionata nell'elenco a discesa nella sezione **Colonne disponibili** da **Tutti** a **Proprietà**.

La colonna **Manutenzione self-service** è ora visualizzata nell'elenco dei set di scalabilità di macchine virtuali. Ogni set di scalabilità di macchine virtuali può avere uno dei valori seguenti per la colonna manutenzione self-service:

| Value | DESCRIZIONE |
|-------|-------------|
| Sì | Almeno una macchina virtuale nel set di scalabilità di macchine virtuali si trova in una finestra self-service. È possibile avviare la manutenzione in qualsiasi momento durante questa finestra self-service. | 
| No  | Non sono presenti macchine virtuali in una finestra self-service del set di scalabilità di macchine virtuali interessato. | 
| - | I set di scalabilità di macchine virtuali non fanno parte di un'ondata di manutenzione pianificata.| 

## <a name="notification-and-alerts-in-the-portal"></a>Notifiche e avvisi del portale

Azure comunica il programma di una manutenzione pianificata inviando un messaggio di posta elettronica al proprietario e ai comproprietari della sottoscrizione. È possibile aggiungere destinatari e canali a questa comunicazione creando avvisi del log attività. Per altre informazioni, vedere [Monitorare l'attività di sottoscrizione con il log attività di Azure](../azure-monitor/platform/activity-logs-overview.md).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra selezionare **Monitoraggio**. 
3. Nel riquadro **Monitoraggio - Avvisi (versione classica)** selezionare **+ Aggiungi avviso del log attività**.
4. Nella pagina **Aggiungi avviso del log attività** selezionare o immettere le informazioni richieste. In **Criteri** assicurarsi di impostare i valori seguenti:
   - **Categoria evento**: Selezionare **Integrità dei servizi**.
   - **Servizi**: selezionare **Set di scalabilità di macchine virtuali e Macchine virtuali**.
   - **Tipo**: selezionare **Manutenzione pianificata**. 
    
Per altre informazioni su come configurare gli avvisi del log attività, vedere [Creare avvisi del log attività](../azure-monitor/platform/activity-log-alerts.md).
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Avviare la manutenzione del set di scalabilità di macchine virtuali dal portale

È possibile visualizzare altri dettagli relativi alla manutenzione nella panoramica dei set di scalabilità di macchine virtuali. Se almeno una macchina virtuale nel set di scalabilità di macchine virtuali è inclusa nel ciclo di manutenzione pianificata, viene aggiunta una nuova barra delle notifiche nella parte superiore della pagina. Selezionare la barra delle notifiche per passare alla pagina **Manutenzione**. 

Nella pagina **Manutenzione** è possibile visualizzare quale istanza di macchina virtuale è interessata dalla manutenzione pianificata. Per avviare la manutenzione, selezionare la casella di controllo corrispondente alla macchina virtuale interessata. Selezionare quindi **Avvia la manutenzione**.

Dopo aver avviato la manutenzione, le macchine virtuali interessate nel set di scalabilità di macchine virtuali vengono sottoposte a manutenzione e sono temporaneamente non disponibili. Se si perde la finestra self-service, è comunque possibile visualizzare la finestra temporale durante la quale verrà eseguita la manutenzione del set di scalabilità di macchine virtuali da Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Controllare lo stato della manutenzione con PowerShell

È anche possibile usare Azure PowerShell per vedere quando è pianificata la manutenzione dei diversi set di scalabilità di macchine virtuali. Le informazioni sulla manutenzione pianificata sono disponibili con il cmdlet [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss), usando il parametro `-InstanceView`.
 
Le informazioni vengono restituite solo se la manutenzione è pianificata. Se non è presente alcuna manutenzione pianificata per l'istanza della macchina virtuale, il cmdlet non restituisce informazioni. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

**MaintenanceRedeployStatus** restituisce le proprietà seguenti: 

| Valore | Descrizione |

|-------|---------------| | IsCustomerInitiatedMaintenanceAllowed | Indica se è possibile avviare la manutenzione sulla macchina virtuale in questo momento. | | PreMaintenanceWindowStartTime | Inizio della finestra di manutenzione self-service la possibilità di avviare la manutenzione della macchina virtuale. | | PreMaintenanceWindowEndTime | Fine della finestra di manutenzione self-service la possibilità di avviare la manutenzione della macchina virtuale. | | MaintenanceWindowStartTime | Inizio della manutenzione pianificata in cui Azure avvia la manutenzione della macchina virtuale. | | MaintenanceWindowEndTime | Fine della finestra di manutenzione pianificata in cui Azure avvia la manutenzione della macchina virtuale. | | LastOperationResultCode | Il risultato dell'ultimo tentativo di avviare la manutenzione sulla macchina virtuale. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Avviare la manutenzione dell'istanza della macchina virtuale usando PowerShell

È possibile avviare la manutenzione di una macchina virtuale se **IsCustomerInitiatedMaintenanceAllowed** è impostato su **true**. Usare il cmdlet [Set-AzVmss](/powershell/module/az.compute/set-azvmss) con il parametro `-PerformMaintenance`.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Controllare lo stato della manutenzione con l'interfaccia della riga di comando

È possibile visualizzare le informazioni per la manutenzione pianificata con [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Le informazioni vengono restituite solo se la manutenzione è pianificata. Se non è presente alcuna manutenzione pianificata per l'istanza della macchina virtuale, il comando non restituisce informazioni. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

**MaintenanceRedeployStatus** restituisce le proprietà seguenti per ogni istanza della macchina virtuale: 

| Valore | Descrizione |

|-------|---------------| | IsCustomerInitiatedMaintenanceAllowed | Indica se è possibile avviare la manutenzione sulla macchina virtuale in questo momento. | | PreMaintenanceWindowStartTime | Inizio della finestra di manutenzione self-service la possibilità di avviare la manutenzione della macchina virtuale. | | PreMaintenanceWindowEndTime | Fine della finestra di manutenzione self-service la possibilità di avviare la manutenzione della macchina virtuale. | | MaintenanceWindowStartTime | Inizio della manutenzione pianificata in cui Azure avvia la manutenzione della macchina virtuale. | | MaintenanceWindowEndTime | Fine della finestra di manutenzione pianificata in cui Azure avvia la manutenzione della macchina virtuale. | | LastOperationResultCode | Il risultato dell'ultimo tentativo di avviare la manutenzione sulla macchina virtuale. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Avviare la manutenzione dell'istanza della macchina virtuale usando l'interfaccia della riga di comando

La chiamata seguente avvia la manutenzione in un'istanza della macchina virtuale se `IsCustomerInitiatedMaintenanceAllowed` è impostata su **true**:

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Domande frequenti

**D: Perché è necessario riavviare ora le macchine virtuali?**

**R:** anche se la maggior parte degli aggiornamenti della piattaforma Azure non influisce sulla disponibilità delle macchine virtuali, in alcuni casi, non è possibile evitare di riavviare le macchine virtuali ospitate in Azure. Sono state accumulate diverse modifiche che rendono necessario il riavvio dei server e di conseguenza anche quello delle macchine virtuali.

**D: È sicuro seguire le raccomandazioni per la disponibilità elevata che prevedono l'uso di un set di disponibilità?**

**R:** Le macchine virtuali distribuite in un set di disponibilità o nei set di scalabilità di macchine virtuali usano i domini di aggiornamento. Quando si esegue la manutenzione, Azure rispetta il vincolo dei domini di aggiornamento e non riavvia le macchine virtuali da un dominio di aggiornamento diverso (all'interno dello stesso set di disponibilità). Azure attende anche almeno 30 minuti prima di passare al gruppo successivo di macchine virtuali. 

Per altre informazioni sulla disponibilità elevata, vedere [Regions and availability for virtual machines in Azure](../virtual-machines/windows/regions-and-availability.md) (Aree e disponibilità per le macchine virtuali in Azure).

**D: Come si possono ottenere notifiche per la manutenzione pianificata?**

**R:** un ciclo di manutenzione pianificata viene avviato impostando una programmazione su una o più aree di Azure. Una notifica di posta elettronica viene inviata poco dopo ai proprietari della sottoscrizione (un messaggio di posta elettronica per ogni sottoscrizione). Si possono aggiungere canali e destinatari per questa notifica usando gli avvisi del log attività. Se si distribuisce una macchina virtuale in un'area in cui la manutenzione pianificata è già pianificata, non si riceve la notifica. Controllare invece lo stato di manutenzione della macchina virtuale.

**D: Non sono presenti indicazioni relative alla manutenzione pianificata nel portale, in PowerShell o nell'interfaccia della riga di comando. Qual è il problema?**

**R:** Le informazioni relative alla manutenzione pianificata sono disponibili durante un ciclo di manutenzione pianificata solo per le macchine virtuali interessate dalla manutenzione pianificata. Se i dati non sono visibili, è possibile che il ciclo di manutenzione sia già stato completato (o non sia stato avviato) oppure che la macchina virtuale sia già ospitata in un server aggiornato.

**D: È possibile sapere con esattezza quando la macchina virtuale verrà coinvolta?**

**R:** Quando si imposta la pianificazione, viene definita una finestra temporale di alcuni giorni. La sequenza esatta dei server e delle macchine virtuali entro questa finestra è sconosciuta. Se si vuole conoscere l'ora esatta dell'aggiornamento delle macchine virtuali, è possibile usare gli [eventi pianificati](../virtual-machines/windows/scheduled-events.md). Quando si usano gli eventi pianificati, è possibile eseguire una query all'interno della macchina virtuale e ricevere una notifica 15 minuti prima di un riavvio della macchina virtuale.

**D: Quanto tempo sarà necessario per riavviare la macchina virtuale?**

**R:**  A seconda delle dimensioni della macchina virtuale, il riavvio potrebbe richiedere alcuni minuti durante la finestra di manutenzione self-service. Durante i riavvii avviati da Azure nella finestra di manutenzione pianificata, il riavvio richiede in genere circa 25 minuti. Se si usano Servizi cloud (ruolo Web o di lavoro), set di scalabilità di macchine virtuali o set di disponibilità, sono disponibili 30 minuti tra ogni gruppo di macchine virtuali (domini di aggiornamento) durante l'intervallo di manutenzione pianificato. 

**D: Non sono disponibili informazioni sulla manutenzione per le macchine virtuali. Perché?**

**R:** I motivi per cui non vengono visualizzate informazioni sulla manutenzione per le macchine virtuali sono diversi:
   - Si usa una sottoscrizione contrassegnata come *Microsoft (interno)*.
   - Le macchine virtuali non sono pianificate per la manutenzione. La manutenzione potrebbe essere terminata oppure essere stata annullata o modificata e di conseguenza le macchine virtuali non sono più interessate.
   - La colonna **Manutenzione** non è stata aggiunta alla visualizzazione elenco delle macchine virtuali. Anche se questa colonna è stata aggiunta alla visualizzazione predefinita, se si configura la visualizzazione per mostrare le colonne non predefinite, è necessario aggiungere manualmente la colonna **Manutenzione** alla visualizzazione elenco delle macchine virtuali.

**D: La VM è pianificata per la manutenzione per la seconda volta. Perché?**

**R:** In diversi casi d'uso, la macchina virtuale risulta pianificata per la manutenzione dopo avere già completato la manutenzione e la ridistribuzione:
   - Il ciclo di manutenzione è stato annullato e riavviato con un payload diverso. Potrebbe essere stato rilevato un payload con errori e potrebbe essere semplicemente necessario distribuire un payload aggiuntivo.
   - È stato *ripristinato il servizio* della VM in un altro nodo a causa di un errore hardware.
   - Si è scelto di arrestare (deallocare) e riavviare la VM.
   - È attivo l'**arresto automatico** per la VM.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come registrare eventi di manutenzione da una macchina virtuale usando gli [eventi pianificati](../virtual-machines/windows/scheduled-events.md).
