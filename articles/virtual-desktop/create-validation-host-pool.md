---
title: Creare un pool di host dell'anteprima di Desktop virtuale Windows per convalidare gli aggiornamenti del servizio - Azure
description: Come creare un pool di host di convalida per monitorare gli aggiornamenti del servizio prima di implementare gli aggiornamenti in produzione.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: helohr
ms.openlocfilehash: 732192e481f88c0cd3c78e9dac43970eb5be72e6
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840527"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Esercitazione: Creare un pool di host per convalidare gli aggiornamenti dei servizi

I pool di host sono una raccolta di una o più macchine virtuali identiche all'interno di ambienti tenant dell'anteprima di Desktop virtuale Windows. Prima di distribuire i pool di host nell'ambiente di produzione, si consiglia di creare un pool di host di convalida. Gli aggiornamenti vengono prima applicati nel pool di host di convalida, consentendo all'utente di monitorare gli aggiornamenti del servizio prima di distribuirlo nell'ambiente di produzione. Senza un pool di host di convalida, si potrebbero non notare i cambiamenti che presentano errori, il che può provocare tempi di inattività per gli utenti nell'ambiente di produzione.

Per garantire il funzionamento delle app con gli aggiornamenti più recenti, il pool di host di convalida deve essere il più simile possibile ai pool di host nell'ambiente di produzione. Gli utenti devono connettersi al pool di host di convalida con la stessa frequenza con cui si connettono al pool di host di produzione. Se nel pool di host sono attivi i test automatizzati, è necessario includere i test automatizzati anche nel pool di host di convalida.

È possibile eseguire il debug dei problemi nel pool di host di convalida con [la funzionalità di diagnostica](diagnostics-role-service.md) o gli [articoli di risoluzione dei problemi del Desktop virtuale Windows](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview).

>[!NOTE]
> È consigliabile conservare il pool di host di convalida per testare tutti gli aggiornamenti futuri.

Prima di iniziare, [scaricare e importare il modulo PowerShell del Desktop virtuale Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview), se non è già stato fatto.

## <a name="create-your-host-pool"></a>Creare il pool di host

È possibile creare un pool di host seguendo le istruzioni di uno dei seguenti articoli:
- [Esercitazione: Creare un pool di host con Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Creare un pool di host con un modello di Azure Resource Manager](create-host-pools-arm-template.md)
- [Creare un pool di host con PowerShell](create-host-pools-powershell.md)

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
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Aggiornare la pianificazione

Nell'anteprima, gli aggiornamenti del servizio si verificano ogni mese circa. Se sono presenti problemi importanti, gli aggiornamenti critici saranno forniti a cadenza più frequente.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato un pool di host di convalida, è possibile imparare a distribuire e connettersi a uno strumento di gestione per la gestione delle risorse di Desktop virtuale Microsoft.

> [!div class="nextstepaction"]
> [Esercitazione per la distribuzione di uno strumento di gestione](./manage-resources-using-ui.md)
