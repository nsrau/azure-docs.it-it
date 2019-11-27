---
title: Notifiche di manutenzione per le macchine virtuali di Azure | Microsoft Docs
description: Panoramica delle notifiche di manutenzione per le macchine virtuali in esecuzione in Azure.
services: virtual-machines
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: ee7a03f27ab03feadedb7ac8441675a90468655c
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535808"
---
# <a name="handling-planned-maintenance-notifications"></a>Gestione delle notifiche di manutenzione pianificata

Azure esegue periodicamente aggiornamenti per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host per le macchine virtuali. Gli aggiornamenti sono considerati modifiche, ad esempio l'applicazione di patch all'ambiente di hosting o l'aggiornamento e la rimozione di componenti hardware. La maggior parte di questi aggiornamenti viene completata senza alcun effetto sulle macchine virtuali ospitate. Ci sono casi tuttavia in cui gli aggiornamenti hanno conseguenze:

- Se la manutenzione non richiede un riavvio, Azure usa la migrazione sul posto per sospendere la VM mentre l'host viene aggiornato. Per questi tipi di operazioni di manutenzione viene applicato un dominio di errore per dominio di errore. L'operazione viene interrotta in caso di ricezione di segnali di integrità di avviso.

- Se la manutenzione richiede un riavvio, si riceve un avviso che informa per quando è pianificata la manutenzione. Viene fornito un intervallo di tempo di circa 35 giorni, in cui è possibile avviare manualmente la manutenzione, quando funziona.


La manutenzione pianificata che richiede un riavvio viene pianificata in cicli. Ogni ondata ha un ambito diverso (aree),

- e inizia con un avviso ai clienti. Per impostazione predefinita, l'avviso viene inviato ai comproprietari e ai proprietari della sottoscrizione. È possibile aggiungere altri destinatari e opzioni di messaggistica, ad esempio posta elettronica, SMS e webhook, usando gli [avvisi del log attività](../azure-monitor/platform/activity-logs-overview.md).  
- Una volta inviata una notifica, viene resa disponibile una *finestra Self-Service* . Durante questa finestra è possibile eseguire una query sulle macchine virtuali interessate e avviare la manutenzione in base alle proprie esigenze di pianificazione. La finestra Self-Service è in genere di circa 35 giorni.
- Dopo l'intervallo in modalità self-service, viene avviato un *intervallo di manutenzione pianificato*. In un determinato momento di questo intervallo Azure pianifica e applica la manutenzione necessaria alla macchina virtuale. 

La presenza di due finestre consente di avere tempo sufficiente per iniziare la manutenzione e riavviare la macchina virtuale, sapendo comunque quando Azure avvierà automaticamente la manutenzione.


Per eseguire una query sulla finestra di manutenzione della macchina virtuale e avviare la manutenzione self-service è possibile usare il portale di Azure, PowerShell, API REST e interfaccia della riga di comando.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>L'uso dell'opzione di manutenzione deve iniziare durante l'intervallo in modalità self-service?  

Le linee guida seguenti consentono di decidere se usare questa funzionalità e avviare la manutenzione nel momento più opportuno. 

> [!NOTE] 
> La manutenzione in modalità self-service potrebbe non essere disponibile per tutte le macchine virtuali. Per determinare se per la propria macchina virtuale è disponibile la ridistribuzione proattiva, cercare l'opzione **Start now** (Inizia ora) nello stato di manutenzione. La manutenzione self-service non è attualmente disponibile per Servizi cloud (ruolo Web o di lavoro) e Service Fabric.


La manutenzione Self-Service non è consigliata per le distribuzioni che usano i **set di disponibilità**. I set di disponibilità sono già aggiornati un solo dominio di aggiornamento alla volta. 

- Lasciare che Azure attivi la manutenzione. Per la manutenzione che richiede il riavvio, la manutenzione effettuerà l'aggiornamento del dominio in base al dominio di aggiornamento. I domini di aggiornamento non ricevono necessariamente la manutenzione in sequenza e si verifica una pausa di 30 minuti tra i domini di aggiornamento. 
- Se una perdita temporanea di una determinata capacità (1 dominio di aggiornamento) rappresenta un problema, è possibile aggiungere istanze durante il periodo di manutenzione. 
- Per la manutenzione che non richiede il riavvio, gli aggiornamenti vengono applicati a livello di dominio di errore. 

