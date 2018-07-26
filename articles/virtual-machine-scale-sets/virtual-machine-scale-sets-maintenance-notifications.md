---
title: Gestire gli avvisi di manutenzione per set di scalabilità di macchine virtuali in Azure | Microsoft Docs
description: Visualizzare gli avvisi relativi alla manutenzione per set di scalabilità di macchine virtuali in Azure e avviare la manutenzione self-service.
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
ms.openlocfilehash: 4ce984686c2bb320d5d32771d31b81cdec1153af
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38506258"
---
# <a name="handling-planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Gestire gli avvisi relativi alla manutenzione pianificata per set di scalabilità di macchine virtuali

Azure esegue periodicamente aggiornamenti per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host per le macchine virtuali. Gli aggiornamenti sono considerati modifiche, ad esempio l'applicazione di patch all'ambiente di hosting o l'aggiornamento e la rimozione di componenti hardware. La maggior parte di questi aggiornamenti viene eseguita senza alcuna conseguenza per le macchine virtuali ospitate. Ci sono casi tuttavia in cui gli aggiornamenti hanno conseguenze:

- Se la manutenzione non richiede un riavvio, Azure usa la migrazione sul posto per sospendere la VM mentre l'host viene aggiornato. Queste operazioni di manutenzione senza riavvio sono applicate dominio di errore per dominio di errore e l'avanzamento viene arrestato se vengono ricevuti segnali di avviso di integrità.

- Se la manutenzione richiede un riavvio, si riceve un avviso che informa per quando è pianificata la manutenzione. In questi casi, viene anche indicata una finestra temporale in cui avviare la manutenzione manualmente, in un momento opportuno per l'utente.


La manutenzione pianificata che richiede un riavvio viene pianificata a ondate. Ogni ondata ha un ambito diverso (aree),

- e inizia con un avviso ai clienti. Per impostazione predefinita, l'avviso viene inviato ai comproprietari e ai proprietari della sottoscrizione. È possibile aggiungere più destinatari e opzioni di messaggistica, come messaggi di posta elettronica, SMS e webhook, alle notifiche tramite gli [avvisi del log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) di Azure.  
- Al momento della notifica, viene reso disponibile un *intervallo in modalità self-service*. Durante questo intervallo è possibile individuare le macchine virtuali incluse in questo ciclo e avviare in modo proattivo la manutenzione in base alle proprie esigenze di pianificazione.
- Dopo l'intervallo in modalità self-service, viene avviato un *intervallo di manutenzione pianificato*. In un determinato momento di questo intervallo Azure pianifica e applica la manutenzione necessaria alla macchina virtuale. 

La presenza di due finestre consente di avere tempo sufficiente per iniziare la manutenzione e riavviare la macchina virtuale, sapendo comunque quando Azure avvierà automaticamente la manutenzione.


Per eseguire una query sulla finestra di manutenzione delle macchine virtuali contenute in un set di scalabilità di macchine virtuali e avviare la manutenzione self-service è possibile usare il portale di Azure, PowerShell, API REST e interfaccia della riga di comando.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>La manutenzione deve iniziare durante l'intervallo in modalità self-service?  

Le linee guida seguenti consentono di decidere se usare questa funzionalità e avviare la manutenzione nel momento più opportuno.

> [!NOTE] 
> La manutenzione in modalità self-service potrebbe non essere disponibile per tutte le macchine virtuali. Per determinare se per la propria macchina virtuale è disponibile la ridistribuzione proattiva, cercare l'opzione **Start now** (Inizia ora) nello stato di manutenzione. La manutenzione self-service non è attualmente disponibile per Servizi cloud (ruolo Web o di lavoro) e Service Fabric.


La manutenzione self-service non è consigliata per distribuzioni che usano **set di disponibilità** perché questi ultimi sono configurazioni ad alta disponibilità, in cui solo un dominio di aggiornamento è interessato in un determinato momento. 

