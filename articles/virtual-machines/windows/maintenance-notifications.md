---
title: Gestire gli avvisi di manutenzione per macchine virtuali Windows in Azure | Microsoft Docs
description: Visualizzare gli avvisi relativi alla manutenzione per macchine virtuali Windows in esecuzione in Azure e avviare la manutenzione self-service.
services: virtual-machines-windows
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: zivr
ms.openlocfilehash: d6d8507508ef1946c1dfa41c47ae81f51c0ad4ef
ms.sourcegitcommit: 8fc9b78a2a3625de2cecca0189d6ee6c4d598be3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/29/2017
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Gestire gli avvisi relativi alla manutenzione pianificata per le macchine virtuali Windows

Azure esegue periodicamente aggiornamenti per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host per le macchine virtuali. Gli aggiornamenti sono considerati modifiche, ad esempio l'applicazione di patch all'ambiente di hosting o l'aggiornamento e la rimozione di componenti hardware. La maggior parte di questi aggiornamenti viene eseguita senza alcuna conseguenza per le macchine virtuali ospitate. Ci sono casi tuttavia in cui gli aggiornamenti hanno conseguenze:

- Se la manutenzione non richiede un riavvio, Azure usa la migrazione sul posto per sospendere la VM mentre l'host viene aggiornato.

- Se la manutenzione richiede un riavvio, si riceve un avviso che informa per quando è pianificata la manutenzione. In questi casi, viene anche indicata una finestra temporale in cui avviare la manutenzione manualmente, in un momento opportuno per l'utente.


La manutenzione pianificata che richiede un riavvio viene pianificata a ondate. Ogni ondata ha un ambito diverso (aree),

- e inizia con un avviso ai clienti. Per impostazione predefinita, l'avviso viene inviato ai comproprietari e ai proprietari della sottoscrizione. È possibile aggiungere più destinatari e opzioni di messaggistica, come messaggi di posta elettronica, SMS e webhook, alle notifiche tramite gli [avvisi del log attività](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) di Azure.  
- Al momento della notifica, viene reso disponibile un *intervallo in modalità self-service*. Durante questo intervallo è possibile individuare le macchine virtuali incluse in questo ciclo e avviare in modo proattivo la manutenzione in base alle proprie esigenze di pianificazione.
- Dopo l'intervallo in modalità self-service, viene avviato un *intervallo di manutenzione pianificato*. In un determinato momento di questo intervallo Azure pianifica e applica la manutenzione necessaria alla macchina virtuale. 

La presenza di due finestre consente di avere tempo sufficiente per iniziare la manutenzione e riavviare la macchina virtuale, sapendo comunque quando Azure avvierà automaticamente la manutenzione.


Per eseguire una query sulla finestra di manutenzione della macchina virtuale e avviare la manutenzione self-service è possibile usare il portale di Azure, PowerShell, API REST e interfaccia della riga di comando.

 > [!NOTE]
 > Se si tenta di avviare la manutenzione e la richiesta non riesce, Azure contrassegna la macchina virtuale come **ignorata**. Non sarà più possibile usare l'opzione di manutenzione avviata dal cliente e la macchina virtuale dovrà essere riavviata da Azure durante la fase di manutenzione pianificata.


 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>L'uso dell'opzione di manutenzione deve iniziare durante l'intervallo in modalità self-service?  

Le linee guida seguenti consentono di decidere se è necessario usare questa funzionalità e avviare la manutenzione nel momento più opportuno.

> [!NOTE] 
> La manutenzione in modalità self-service potrebbe non essere disponibile per tutte le macchine virtuali. Per determinare se per la propria macchina virtuale è disponibile la ridistribuzione proattiva, cercare l'opzione **Start now** (Inizia ora) nello stato di manutenzione. La manutenzione self-service non è attualmente disponibile per Servizi cloud (ruolo Web o di lavoro), Service Fabric e set di scalabilità di macchine virtuali.


La manutenzione self-service non è consigliata per distribuzioni che usano **set di disponibilità** perché questi ultimi sono configurazioni ad alta disponibilità, in cui solo un dominio di aggiornamento è interessato in un determinato momento. 
    - È consigliabile consentire ad Azure di attivare la manutenzione, ma è opportuno ricordare che l'ordine dei domini di aggiornamento interessati non deve necessariamente essere sequenziale e che tra i domini di aggiornamenti intercorre una pausa di 30 minuti.
    - Se una perdita di capacità temporanea (1/numero domini di aggiornamento) rappresenta un problema, tuttavia può essere compensata dall'allocazione di istanze aggiuntive durante il periodo di manutenzione **Non** usare la manutenzione self-service negli scenari seguenti: 
    - Arresto frequente delle macchine virtuali, manualmente, tramite DevTest Labs o l'arresto automatico o in base a una pianificazione, perché lo stato di manutenzione potrebbe essere ripristinato con un conseguente aumento del tempo di inattività.
    - In macchine virtuali di breve durata di cui si sa che verranno eliminate prima della fine del ciclo di manutenzione. 
    - In carichi di lavoro con uno stato di grandi dimensioni archiviato nel disco locale (temporaneo) che si desidera mantenere dopo l'aggiornamento. 
    - Nei casi in cui si ridimensioni spesso la macchina virtuale perché lo stato di manutenzione potrebbe essere ripristinato. 
    - Nel caso in cui siano stati adottati eventi pianificati che consentono il failover proattivo o l'arresto normale del carico di lavoro, 15 minuti prima dell'inizio dell'arresto della manutenzione.