**Non** usare la manutenzione self-service negli scenari seguenti: 
- Se si arrestano di frequente le VM, manualmente, tramite DevTest Labs, con l'arresto automatico o in base a una pianificazione, lo stato di manutenzione potrebbe essere ripristinato con un conseguente aumento del tempo di inattività.
- Nelle VM di breve durata che si è certi saranno eliminate prima della fine del ciclo di manutenzione. 
- In carichi di lavoro con uno stato di grandi dimensioni archiviato nel disco locale (temporaneo) che si desidera mantenere dopo l'aggiornamento. 
- Nei casi in cui si ridimensioni spesso la macchina virtuale perché lo stato di manutenzione potrebbe essere ripristinato. 
- Nel caso in cui siano stati adottati eventi pianificati che consentono il failover proattivo o l'arresto normale del carico di lavoro, 15 minuti prima dell'inizio dell'arresto della manutenzione

**Usare** la manutenzione self-service se si prevede di eseguire la macchina virtuale senza interruzione durante la fase di manutenzione pianificata e non è applicabile alcun indicatore di conteggio citato in precedenza. 

È consigliabile usare la manutenzione self-service nei casi seguenti:
- Necessità di comunicare un intervallo di manutenzione esatto ai propri responsabili o ai clienti finali. 
- Necessità di completare la manutenzione entro una data specifica. 
- Necessità di controllare la sequenza di manutenzione, ad esempio applicazione a più livelli per garantire un recupero sicuro.
- Sono necessari più di 30 minuti di tempo per il ripristino delle macchine virtuali tra due domini di aggiornamento (UDs). Per controllare l'intervallo tra i domini di aggiornamento, è necessario attivare la manutenzione sulle macchine virtuali di un dominio di aggiornamento alla volta.


## <a name="faq"></a>FAQ


**D: Perché è necessario riavviare ora le macchine virtuali?**

**R:** Anche se la maggior parte degli aggiornamenti alla piattaforma Azure non ha effetto sulla disponibilità di una macchina virtuale, in alcuni casi non è possibile evitare di riavviare le macchine virtuali ospitate in Azure. Sono state accumulate diverse modifiche che rendono necessario il riavvio dei server e di conseguenza anche quello delle macchine virtuali.

**D: È sicuro seguire le raccomandazioni per la disponibilità elevata che prevedono l'uso di un set di disponibilità?**

**R:** Le macchine virtuali distribuite in un set di disponibilità o nei set di scalabilità di macchine virtuali riconoscono i domini di aggiornamento. Quando si esegue la manutenzione, Azure rispetta il vincolo dei domini di aggiornamento e non riavvia le macchine virtuali da domini di aggiornamento diversi (nello stesso set di disponibilità).  Azure attende anche almeno 30 minuti prima di passare al gruppo successivo di macchine virtuali. 

Per altre informazioni sulla disponibilità elevata, vedere [disponibilità per le macchine virtuali in Azure](/linux/availability.md).

**D: Come si ricevono le notifiche relative alla manutenzione pianificata?**

**R:** Un ciclo di manutenzione pianificata viene avviato impostando una programmazione su una o più aree di Azure. Una notifica di posta elettronica viene inviata poco dopo ai proprietari della sottoscrizione (un messaggio di posta elettronica per ogni sottoscrizione). Si possono configurare altri canali e destinatari per questa notifica usando gli avvisi del log attività. Se si distribuisce una macchina virtuale in un'area in cui la manutenzione pianificata è già programmata, non si riceverà la notifica, ma sarà necessario controllare lo stato della manutenzione della VM.

**D: non viene visualizzata alcuna indicazione di manutenzione pianificata nel portale, in PowerShell o nell'interfaccia della riga di comando. Cosa c'è che non va?**