- Lasciare che Azure attivi la manutenzione. Per la manutenzione che richiede il riavvio, tenere presente che verrà eseguita dominio di aggiornamento per dominio di aggiornamento, che i domini di aggiornamento non ricevono necessariamente la manutenzione in modo sequenziale e che c'è una pausa di 30 minuti tra i domini di aggiornamento.
- Se una perdita di capacità temporanea (1/numero domini di aggiornamento) rappresenta un problema, tuttavia può essere compensata dall'allocazione di istanze aggiuntive durante il periodo di manutenzione.
- Per la manutenzione che non richiede il riavvio, gli aggiornamenti vengono applicati a livello di dominio di errore. 
    
**Non** usare la manutenzione self-service negli scenari seguenti: 

- Se si arrestano di frequente le VM, manualmente, tramite DevTest Labs, con l'arresto automatico o in base a una pianificazione, lo stato di manutenzione potrebbe essere ripristinato con un conseguente aumento del tempo di inattività.
- Nelle VM di breve durata che si è certi saranno eliminate prima della fine del ciclo di manutenzione. 
- In carichi di lavoro con uno stato di grandi dimensioni archiviato nel disco locale (temporaneo) che si desidera mantenere dopo l'aggiornamento. 
- Nei casi in cui si ridimensioni spesso la macchina virtuale perché lo stato di manutenzione potrebbe essere ripristinato. 
- Nel caso in cui siano stati adottati eventi pianificati che consentono il failover proattivo o l'arresto normale del carico di lavoro, 15 minuti prima dell'inizio dell'arresto della manutenzione.

**Usare** la manutenzione self-service se si prevede di eseguire la macchina virtuale senza interruzione durante la fase di manutenzione pianificata e non è applicabile alcun indicatore di conteggio citato in precedenza. 

È consigliabile usare la manutenzione self-service nei casi seguenti:

- Necessità di comunicare un intervallo di manutenzione esatto ai propri responsabili o ai clienti finali. 
- Necessità di completare la manutenzione entro una data specifica. 
- Necessità di controllare la sequenza di manutenzione, ad esempio, di un'applicazione a più livelli per garantire un recupero sicuro.
- Necessità di oltre 30 minuti di tempo di recupero per le macchine virtuali tra due domini di aggiornamento. Per controllare l'intervallo tra i domini di aggiornamento, è necessario attivare la manutenzione sulle macchine virtuali di un dominio di aggiornamento alla volta.

 
## <a name="view-virtual-machine-scale-sets-impacted-by-maintenance-in-the-portal"></a>Visualizzare set di scalabilità di macchine virtuali interessati dalla manutenzione nel portale

Dopo la programmazione di un'ondata di manutenzione pianificata è possibile visualizzare l'elenco dei set di scalabilità di macchine virtuali interessati dalla prossima ondata tramite il portale di Azure. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **All services** (Tutti i servizi) nel riquadro di spostamento a sinistra e scegliere **Set di scalabilità di macchine virtuali**.
3. Nella pagina **Set di scalabilità della macchina virtuale**, scegliere l'opzione**Modifica colonne** nella parte superiore per aprire l'elenco delle colonne disponibili.
4. Nel **sezione delle colonne disponibili**, selezionare la voce **Manutenzione self-service** e usare i pulsanti freccia per spostare i dati nell'elenco **Colonne selezionate**. È possibile spostare l'elenco a discesa nella sezione **Colonne disponibili** da **Tutti** a **Proprietà** per rendere la voce **Manutenzione self-service** più facile da trovare. Dopo aver creato la voce **Manutenzione self-service** nella sezione **Colonne selezionate**, selezionare il pulsante **Applica** nella parte inferiore della pagina. 

Dopo aver completato i passaggi precedenti, la colonna **Manutenzione self-service** verrà visualizzata nell'elenco dei set di scalabilità di macchine virtuali. Ogni set di scalabilità di macchine virtuali può avere uno dei valori seguenti per la colonna manutenzione self-service:

| Valore | DESCRIZIONE |
|-------|-------------|
| Sì | Almeno una macchina virtuale nel set di scalabilità di macchine virtuali si trova in una finestra self-service. È possibile avviare la manutenzione in qualsiasi momento durante questa finestra self-service. | 
| No  | Non sono presenti macchine virtuali in una finestra self-service del set di scalabilità di macchine virtuali interessato. | 
| - | I set di scalabilità di macchine virtuali non fanno parte di un'ondata di manutenzione pianificata.| 

## <a name="notification-and-alerts-in-the-portal"></a>Notifiche e avvisi del portale

Azure comunica il programma di una manutenzione pianificata inviando un messaggio di posta elettronica al proprietario e ai comproprietari della sottoscrizione. È possibile aggiungere altri destinatari e canali a questa comunicazione creando avvisi del log attività di Azure. Per altre informazioni, vedere [Monitorare l'attività di sottoscrizione con il log attività di Azure] (../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Monitoraggio** dal menu a sinistra. 
3. Nel riquadro **Monitoraggio - Avvisi (versione classica)** fare clic su **+ Aggiungi avviso del log attività**.
4. Completare le informazioni contenute sulla pagina **Aggiungi avviso del log attività** e assicurarsi di impostare i seguenti **Criteri**:
   - **Categoria evento**: Integrità dei servizi
   - **Servizi**: Set di scalabilità di macchine virtuali e Macchine virtuali
   - **Tipo**: Manutenzione pianificata 
    
Per altre informazioni su come configurare gli avvisi del log attività, vedere [Creare avvisi del log attività](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Avviare la manutenzione del set di scalabilità di macchine virtuali dal portale

Mentre si esaminano i dettagli dei set di scalabilità di macchine virtuali, è possibile visualizzare altri dettagli relativi alla manutenzione. Se almeno una macchina virtuale nel set di scalabilità di macchine virtuali è inclusa nel ciclo di manutenzione pianificata, verrà aggiunta una nuova barra delle notifiche nella parte superiore della pagina. È possibile fare clic sulla barra delle notifiche per passare alla pagina **Manutenzione** e verificare quale istanza di macchina virtuale è interessata dalla manutenzione pianificata. 

Da qui, sarà possibile avviare la manutenzione selezionando la casella corrispondente alla macchina virtuale e facendo clic su **Avvia manutenzione**.

Una volta avviata la manutenzione, le macchine virtuali interessate nel set di scalabilità di macchine virtuali verranno sottoposte a manutenzione e saranno temporaneamente non disponibili. Se si perde la finestra Self-service, è comunque possibile visualizzarla quando viene eseguita la manutenzione del set di scalabilità di macchine virtuali da Azure.
 
## <a name="check-maintenance-status-using-powershell"></a>Controllare lo stato della manutenzione con PowerShell

È anche possibile usare Azure Powershell per vedere quando è pianificata la manutenzione dei diversi set di scalabilità di macchine virtuali. Le informazioni sulla manutenzione pianificata sono disponibili con il cmdlet [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss), usando il parametro `-InstanceView`.
 
Le informazioni vengono restituite solo se è presente una manutenzione pianificata. Se non è presente alcuna manutenzione pianificata per l'istanza della macchina virtuale, il cmdlet non restituisce informazioni. 

```powershell
Get-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

MaintenanceRedeployStatus restituisce le proprietà seguenti: 
| Valore | DESCRIZIONE   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se in questo momento è possibile avviare la manutenzione per la macchina virtuale ||
| PreMaintenanceWindowStartTime         | Inizio della finestra di manutenzione self-service, che segnala la possibilità di avviare la manutenzione della VM ||
| PreMaintenanceWindowEndTime           | Termine della finestra di manutenzione self-service, che segnala la possibilità di avviare la manutenzione della VM ||
| MaintenanceWindowStartTime            | Inizio della manutenzione pianificata in cui Azure avvia la manutenzione della VM ||
| MaintenanceWindowEndTime              | Fine dell'intervallo pianificato di manutenzione in cui Azure avvia la manutenzione della macchina virtuale ||
| LastOperationResultCode               | Risultato dell'ultimo tentativo di avviare la manutenzione della macchina virtuale ||



### <a name="start-maintenance-on-your-vm-instance-using-powershell"></a>Avviare la manutenzione dell'istanza della macchina virtuale usando PowerShell

È possibile avviare la manutenzione di una macchina virtuale se **IsCustomerInitiatedMaintenanceAllowed** è impostato su true usando il cmdlet [Set-AzureRmVmss](/powershell/module/azurerm.compute/set-azurermvmss) con il parametro `-PerformMaintenance`.

```powershell
Set-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-using-cli"></a>Controllare lo stato della manutenzione con l'interfaccia della riga di comando

È possibile visualizzare le informazioni per la manutenzione pianificata con [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Le informazioni vengono restituite solo se è presente una manutenzione pianificata. Se non è presente alcuna manutenzione pianificata per l'istanza della macchina virtuale, il comando non restituisce informazioni. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

MaintenanceRedeployStatus restituisce le proprietà seguenti per ogni istanza della macchina virtuale: 
| Valore | DESCRIZIONE   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se in questo momento è possibile avviare la manutenzione per la macchina virtuale ||
| PreMaintenanceWindowStartTime         | Inizio della finestra di manutenzione self-service, che segnala la possibilità di avviare la manutenzione della VM ||
| PreMaintenanceWindowEndTime           | Termine della finestra di manutenzione self-service, che segnala la possibilità di avviare la manutenzione della VM ||
| MaintenanceWindowStartTime            | Inizio della manutenzione pianificata in cui Azure avvia la manutenzione della VM ||
| MaintenanceWindowEndTime              | Fine dell'intervallo pianificato di manutenzione in cui Azure avvia la manutenzione della macchina virtuale ||
| LastOperationResultCode               | Risultato dell'ultimo tentativo di avviare la manutenzione della macchina virtuale ||


### <a name="start-maintenance-on-your-vm-instance-using-cli"></a>Avviare la manutenzione dell'istanza della macchina virtuale usando l'interfaccia della riga di comando

La chiamata seguente avvia la manutenzione in un'istanza della macchina virtuale se `IsCustomerInitiatedMaintenanceAllowed` è impostata su true.

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Domande frequenti


**D: Perché è necessario riavviare ora le macchine virtuali?**

**R:** Anche se la maggior parte degli aggiornamenti alla piattaforma Azure non ha effetto sulla disponibilità di una macchina virtuale, in alcuni casi non è possibile evitare di riavviare le macchine virtuali ospitate in Azure. Sono state accumulate diverse modifiche che rendono necessario il riavvio dei server e di conseguenza anche quello delle macchine virtuali.

**D: È sicuro seguire le raccomandazioni per la disponibilità elevata che prevedono l'uso di un set di disponibilità?**

**R:** Le macchine virtuali distribuite in un set di disponibilità o nei set di scalabilità di macchine virtuali riconoscono i domini di aggiornamento. Quando si esegue la manutenzione, Azure rispetta il vincolo dei domini di aggiornamento e non riavvia le macchine virtuali da domini di aggiornamento diversi (nello stesso set di disponibilità).  Azure attende anche almeno 30 minuti prima di passare al gruppo successivo di macchine virtuali. 

Per altre informazioni sulla disponibilità elevata, vedere [Regions and availability for virtual machines in Azure](../virtual-machines/windows/regions-and-availability.md) (Aree e disponibilità per le macchine virtuali in Azure).

**D: Come si ricevono le notifiche relative alla manutenzione pianificata?**

**R:** Un ciclo di manutenzione pianificata viene avviato impostando una programmazione su una o più aree di Azure. Una notifica di posta elettronica viene inviata poco dopo ai proprietari della sottoscrizione (un messaggio di posta elettronica per ogni sottoscrizione). Si possono configurare altri canali e destinatari per questa notifica usando gli avvisi del log attività. Se si distribuisce una macchina virtuale in un'area in cui la manutenzione pianificata è già programmata, non si riceverà la notifica, ma sarà necessario controllare lo stato della manutenzione della VM.

**D: Non sono presenti indicazioni relative alla manutenzione pianificata nel portale, in Powershell o nell'interfaccia della riga di comando. Qual è il problema?**

**R:** Le informazioni relative alla manutenzione pianificata sono disponibili durante un ciclo di manutenzione pianificata solo per le macchine virtuali che ne saranno interessate. In altre parole, se non sono visualizzati dati, il ciclo di manutenzione potrebbe essere già stato completato (o non avviato) o la macchina virtuale potrebbe essere già ospitata in un server aggiornato.

**D: È possibile sapere con esattezza quando la macchina virtuale verrà coinvolta?**

**R:** Quando si imposta la pianificazione, viene definita una finestra temporale di alcuni giorni. La sequenza esatta dei server (e delle VM) entro questa finestra è tuttavia sconosciuta. I clienti che vogliono conoscere l'ora esatta per le proprie macchine virtuali possono usare gli [eventi pianificati](../virtual-machines/windows/scheduled-events.md), eseguire query dalla macchina virtuale e ricevere una notifica 15 minuti prima del riavvio di una macchina virtuale.

**D: Quanto tempo sarà necessario per riavviare la macchina virtuale?**

**R:** A seconda delle dimensioni della macchina virtuale, il riavvio potrebbe richiedere alcuni minuti durante l'intervallo di manutenzione self-service. Durante i riavvii avviati da Azure nell'intervallo di manutenzione pianificato, il riavvio richiede in genere circa 25 minuti. Si noti che nel caso in cui si usino Servizi cloud (ruolo Web o di lavoro), set di scalabilità di macchine virtuali o set di disponibilità, saranno disponibili 30 minuti tra ogni gruppo di macchine virtuali (domini di aggiornamento) durante l'intervallo di manutenzione pianificato. 

**D: Non sono disponibili informazioni sulla manutenzione per le VM. Perché?**

**R:** I motivi per cui non vengono visualizzate informazioni sulla manutenzione per le VM sono diversi:
   - Si usa una sottoscrizione contrassegnata come Microsoft (interno).
   - Le VM non sono pianificate per la manutenzione. La manutenzione potrebbe essere terminata oppure essere stata annullata o modificata e di conseguenza le macchine virtuali non sono più interessate.
   - La colonna **Manutenzione** non è stata aggiunta alla visualizzazione elenco delle macchine virtuali. Anche se questa colonna è stata aggiunta alla visualizzazione predefinita, i clienti con una configurazione che non prevede la visualizzazione delle colonne non predefinite devono aggiungere manualmente la colonna **Manutenzione** alla visualizzazione elenco delle VM.

**D: La VM è pianificata per la manutenzione per la seconda volta. Perché?**

**R:** In diversi casi d'uso la VM risulterà pianificata per la manutenzione dopo avere già completato la ridistribuzione della manutenzione:
   - Il ciclo di manutenzione è stato annullato e riavviato con un payload diverso. Potrebbe essere stato rilevato un payload con errori e potrebbe essere stato semplicemente necessario distribuire un payload aggiuntivo.
   - È stato *ripristinato il servizio* della VM in un altro nodo a causa di un errore hardware.
   - Si è scelto di arrestare (deallocare) e riavviare la VM.
   - È attivo l'**arresto automatico** per la VM.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come registrare eventi di manutenzione da una macchina virtuale usando [gli eventi pianificati](../virtual-machines/windows/scheduled-events.md).
