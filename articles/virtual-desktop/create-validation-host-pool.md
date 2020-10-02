---
title: Aggiornamenti del pool di host di Desktop virtuale Windows - Azure
description: Come creare un pool di host di convalida per monitorare gli aggiornamenti del servizio prima di implementare gli aggiornamenti in produzione.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b8eb8378480b8784939e3ab63327e73e3705c74a
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526511"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Esercitazione: Creare un pool di host per convalidare gli aggiornamenti dei servizi

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).

I pool di host sono una raccolta di una o più macchine virtuali identiche all'interno dell'ambiente di Desktop virtuale Windows. È consigliabile creare un pool di host di convalida in cui applicare per primi gli aggiornamenti del servizio. In questo modo è possibile monitorare gli aggiornamenti del servizio prima che vengano applicati all'ambiente standard o non di convalida. Senza un pool di host di convalida, si potrebbero non rilevare modifiche che introducono errori, con conseguenti possibili tempi di inattività per gli utenti nell'ambiente standard.

Per garantire il funzionamento delle app con gli aggiornamenti più recenti, il pool di host di convalida deve essere il più simile possibile ai pool di host nell'ambiente non di convalida. Gli utenti dovranno connettersi al pool di host di convalida con la stessa frequenza con cui si connettono al pool di host standard. Se nel pool di host sono attivi i test automatizzati, è necessario includere i test automatizzati anche nel pool di host di convalida.

È possibile eseguire il debug dei problemi nel pool di host di convalida con [la funzionalità di diagnostica](diagnostics-role-service.md) o gli [articoli di risoluzione dei problemi del Desktop virtuale Windows](troubleshoot-set-up-overview.md).

>[!NOTE]
> È consigliabile conservare il pool di host di convalida per testare tutti gli aggiornamenti futuri.

>[!IMPORTANT]
>Desktop virtuale Windows con l'integrazione con Azure Resource Manager presenta attualmente problemi di abilitazione e disabilitazione dell'ambiente di convalida. Questo articolo verrà aggiornato dopo la risoluzione del problema.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, seguire le istruzioni riportate in [Configurare il modulo PowerShell di Desktop virtuale Windows](powershell-module.md) per configurare il modulo PowerShell e accedere ad Azure.

## <a name="create-your-host-pool"></a>Creare il pool di host

È possibile creare un pool di host seguendo le istruzioni di uno dei seguenti articoli:
- [Esercitazione: Creare un pool di host con Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Creare un pool di host con PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definire il pool di host come un pool di host di convalida

Eseguire i seguenti comandi cmdlet di PowerShell per definire il nuovo pool di host come pool di host di convalida. Sostituire i valori tra parentesi con i valori relativi alla sessione:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

Eseguire il seguente comando cmdlet di PowerShell per verificare di aver impostato la proprietà di convalida. Sostituire i valori tra parentesi con i valori relativi alla sessione.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

I risultati del comando cmdlet dovrebbero essere simili a questo output:

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="update-schedule"></a>Aggiornare la pianificazione

Gli aggiornamenti del servizio vengono eseguiti mensilmente. Se sono presenti problemi importanti, gli aggiornamenti critici saranno forniti a cadenza più frequente.

Se sono presenti aggiornamenti del servizio, assicurarsi di avere almeno un piccolo gruppo di utenti che accedono ogni giorno per convalidare l'ambiente. È consigliabile visitare regolarmente il [sito TechCommunity](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) e seguire gli eventuali post con WVDUPdate per rimanere informati sugli aggiornamenti dei servizi.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato un pool di host di convalida, si apprenderà a usare Integrità dei servizi di Azure per monitorare la distribuzione di Desktop virtuale Windows.

> [!div class="nextstepaction"]
> [Configurare gli avvisi dei servizi](./set-up-service-alerts.md)
