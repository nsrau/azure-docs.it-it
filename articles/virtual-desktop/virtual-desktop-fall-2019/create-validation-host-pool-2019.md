---
title: Aggiornamenti del pool di host di Desktop virtuale Windows - Azure
description: Come creare un pool di host di convalida per monitorare gli aggiornamenti del servizio prima di implementare gli aggiornamenti in produzione.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 21c3e3c7ae515f5a740384d202451f61291bc05b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82613847"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Esercitazione: Creare un pool di host per convalidare gli aggiornamenti dei servizi

>[!IMPORTANT]
>Questo contenuto si applica alla versione Autunno 2019 che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si sta tentando di gestire gli oggetti Azure Resource Manager di Desktop virtuale Windows introdotti nell'aggiornamento di Primavera 2020, vedere [questo articolo](../create-validation-host-pool.md).

I pool di host sono una raccolta di una o più macchine virtuali identiche all'interno di ambienti tenant di Desktop virtuale Windows. Prima di distribuire i pool di host nell'ambiente di produzione, si consiglia di creare un pool di host di convalida. Gli aggiornamenti vengono prima applicati nel pool di host di convalida, consentendo all'utente di monitorare gli aggiornamenti del servizio prima di distribuirlo nell'ambiente di produzione. Senza un pool di host di convalida, si potrebbero non notare i cambiamenti che presentano errori, il che può provocare tempi di inattività per gli utenti nell'ambiente di produzione.

Per garantire il funzionamento delle app con gli aggiornamenti più recenti, il pool di host di convalida deve essere il più simile possibile ai pool di host nell'ambiente di produzione. Gli utenti devono connettersi al pool di host di convalida con la stessa frequenza con cui si connettono al pool di host di produzione. Se nel pool di host sono attivi i test automatizzati, è necessario includere i test automatizzati anche nel pool di host di convalida.

È possibile eseguire il debug dei problemi nel pool di host di convalida con [la funzionalità di diagnostica](diagnostics-role-service-2019.md) o gli [articoli di risoluzione dei problemi del Desktop virtuale Windows](troubleshoot-set-up-overview-2019.md).

>[!NOTE]
> È consigliabile conservare il pool di host di convalida per testare tutti gli aggiornamenti futuri.

Prima di iniziare, [scaricare e importare il modulo PowerShell del Desktop virtuale Windows](/powershell/windows-virtual-desktop/overview/), se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Creare il pool di host

È possibile creare un pool di host seguendo le istruzioni di uno dei seguenti articoli:
- [Esercitazione: Creare un pool di host con Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
- [Creare un pool di host con un modello di Azure Resource Manager](create-host-pools-arm-template.md)
- [Creare un pool di host con PowerShell](create-host-pools-powershell-2019.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definire il pool di host come un pool di host di convalida

Eseguire i seguenti comandi cmdlet di PowerShell per definire il nuovo pool di host come pool di host di convalida. Sostituire i valori tra virgolette con i valori relativi alla sessione:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Eseguire il seguente comando cmdlet di PowerShell per verificare di aver impostato la proprietà di convalida. Sostituire i valori tra virgolette con i valori relativi alla sessione.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

I risultati del comando cmdlet dovrebbero essere simili a questo output:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Aggiornare la pianificazione

Gli aggiornamenti del servizio vengono eseguiti mensilmente. Se sono presenti problemi importanti, gli aggiornamenti critici saranno forniti a cadenza più frequente.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato un pool di host di convalida, si apprenderà a usare Integrità dei servizi di Azure per monitorare la distribuzione di Desktop virtuale Windows. 

> [!div class="nextstepaction"]
> [Configurare gli avvisi dei servizi](../set-up-service-alerts.md)