**Usare** la manutenzione self-service se si prevede di eseguire la macchina virtuale senza interruzione durante la fase di manutenzione pianificata e non è applicabile alcun indicatore di conteggio citato in precedenza. 

È consigliabile usare la manutenzione self-service nei casi seguenti:
    - Necessità di comunicare un intervallo di manutenzione esatto ai propri responsabili o ai clienti finali. 
    - Necessità di completare la manutenzione entro una data specifica. 
    - Necessità di controllare la sequenza di manutenzione, ad esempio, applicazione a più livelli per garantire un recupero sicuro.
    - Necessità di oltre 30 minuti di tempo di recupero per le macchine virtuali tra due domini di aggiornamento. Per controllare l'intervallo tra i domini di aggiornamento, è necessario attivare la manutenzione sulle macchine virtuali di un dominio di aggiornamento alla volta.

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Controllare lo stato della manutenzione con PowerShell

È anche possibile usare Azure Powershell per vedere quando è pianificata la manutenzione delle diverse macchine virtuali. Le informazioni sulla manutenzione pianificata sono disponibili con il cmdlet [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm), usando il parametro `-status`.
 
Le informazioni vengono restituite solo se è presente una manutenzione pianificata. Se non è presente alcuna manutenzione pianificata per la macchina virtuale, il cmdlet non restituisce informazioni. 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
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



