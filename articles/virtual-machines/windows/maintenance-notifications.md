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
ms.openlocfilehash: b0103acf1e407a6a198159fad227b7ccc25052d2
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Gestire gli avvisi relativi alla manutenzione pianificata per le macchine virtuali Windows

Azure esegue periodicamente aggiornamenti per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host per le macchine virtuali. Gli aggiornamenti sono considerati modifiche, ad esempio l'applicazione di patch all'ambiente di hosting o l'aggiornamento e la rimozione di componenti hardware. La maggior parte di questi aggiornamenti viene eseguita senza alcuna conseguenza per le macchine virtuali ospitate. Ci sono casi tuttavia in cui gli aggiornamenti hanno conseguenze:

- Se la manutenzione non richiede un riavvio, Azure usa la migrazione sul posto per sospendere la VM mentre l'host viene aggiornato.

- Se la manutenzione richiede un riavvio, si riceve un avviso che informa per quando è pianificata la manutenzione. In questi casi, viene anche indicata una finestra temporale in cui avviare la manutenzione manualmente, in un momento opportuno per l'utente.


La manutenzione pianificata che richiede un riavvio viene pianificata a ondate. Ogni ondata ha un ambito diverso (aree),

- e inizia con un avviso ai clienti. Per impostazione predefinita, l'avviso viene inviato ai comproprietari e ai proprietari della sottoscrizione. È possibile aggiungere più destinatari e le opzioni di messaggistica come messaggio di posta elettronica, SMS e ai Webhook, per le notifiche tramite Azure [gli avvisi del registro attività](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).  
- Al momento della notifica, un *self-service finestra* viene reso disponibile. Durante questo periodo, è possibile trovare le macchine virtuali inclusi in questo wave e avviare in modo proattivo manutenzione in base alle proprie esigenze di pianificazione.
- Dopo la finestra di self-service, un *finestra di manutenzione pianificata* inizia. A un certo punto durante questa finestra di Azure pianifica e si applica la manutenzione necessaria per la macchina virtuale. 

La presenza di due finestre consente di avere tempo sufficiente per iniziare la manutenzione e riavviare la macchina virtuale, sapendo comunque quando Azure avvierà automaticamente la manutenzione.


Per eseguire una query sulla finestra di manutenzione della macchina virtuale e avviare la manutenzione self-service è possibile usare il portale di Azure, PowerShell, API REST e interfaccia della riga di comando.

 > [!NOTE]
 > Se si tenta di avviare la manutenzione e la richiesta non riesce, Azure contrassegna la macchina virtuale come **ignorato**. Non sarà in grado di utilizzare l'opzione di manutenzione avviato dal cliente. La macchina virtuale dovrà essere riavviato da Azure durante la fase di manutenzione pianificata.


 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Deve iniziare con manutenzione durante la finestra di self-service?  

Le linee guida seguenti consentono di decidere se è necessario utilizzare questa funzionalità e avviare manutenzione in un secondo momento.

> [!NOTE] 
> Self-service manutenzione potrebbe non essere disponibile per tutte le macchine virtuali. Per determinare se è disponibile per la macchina virtuale la ridistribuzione, cercare il **iniziare** nello stato di manutenzione. Manutenzione self-service non è attualmente disponibile per i servizi Cloud (ruoli Web/di lavoro), Service Fabric e set di scalabilità di macchine virtuali.


Manutenzione self-service non è consigliata per distribuzioni con **set di disponibilità** poiché sono le installazioni a disponibilità elevata, in cui un solo aggiornamento dominio ha un impatto in qualsiasi momento. 
    - Consente di Azure trigger di manutenzione, ma tenere presente che l'ordine dei domini di aggiornamento influenzate non necessariamente avvengono in modo sequenziale e che sia presente una pausa di 30 minuti tra i domini di aggiornamento.
    - Se una temporanea perdita di alcune delle capacità di (numero di domini di aggiornamento/1) è un problema, è possibile per compensare facilmente allocando le istanze di aggiunta durante il periodo di manutenzione. 

**Non** utilizzare self-service di manutenzione negli scenari seguenti: 
    - Se si arresta le macchine virtuali, spesso, sia manualmente, utilizzando DevTest labs, utilizzando arresto automatici o in base alla pianificazione, è possibile ripristinare lo stato di manutenzione e pertanto causare tempi di inattività aggiuntive.
    - In breve durate macchine virtuali che verranno eliminati prima della fine della fase di manutenzione. 
    - Per i carichi di lavoro con uno stato di grandi dimensioni archiviati nel disco locale (temporaneo) che si desidera essere mantenute dopo l'aggiornamento. 
    - Per i casi in cui si ridimensiona la macchina virtuale spesso, perché Impossibile ripristinare lo stato di manutenzione. 
    - Se si hanno adottato per gli eventi pianificati che consentono il failover o spegnimento del carico di lavoro, 15 minuti prima dell'inizio della chiusura di manutenzione

