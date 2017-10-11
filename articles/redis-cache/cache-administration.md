---
title: Come amministrare Cache Redis di Azure | Documentazione Microsoft
description: "Informazioni su come eseguire attività di amministrazione, ad esempio il riavvio e la pianificazione degli aggiornamenti per Cache Redis di Azure"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 07/05/2017
ms.author: sdanie
ms.openlocfilehash: 3352fec59d7dfbfab9b0416992a60f11d0ec2402
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-administer-azure-redis-cache"></a>Come amministrare Cache Redis di Azure
Questo argomento descrive come eseguire attività di amministrazione, ad esempio il [riavvio](#reboot) e la [pianificazione degli aggiornamenti](#schedule-updates) per le istanze di Cache Redis di Azure.

## <a name="reboot"></a>Reboot
Il pannello **Riavvia** consente di riavviare uno o più nodi della cache. La funzionalità di riavvio consente di testare la resilienza dell'applicazione in presenza di un errore di un nodo della cache.

![Reboot](./media/cache-administration/redis-cache-administration-reboot.png)

Selezionare i nodi per il riavvio e fare clic su **Riavvia**.

![Reboot](./media/cache-administration/redis-cache-reboot.png)

Se si dispone di una cache Premium con clustering abilitato, è possibile selezionare le partizioni della cache da riavviare.

![Reboot](./media/cache-administration/redis-cache-reboot-cluster.png)

Per riavviare uno o più nodi della cache, selezionare i nodi desiderati e fare clic su **Reboot**(Riavvia). Se si dispone di una cache Premium con clustering abilitato, selezionare le partizioni desiderate per riavviare il computer e quindi fare clic su **Reboot**(Riavvia). Dopo alcuni minuti, i nodi selezionati si riavviano e vengono ripristinati online pochi minuti dopo.

L'impatto sulle applicazioni client varia a seconda dei nodi che si riavviano.

* **Principale** : quando il nodo principale viene riavviato, Cache Redis di Azure esegue il failover del nodo della replica, che diventa principale. Durante il failover potrebbe verificarsi un breve intervallo in cui le connessioni alla cache potrebbero avere esito negativo.
* **Slave** : il riavvio del nodo slave in genere non comporta alcun impatto sui client della cache.
* **Principale e slave** : al riavvio di entrambi i nodi della cache, tutti i dati della cache vengono persi e le connessioni alla cache hanno esito negativo fino a quando il nodo primario non torna online. Se è stato configurato il [salvataggio permanente dei dati](cache-how-to-premium-persistence.md), il backup più recente viene ripristinato quando la cache ritorna in linea, ma tutte le scritture nella cache che si sono verificate dopo l'ultimo backup andranno perse.
* **Nodi di una cache Premium con clustering abilitato**: quando si riavviano uno o più nodi di una cache Premium con clustering abilitato, il comportamento per i nodi selezionati è analogo a quello che si ottiene quando si riavviano il nodo o i nodi corrispondenti di una cache non cluster.

> [!IMPORTANT]
> Il riavvio ora è disponibile per tutti i piani tariffari.
> 
> 

## <a name="reboot-faq"></a>Domande frequenti sulla funzionalità di riavvio
* [Quale nodo si deve riavviare per testare l'applicazione?](#which-node-should-i-reboot-to-test-my-application)
* [È possibile riavviare la cache per annullare le connessioni al client?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Con il riavvio i dati nella cache andranno persi?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [È possibile riavviare la cache usando PowerShell, l'interfaccia della riga di comando o altri strumenti di gestione?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Con quali piani tariffari è possibile usufruire della funzionalità di riavvio?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Quale nodo si deve riavviare per testare l'applicazione?
Per testare la resilienza dell'applicazione in caso di errore del nodo principale della cache, riavviare il nodo **Principale** . Per testare la resilienza dell'applicazione in caso di errore del nodo secondario, riavviare il nodo **Slave** . Per testare la resilienza dell'applicazione in caso di errore completo della cache, riavviare **Entrambi** i nodi.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>È possibile riavviare la cache per annullare le connessioni al client?
Sì, se si riavvia la cache tutte le connessioni client vengono annullate. Il riavvio può risultare utile nel caso in cui tutte le connessioni client si interrompano a causa di un errore logico o di un bug nell'applicazione client. Ogni piano tariffario presenta diversi [limiti di connessione al client](cache-configure.md#default-redis-server-configuration) per le diverse dimensioni e, una volta raggiunti questi limiti, non vengono accettate altre connessioni al client. Il riavvio della cache consente di annullare tutte le connessioni al client.

> [!IMPORTANT]
> Se si riavvia la cache per cancellare le connessioni client, StackExchange.Redis si riconnette automaticamente solo dopo che il nodo di Redis è tornato in linea. Se il problema sottostante non viene risolto, le connessioni client possono continuare a interrompersi.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Con il riavvio i dati nella cache andranno persi?
Se si riavviano i nodi **master** e **slave**, vengono persi tutti i dati nella cache o nella partizione specifica se si usa una cache Premium con clustering abilitato. Se è stato configurato il [salvataggio permanente dei dati](cache-how-to-premium-persistence.md), il backup più recente viene ripristinato quando la cache ritorna in linea, ma tutte le scritture nella cache che si sono verificate dopo l'esecuzione del backup andranno perse.

Se si riavvia solo uno dei nodi, in genere i dati non vengono persi, ma è comunque possibile. Per esempio, se il nodo principale viene riavviato durante la scrittura della cache, i dati della scrittura andranno persi. Un altro scenario in cui avviene una perdita di dati si verifica se si riavvia un nodo e l'altro nodo diventa contemporaneamente inattivo basso a causa di un errore. Per altre informazioni sulle possibili cause di una perdita di dati, vedere [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)(Cosa è accaduto ai dati in Redis)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>È possibile riavviare la cache usando PowerShell, l'interfaccia della riga di comando o altri strumenti di gestione?
Sì, per istruzioni relative a PowerShell vedere [To reboot a Redis cache](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache)(Per riavviare una Cache Redis).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Con quali piani tariffari è possibile usufruire della funzionalità di riavvio?
Il riavvio è disponibile per tutti i piani tariffari.

## <a name="schedule-updates"></a>Pianificare gli aggiornamenti
Il pannello **Pianifica aggiornamenti** consente di definire un intervallo di manutenzione per la cache di livello Premium. Quando viene specificato l'intervallo di manutenzione, tutti gli aggiornamenti del server Redis vengono eseguiti durante questo intervallo. 

> [!NOTE] 
> Si noti che l'intervallo di manutenzione è applicabile solo agli aggiornamenti del server Redis e non a tutti gli aggiornamenti di Azure o del sistema operativo delle macchine virtuali che ospitano la cache.
> 
> 

![Pianificare gli aggiornamenti](./media/cache-administration/redis-schedule-updates.png)

Per specificare un intervallo di manutenzione, selezionare i giorni desiderati e specificare l'ora di inizio dell'intervallo per ogni giorno, quindi fare clic su **OK**. Si noti che l'orario dell'intervallo di manutenzione è in formato UTC. 

> [!NOTE]
> L'intervallo di manutenzione predefinito per gli aggiornamenti è di cinque ore. Questo valore non è configurabile dal portale di Azure, ma può essere configurato in PowerShell usando il parametro `MaintenanceWindow` del cmdlet [New-AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry) . Per altre informazioni, vedere [È possibile gestire gli aggiornamenti pianificati usando PowerShell, l'interfaccia della riga di comando o altri strumenti di gestione?](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)
> 
> 

## <a name="schedule-updates-faq"></a>Domande frequenti sulla pianificazione degli aggiornamenti
* [Quando vengono eseguiti gli aggiornamenti se non si usa la funzionalità di pianificazione degli aggiornamenti?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Quale tipo di aggiornamenti vengono eseguiti durante l'intervallo di manutenzione pianificato?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [È possibile gestire gli aggiornamenti pianificati usando PowerShell, l'interfaccia della riga di comando o altri strumenti di gestione?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [Con quali piani tariffari è possibile usufruire della funzionalità di pianificazione degli aggiornamenti?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Quando vengono eseguiti gli aggiornamenti se non si usa la funzionalità di pianificazione degli aggiornamenti?
Se non si specifica un intervallo di manutenzione, è possibile eseguire aggiornamenti in qualsiasi momento.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Quale tipo di aggiornamenti vengono eseguiti durante l'intervallo di manutenzione pianificato?
Durante l'intervallo di manutenzione pianificato vengono eseguiti solo gli aggiornamenti del server Redis. L'intervallo di manutenzione non si applica agli aggiornamenti di Azure o del sistema operativo della macchina virtuale.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>È possibile gestire gli aggiornamenti pianificati usando PowerShell, l'interfaccia della riga di comando o altri strumenti di gestione?
Sì, è possibile gestire gli aggiornamenti pianificati con i cmdlet di PowerShell seguenti:

* [Get-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/get-azurermrediscachepatchschedule)
* [New-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/new-azurermrediscachepatchschedule)
* [New-AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry)
* [Remove-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/remove-azurermrediscachepatchschedule)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Con quali piani tariffari è possibile usufruire della funzionalità di pianificazione degli aggiornamenti?
La funzionalità di **pianificazione degli aggiornamenti** è disponibile solo nel piano tariffario Premium.

## <a name="next-steps"></a>Passaggi successivi
* Esplorare altre funzionalità del [piano Premium di Cache Redis di Azure](cache-premium-tier-intro.md) .