**R:** Le informazioni relative alla manutenzione pianificata sono disponibili durante un ciclo di manutenzione pianificata solo per le macchine virtuali che ne saranno interessate. In altre parole, se non sono visualizzati dati, il ciclo di manutenzione potrebbe essere già stato completato (o non avviato) o la macchina virtuale potrebbe essere già ospitata in un server aggiornato.

**D: È possibile sapere con esattezza quando la macchina virtuale verrà coinvolta?**

**R:** Quando si imposta la pianificazione, viene definita una finestra temporale di alcuni giorni. La sequenza esatta dei server (e delle VM) entro questa finestra è tuttavia sconosciuta. I clienti che vogliono conoscere l'ora esatta per le proprie macchine virtuali possono usare gli [eventi pianificati](/linux/scheduled-events.md), eseguire query dalla macchina virtuale e ricevere una notifica 15 minuti prima del riavvio di una macchina virtuale.

**D: Quanto tempo sarà necessario per riavviare la macchina virtuale?**

**R:** A seconda delle dimensioni della macchina virtuale, il riavvio potrebbe richiedere alcuni minuti durante l'intervallo di manutenzione self-service. Durante i riavvii avviati da Azure nell'intervallo di manutenzione pianificato, il riavvio richiede in genere circa 25 minuti. Si noti che nel caso in cui si usino Servizi cloud (ruolo Web o di lavoro), set di scalabilità di macchine virtuali o set di disponibilità, saranno disponibili 30 minuti tra ogni gruppo di macchine virtuali (domini di aggiornamento) durante l'intervallo di manutenzione pianificato.

**D: Qual è l'esperienza nel caso di set di scalabilità di macchine virtuali?**

**R:** La manutenzione pianificata è ora disponibile per il set di scalabilità di macchine virtuali. Per istruzioni su come avviare la manutenzione Self-Service, fare riferimento alla [manutenzione pianificata per i set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) .

**D: Qual è l'esperienza nel caso di Servizi cloud (ruolo Web o di lavoro) e Service Fabric?**

**R:** Anche se queste piattaforme sono interessate dalla manutenzione pianificata, i clienti che le usano sono considerati sicuri perché solo le VM di un singolo dominio di aggiornamento saranno interessate a una determinata ora. La manutenzione self-service non è attualmente disponibile per Servizi cloud (ruolo Web o di lavoro) e Service Fabric.

**D: non vengono visualizzate informazioni di manutenzione sulle VM. Cosa è successo?**

**R:** I motivi per cui non vengono visualizzate informazioni sulla manutenzione per le VM sono diversi:
1.  Si usa una sottoscrizione contrassegnata come Microsoft (interno).
2.  Le VM non sono pianificate per la manutenzione. La manutenzione potrebbe essere terminata oppure essere stata annullata o modificata e di conseguenza le macchine virtuali non sono più interessate.
3.  La colonna **Manutenzione** non è stata aggiunta alla visualizzazione elenco delle macchine virtuali. Anche se questa colonna è stata aggiunta alla visualizzazione predefinita, i clienti con una configurazione che non prevede la visualizzazione delle colonne non predefinite devono aggiungere manualmente la colonna **Manutenzione** alla visualizzazione elenco delle VM.

**D: la macchina virtuale è pianificata per la manutenzione per la seconda volta. Perché?**

**R:** In diversi casi d'uso la VM risulterà pianificata per la manutenzione dopo avere già completato la ridistribuzione della manutenzione:
1.  Il ciclo di manutenzione è stato annullato e riavviato con un payload diverso. Potrebbe essere stato rilevato un payload con errori e potrebbe essere stato semplicemente necessario distribuire un payload aggiuntivo.
2.  È stato *ripristinato il servizio* della VM in un altro nodo a causa di un errore hardware.
3.  Si è scelto di arrestare (deallocare) e riavviare la VM.
4.  È attivo l'**arresto automatico** per la VM.



## <a name="next-steps"></a>Passaggi successivi

È possibile gestire la manutenzione pianificata usando l'interfaccia della riga di comando di [Azure](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) o il [portale](maintenance-notifications-portal.md).