**Utilizzare** manutenzione self-service, se si prevede di eseguire la macchina virtuale senza interruzioni durante la fase di manutenzione pianificata e nessuna delle Counter-indicazioni indicate in precedenza sono applicabile. 

È consigliabile utilizzare self-service di manutenzione nei casi seguenti:
    - È necessario comunicare una finestra di manutenzione esatta per la gestione o un cliente finale. 
    - È necessario completare la manutenzione per una determinata data. 
    - È necessario controllare la sequenza di manutenzione, ad esempio, applicazione a più livelli per garantire il recupero.
    - È necessario più di 30 minuti di tempo di recupero di macchina virtuale tra due domini di aggiornamento (UD). Per controllare l'intervallo tra i domini di aggiornamento, è necessario attivare manutenzione sulle macchine virtuali di un dominio di aggiornamento (UD) alla volta.

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Controllare lo stato della manutenzione con PowerShell

È anche possibile usare Azure Powershell per vedere quando è pianificata la manutenzione delle diverse macchine virtuali. Le informazioni sulla manutenzione pianificata sono disponibili con il cmdlet [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm), usando il parametro `-status`.
 
Le informazioni vengono restituite solo se è presente una manutenzione pianificata. Se non è presente alcuna manutenzione pianificata per la macchina virtuale, il cmdlet non restituisce informazioni. 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
```

MaintenanceRedeployStatus restituisce le proprietà seguenti: 
| Valore | Descrizione   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se in questo momento è possibile avviare la manutenzione per la macchina virtuale ||
| PreMaintenanceWindowStartTime         | Inizio della finestra di manutenzione self-service, che segnala la possibilità di avviare la manutenzione della VM ||
| PreMaintenanceWindowEndTime           | Termine della finestra di manutenzione self-service, che segnala la possibilità di avviare la manutenzione della VM ||
| MaintenanceWindowStartTime            | Inizio della finestra di manutenzione pianificata, che segnala la possibilità di avviare la manutenzione della VM ||
| MaintenanceWindowEndTime              | Termine della finestra di manutenzione pianificata, che segnala la possibilità di avviare la manutenzione della VM ||
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
        $rg = $rgList[$rgIdx]        $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
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

Per avviare manutenzione nella VM classica, digitare:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>domande frequenti


**D: Perché è necessario riavviare ora le macchine virtuali?**

**R:** Anche se la maggior parte degli aggiornamenti alla piattaforma Azure non ha effetto sulla disponibilità di una macchina virtuale, in alcuni casi non è possibile evitare di riavviare le macchine virtuali ospitate in Azure. Sono state accumulate diverse modifiche che rendono necessario il riavvio dei server e di conseguenza anche quello delle macchine virtuali.

**D: È sicuro seguire le raccomandazioni per la disponibilità elevata che prevedono l'uso di un set di disponibilità?**

**R:** set di macchine virtuali distribuite in un disponibilità o il set di scalabilità di macchine virtuali dispone della nozione di domini di aggiornamento (UD). Quando si esegue la manutenzione, Azure rispetta il vincolo dei domini di aggiornamento e non riavvia le macchine virtuali da domini di aggiornamento diversi (nello stesso set di disponibilità).  Azure attende anche almeno 30 minuti prima di passare al gruppo successivo di macchine virtuali. 

Per ulteriori informazioni sulla disponibilità elevata, vedere [aree e disponibilità per le macchine virtuali in Azure](regions-and-availability.MD).

**D: Come si ricevono le notifiche relative alla manutenzione pianificata?**

**R:** Un ciclo di manutenzione pianificata viene avviato impostando una programmazione su una o più aree di Azure. Una notifica di posta elettronica viene inviata poco dopo ai proprietari della sottoscrizione (un messaggio di posta elettronica per ogni sottoscrizione). Si possono configurare altri canali e destinatari per questa notifica usando gli avvisi del log attività. Se si distribuisce una macchina virtuale in un'area in cui la manutenzione pianificata è già programmata, non si riceverà la notifica, ma sarà necessario controllare lo stato della manutenzione della VM.

**D: Non sono presenti indicazioni relative alla manutenzione pianificata nel portale, in Powershell o nell'interfaccia della riga di comando. Perché?**

**R:** Le informazioni relative alla manutenzione pianificata sono disponibili durante un ciclo di manutenzione pianificata solo per le VM che ne saranno interessate. In altre parole, se non sono visualizzati dati, il ciclo di manutenzione potrebbe essere già stato completato (o non avviato) o la macchina virtuale potrebbe essere già ospitata in un server aggiornato.

**D: È possibile sapere con esattezza quando la macchina virtuale verrà coinvolta?**

**R:** Quando si imposta la pianificazione, viene definita una finestra temporale di alcuni giorni. La sequenza esatta dei server (e delle VM) entro questa finestra è tuttavia sconosciuta. I clienti che si desiderano conoscere l'ora esatta per le proprie macchine virtuali possono utilizzare [pianificati eventi](scheduled-events.md) e query dall'interno della macchina virtuale e ricevere una notifica di 15 minuti prima di un riavvio della VM.

**D: Quanto tempo sarà necessario per riavviare la macchina virtuale?**

**R:** A seconda delle dimensioni della VM, il riavvio può richiedere anche diversi minuti. Si noti che in caso di un set di disponibilità, imposta la scala di macchine virtuali o servizi Cloud (ruoli Web/di lavoro), sarà possibile 30 minuti tra ogni gruppo di macchine virtuali (UD). 

**D: che cos'è l'esperienza in caso di servizi Cloud (ruoli Web/di lavoro), Service Fabric e set di scalabilità di macchine virtuali?**

**R:** Anche se queste piattaforme sono interessate dalla manutenzione pianificata, i clienti che le usano sono considerati sicuri perché solo le VM di un singolo dominio di aggiornamento saranno interessate a una determinata ora. Manutenzione self-service non è attualmente disponibile per i servizi Cloud (ruoli Web/di lavoro), Service Fabric e set di scalabilità di macchine virtuali.

**D: È stato ricevuto un messaggio di posta elettronica sulla rimozione delle autorizzazioni per l'hardware. È uguale alla manutenzione pianificata?**

**R:** anche se la rimozione delle autorizzazioni è un evento di manutenzione pianificata, questo caso d'uso non è ancora stato caricato nella nuova esperienza.  

**D: Non sono disponibili informazioni sulla manutenzione per le VM. Perché?**

**R:** I motivi per cui non vengono visualizzate informazioni sulla manutenzione per le VM sono diversi:
1.  Si usa una sottoscrizione contrassegnata come Microsoft (interno).
2.  Le VM non sono pianificate per la manutenzione. La manutenzione potrebbe essere terminata oppure essere stata annullata o modificata e di conseguenza le VM non sono più interessate.
3.  Non si dispone di **manutenzione** colonna aggiunta alla visualizzazione elenco VM. Anche se questa colonna è stata aggiunta alla visualizzazione predefinita, i clienti con una configurazione che non prevede la visualizzazione delle colonne non predefinite devono aggiungere manualmente la colonna **Manutenzione** alla visualizzazione elenco delle VM.

**D: La VM è pianificata per la manutenzione per la seconda volta. Perché?**

**R:** In diversi casi d'uso la VM risulterà pianificata per la manutenzione dopo avere già completato la ridistribuzione della manutenzione:
1.  Il ciclo di manutenzione è stato annullato e riavviato con un payload diverso. Potrebbe essere stato rilevato un payload con errori e potrebbe essere stato semplicemente necessario distribuire un payload aggiuntivo.
2.  È stato *ripristinato il servizio* della VM in un altro nodo a causa di un errore hardware
3.  Si è scelto di arrestare (deallocare) e riavviare la VM
4.  È attivo l'**arresto automatico** per la VM


**D: manutenzione il set di disponibilità richiede molto tempo e risulta ora "ignorato" stato di alcune della disponibilità di set di istanze. Perché?** 

**R:** se si è selezionato per aggiornare più istanze in un set di disponibilità in successione breve, Azure verranno inseriti nella coda le richieste e inizia a aggiornare solo le macchine virtuali nel dominio di aggiornamento (UD) alla volta. Tuttavia, poiché potrebbe esserci una pausa tra domini di aggiornamento, l'aggiornamento potrebbe sembrare richiedere più tempo. Se la coda di aggiornamento richiede più di 60 minuti, verranno visualizzate alcune istanze di **ignorata** stato anche se sono stati aggiornati correttamente. Per evitare questo stato non corretto, aggiornare la disponibilità del set facendo clic solo su istanza all'interno di una disponibilità impostato e attendere l'aggiornamento su tale macchina virtuale per il completamento prima di fare clic sulla macchina virtuale successiva in un dominio di aggiornamento diversi.


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come registrare eventi di manutenzione da una macchina virtuale usando [gli eventi pianificati](scheduled-events.md).
