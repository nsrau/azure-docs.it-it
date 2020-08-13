---
title: Aggiornamenti del pool di host di Desktop virtuale Windows - Azure
description: Come creare un pool di host di convalida per monitorare gli aggiornamenti del servizio prima di implementare gli aggiornamenti in produzione.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8eac40ad958a10b8c853304ee2be8b2dc27af1a2
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008713"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Esercitazione: Creare un pool di host per convalidare gli aggiornamenti dei servizi

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).

I pool di host sono una raccolta di una o più macchine virtuali identiche all'interno di ambienti tenant di Desktop virtuale Windows. Prima di distribuire i pool di host nell'ambiente di produzione, si consiglia di creare un pool di host di convalida. Gli aggiornamenti vengono prima applicati nel pool di host di convalida, consentendo all'utente di monitorare gli aggiornamenti del servizio prima di distribuirlo nell'ambiente di produzione. Senza un pool di host di convalida, si potrebbero non notare i cambiamenti che presentano errori, il che può provocare tempi di inattività per gli utenti nell'ambiente di produzione.

Per garantire il funzionamento delle app con gli aggiornamenti più recenti, il pool di host di convalida deve essere il più simile possibile ai pool di host nell'ambiente di produzione. Gli utenti devono connettersi al pool di host di convalida con la stessa frequenza con cui si connettono al pool di host di produzione. Se nel pool di host sono attivi i test automatizzati, è necessario includere i test automatizzati anche nel pool di host di convalida.

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