È anche possibile ottenere lo stato di manutenzione per tutte le macchine virtuali in un gruppo di risorse tramite [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm), senza specificare una macchina virtuale.
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName -Status
```

La funzione seguente di PowerShell accetta l'ID sottoscrizione e visualizza un elenco di macchine virtuali per le quali è stata pianificata la manutenzione.

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Avviare la manutenzione della macchina virtuale usando PowerShell

Usando le informazioni derivate dalla funzione nella sezione precedente, il codice seguente avvia la manutenzione in una macchina virtuale se **IsCustomerInitiatedMaintenanceAllowed** è impostato su true.

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Distribuzioni classiche

Se si dispone ancora di macchine virtuali legacy distribuite tramite il modello di distribuzione classico, è possibile usare PowerShell per eseguire query per le macchine virtuali e avviare la manutenzione.

Per ottenere lo stato di manutenzione di una macchina virtuale, digitare:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Per avviare la manutenzione di una VM classica, digitare:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>Domande frequenti


**D: Perché è necessario riavviare ora le macchine virtuali?**

**R:** Anche se la maggior parte degli aggiornamenti alla piattaforma Azure non ha effetto sulla disponibilità di una macchina virtuale, in alcuni casi non è possibile evitare di riavviare le macchine virtuali ospitate in Azure. Sono state accumulate diverse modifiche che rendono necessario il riavvio dei server e di conseguenza anche quello delle macchine virtuali.

**D: È sicuro seguire le raccomandazioni per la disponibilità elevata che prevedono l'uso di un set di disponibilità?**

**R:** Le macchine virtuali distribuite in un set di disponibilità o nei set di scalabilità di macchine virtuali riconoscono i domini di aggiornamento. Quando si esegue la manutenzione, Azure rispetta il vincolo dei domini di aggiornamento e non riavvia le macchine virtuali da domini di aggiornamento diversi (nello stesso set di disponibilità).  Azure attende anche almeno 30 minuti prima di passare al gruppo successivo di macchine virtuali. 

Per altre informazioni sulla disponibilità elevata, vedere [Regions and availability for virtual machines in Azure](regions-and-availability.MD) (Aree e disponibilità per le macchine virtuali in Azure).

**D: Come si ricevono le notifiche relative alla manutenzione pianificata?**

**R:** Un ciclo di manutenzione pianificata viene avviato impostando una programmazione su una o più aree di Azure. Una notifica di posta elettronica viene inviata poco dopo ai proprietari della sottoscrizione (un messaggio di posta elettronica per ogni sottoscrizione). Si possono configurare altri canali e destinatari per questa notifica usando gli avvisi del log attività. Se si distribuisce una macchina virtuale in un'area in cui la manutenzione pianificata è già programmata, non si riceverà la notifica, ma sarà necessario controllare lo stato della manutenzione della VM.

**D: Non sono presenti indicazioni relative alla manutenzione pianificata nel portale, in Powershell o nell'interfaccia della riga di comando. Perché?**

**R:** Le informazioni relative alla manutenzione pianificata sono disponibili durante un ciclo di manutenzione pianificata solo per le VM che ne saranno interessate. In altre parole, se non sono visualizzati dati, il ciclo di manutenzione potrebbe essere già stato completato (o non avviato) o la macchina virtuale potrebbe essere già ospitata in un server aggiornato.

**D: È possibile sapere con esattezza quando la macchina virtuale verrà coinvolta?**

**R:** Quando si imposta la pianificazione, viene definita una finestra temporale di alcuni giorni. La sequenza esatta dei server (e delle VM) entro questa finestra è tuttavia sconosciuta. I clienti che desiderano conoscere l'ora esatta per le proprie macchine virtuali possono usare gli [eventi pianificati](scheduled-events.md), eseguire query dalla macchina virtuale e ricevere una notifica 15 minuti prima del riavvio di una macchina virtuale.

**D: Quanto tempo sarà necessario per riavviare la macchina virtuale?**

**R:** A seconda delle dimensioni della macchina virtuale, il riavvio potrebbe richiedere alcuni minuti durante l'intervallo di manutenzione self-service. Durante i riavvii avviati da Azure nell'intervallo di manutenzione pianificato, il riavvio richiede in genere circa 25 minuti. Si noti che nel caso in cui si usino Servizi cloud (ruolo Web o di lavoro), set di scalabilità di macchine virtuali o set di disponibilità, saranno disponibili 30 minuti tra ogni gruppo di macchine virtuali (domini di aggiornamento) durante l'intervallo di manutenzione pianificato. 

**D: Qual è l'esperienza nel caso di Servizi cloud (ruolo Web o di lavoro), Service Fabric e set di scalabilità di macchine virtuali?**

**R:** Anche se queste piattaforme sono interessate dalla manutenzione pianificata, i clienti che le usano sono considerati sicuri perché solo le VM di un singolo dominio di aggiornamento saranno interessate a una determinata ora. La manutenzione self-service non è attualmente disponibile per Servizi cloud (ruolo Web o di lavoro), Service Fabric e set di scalabilità di macchine virtuali.

**D: È stato ricevuto un messaggio di posta elettronica sulla rimozione delle autorizzazioni per l'hardware. È uguale alla manutenzione pianificata?**

**R:** anche se la rimozione delle autorizzazioni è un evento di manutenzione pianificata, questo caso d'uso non è ancora stato caricato nella nuova esperienza.  

**D: Non sono disponibili informazioni sulla manutenzione per le VM. Perché?**

**R:** I motivi per cui non vengono visualizzate informazioni sulla manutenzione per le VM sono diversi:
1.  Si usa una sottoscrizione contrassegnata come Microsoft (interno).
2.  Le VM non sono pianificate per la manutenzione. La manutenzione potrebbe essere terminata oppure essere stata annullata o modificata e di conseguenza le VM non sono più interessate.
3.  La colonna **Manutenzione** non è stata aggiunta alla visualizzazione elenco delle macchine virtuali. Anche se questa colonna è stata aggiunta alla visualizzazione predefinita, i clienti con una configurazione che non prevede la visualizzazione delle colonne non predefinite devono aggiungere manualmente la colonna **Manutenzione** alla visualizzazione elenco delle VM.

**D: La VM è pianificata per la manutenzione per la seconda volta. Perché?**

**R:** In diversi casi d'uso la VM risulterà pianificata per la manutenzione dopo avere già completato la ridistribuzione della manutenzione:
1.  Il ciclo di manutenzione è stato annullato e riavviato con un payload diverso. Potrebbe essere stato rilevato un payload con errori e potrebbe essere stato semplicemente necessario distribuire un payload aggiuntivo.
2.  È stato *ripristinato il servizio* della VM in un altro nodo a causa di un errore hardware
3.  Si è scelto di arrestare (deallocare) e riavviare la VM
4.  È attivo l'**arresto automatico** per la VM


**D: La manutenzione del set di disponibilità richiede molto tempo e lo stato di alcune istanze del set di disponibilità risulta ora "ignorato". Perché?** 

**R:** Se è stato fatto clic per aggiornare più istanze in un set di disponibilità in breve successione, Azure inserisce queste richieste in coda e inizia ad aggiornare solo le macchine virtuali in un dominio di aggiornamento alla volta. Poiché tra i domini di aggiornamento può intercorrere un periodo di pausa, l'aggiornamento potrebbe sembrare più lungo. Se la coda di aggiornamento richiede più tempo di 60 minuti, per alcune istanze verrà visualizzato lo stato **ignorato** anche se sono state aggiornate in modo corretto. Per evitare questo stato non corretto, aggiornare i set di disponibilità facendo clic solo su un'istanza in un set di disponibilità e attendere il completamento dell'aggiornamento su tale macchina virtuale prima di fare clic sulla macchina virtuale successiva in un dominio di aggiornamento diverso.


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come registrare eventi di manutenzione da una macchina virtuale usando [gli eventi pianificati](scheduled-events.md).
