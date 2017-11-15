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
ms.date: 10/26/2017
ms.author: zivr
ms.openlocfilehash: 80c029866f3d28712be823692f3bf4ce6e210405
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Gestire gli avvisi relativi alla manutenzione pianificata per le macchine virtuali Windows

Azure esegue periodicamente aggiornamenti per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host per le macchine virtuali. Gli aggiornamenti sono considerati modifiche, ad esempio l'applicazione di patch all'ambiente di hosting o l'aggiornamento e la rimozione di componenti hardware. La maggior parte di questi aggiornamenti viene eseguita senza alcuna conseguenza per le macchine virtuali ospitate. Ci sono casi tuttavia in cui gli aggiornamenti hanno conseguenze:

- Se la manutenzione non richiede un riavvio, Azure usa la migrazione sul posto per sospendere la VM mentre l'host viene aggiornato.

- Se la manutenzione richiede un riavvio, si riceve un avviso che informa per quando è pianificata la manutenzione. In questi casi, viene anche indicata una finestra temporale in cui avviare la manutenzione manualmente, in un momento opportuno per l'utente.


La manutenzione pianificata che richiede un riavvio viene pianificata a ondate. Ogni ondata ha un ambito diverso (aree),

- e inizia con un avviso ai clienti. Per impostazione predefinita, l'avviso viene inviato ai comproprietari e ai proprietari della sottoscrizione. Agli avvisi è possibile aggiungere più destinatari e opzioni di messaggistica come messaggio di posta elettronica, SMS e Webhook.  
- Dopo l'avviso viene avviata la finestra self-service. In questa finestra è possibile individuare le macchine virtuali incluse nell'ondata e avviare la manutenzione usando la ridistribuzione proattiva. 
- Dopo la finestra self-service ha inizio la finestra di manutenzione pianificata. In questo intervallo Azure pianifica e applica la manutenzione necessaria alla macchina virtuale. 

La presenza di due finestre consente di avere tempo sufficiente per iniziare la manutenzione e riavviare la macchina virtuale, sapendo comunque quando Azure avvierà automaticamente la manutenzione.


Per eseguire una query sulla finestra di manutenzione della macchina virtuale e avviare la manutenzione self-service è possibile usare il portale di Azure, PowerShell, API REST e interfaccia della riga di comando.

 > [!NOTE]
 > Se si tenta di avviare la manutenzione senza riuscirci, Azure contrassegna la macchina virtuale come **ignorata** e non riavvia il sistema durante la finestra di manutenzione pianificata. L'utente viene contattato in un secondo momento con una nuova pianificazione. 


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

Per avviare la manutenzione di una VM classica, digitare:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>domande frequenti


**D: Perché è necessario riavviare ora le macchine virtuali?**

**R:** Anche se la maggior parte degli aggiornamenti alla piattaforma Azure non ha effetto sulla disponibilità di una macchina virtuale, in alcuni casi non è possibile evitare di riavviare le macchine virtuali ospitate in Azure. Sono state accumulate diverse modifiche che rendono necessario il riavvio dei server e di conseguenza anche quello delle macchine virtuali.

**D: È sicuro seguire le raccomandazioni per la disponibilità elevata che prevedono l'uso di un set di disponibilità?**

**R:**Le macchine virtuali distribuite in un set di disponibilità o nei set di scalabilità di macchine virtuali riconoscono i domini di aggiornamento. Quando si esegue la manutenzione, Azure rispetta il vincolo dei domini di aggiornamento e non riavvia le macchine virtuali da domini di aggiornamento diversi (nello stesso set di disponibilità).  Azure attende anche almeno 30 minuti prima di passare al gruppo successivo di macchine virtuali. 

Per altre informazioni sulla disponibilità elevata, vedere Gestire la disponibilità delle macchine virtuali Windows in Azure o Gestire la disponibilità delle macchine virtuali Linux in Azure.

**D: Si è impostato il ripristino di emergenza in un'altra area. È sicuro?**

**R:** Ogni area di Azure è associata a un'altra area con la stessa collocazione geografica (ad esempio Stati Uniti, Europa o Asia). Gli aggiornamenti pianificati di Azure vengono implementati nelle coppie di aree uno alla volta, per ridurre al minimo il tempo di inattività e il rischio di interruzione delle applicazioni. Durante la manutenzione pianificata, Azure può programmare una finestra simile per consentire agli utenti di avviare la manutenzione, tuttavia la finestra di manutenzione programmata sarà diversa nelle aree associate.  

Per altre informazioni sulle aree di Azure, vedere Aree e disponibilità per le macchine virtuali in Azure,  dove è possibile consultare l'elenco completo delle coppie di aree.

**D: Come si ricevono le notifiche relative alla manutenzione pianificata?**

**R:** Un ciclo di manutenzione pianificata viene avviato impostando una programmazione su una o più aree di Azure. Una notifica di posta elettronica viene inviata poco dopo ai proprietari della sottoscrizione (un messaggio di posta elettronica per ogni sottoscrizione). Si possono configurare altri canali e destinatari per questa notifica usando gli avvisi del log attività. Se si distribuisce una macchina virtuale in un'area in cui la manutenzione pianificata è già programmata, non si riceverà la notifica, ma sarà necessario controllare lo stato della manutenzione della VM.

**D: Non sono presenti indicazioni relative alla manutenzione pianificata nel portale, in Powershell o nell'interfaccia della riga di comando. Perché?**

**R:** Le informazioni relative alla manutenzione pianificata sono disponibili durante un ciclo di manutenzione pianificata solo per le VM che ne saranno interessate. In altre parole, se non sono visualizzati dati, il ciclo di manutenzione potrebbe essere già stato completato (o non avviato) o la macchina virtuale potrebbe essere già ospitata in un server aggiornato.

**D: È consigliabile avviare la manutenzione nella macchina virtuale?**

**R:** In generale, i carichi di lavoro distribuiti in un servizio cloud, in un set di disponibilità o in un set di scalabilità di macchine virtuali sono resilienti per la manutenzione pianificata.  Durante la manutenzione pianificata, l'impatto prodotto interessa un solo dominio di aggiornamento in un dato momento Tenere presente che l'ordine in cui i domini di aggiornamento vengono interessati non è necessariamente sequenziale.

Potrebbe essere necessario avviare la manutenzione manualmente nei casi seguenti:
- L'applicazione viene eseguita in una sola macchina virtuale ed è necessario effettuare tutta la manutenzione durante gli orari di minore attività
- È necessario coordinare l'ora della manutenzione nell'ambito del contratto di servizio
- Sono necessari più di 30 minuti tra il riavvio di una VM e quello di un'altra anche in un set di disponibilità.
- Si vuole disattivare l'intera applicazione (più livelli, più domini di aggiornamento) per completare la manutenzione più rapidamente.

**D: È possibile sapere con esattezza quando la macchina virtuale verrà coinvolta?**

**R:** Quando si imposta la pianificazione, viene definita una finestra temporale di alcuni giorni. La sequenza esatta dei server (e delle VM) entro questa finestra è tuttavia sconosciuta. I clienti che vogliono conoscere l'ora esatta per le VM possono usare gli eventi pianificati ed eseguire una query dalla macchina virtuale e ricevere una notifica 10 minuti prima del riavvio di una VM.
  
**D: Quanto tempo sarà necessario per riavviare la macchina virtuale?**

**R:** A seconda delle dimensioni della VM, il riavvio può richiedere anche diversi minuti. Si noti che, se si usano i servizi cloud, i set di scalabilità o il set di disponibilità, trascorreranno 30 minuti tra ogni gruppo di VM (dominio di aggiornamento). 

**D: Quale sarà l'esperienza nel caso dei servizi cloud, dei set di scalabilità e di Service Fabric?**

**R:** Anche se queste piattaforme sono interessate dalla manutenzione pianificata, i clienti che le usano sono considerati sicuri perché solo le VM di un singolo dominio di aggiornamento saranno interessate a una determinata ora.  

**D: È stato ricevuto un messaggio di posta elettronica sulla rimozione delle autorizzazioni per l'hardware. È uguale alla manutenzione pianificata?**

**R:** anche se la rimozione delle autorizzazioni è un evento di manutenzione pianificata, questo caso d'uso non è ancora stato caricato nella nuova esperienza.  Microsoft ritiene che i clienti possano confondersi ricevendo due messaggi di posta elettronica simili su due diversi cicli di manutenzione pianificati.

**D: Non sono disponibili informazioni sulla manutenzione per le VM. Perché?**

**R:** I motivi per cui non vengono visualizzate informazioni sulla manutenzione per le VM sono diversi:
1.  Si usa una sottoscrizione contrassegnata come Microsoft (interno).
2.  Le VM non sono pianificate per la manutenzione. La manutenzione potrebbe essere terminata oppure essere stata annullata o modificata e di conseguenza le VM non sono più interessate.
3.  La colonna della manutenzione non è stata aggiunta alla visualizzazione elenco delle VM. Anche se questa colonna è stata aggiunta alla visualizzazione predefinita, i clienti con una configurazione che non prevede la visualizzazione delle colonne non predefinite devono aggiungere manualmente la colonna **Manutenzione** alla visualizzazione elenco delle VM.

**D: La VM è pianificata per la manutenzione per la seconda volta. Perché?**

**R:** In diversi casi d'uso la VM risulterà pianificata per la manutenzione dopo avere già completato la ridistribuzione della manutenzione:
1.  Il ciclo di manutenzione è stato annullato e riavviato con un payload diverso. Potrebbe essere stato rilevato un payload con errori e potrebbe essere stato semplicemente necessario distribuire un payload aggiuntivo.
2.  È stato *ripristinato il servizio* della VM in un altro nodo a causa di un errore hardware
3.  Si è scelto di arrestare (deallocare) e riavviare la VM
4.  È attivo l'**arresto automatico** per la VM


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come registrare eventi di manutenzione da una macchina virtuale usando [gli eventi pianificati](scheduled-events.md).
