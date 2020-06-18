---
title: Desktop virtuale Windows con modulo PowerShell - Azure
description: Procedura di installazione e configurazione del modulo PowerShell per Desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd854691203361847ae9a6c873121c9b66820a90
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743380"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Configurare il modulo PowerShell per Desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Primavera 2020 con gli oggetti Azure Resource Manager di Desktop virtuale Windows.
>
> L'aggiornamento di Primavera 2020 di Desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il modulo PowerShell per Desktop virtuale Windows è integrato nel modulo Azure PowerShell. Questo articolo illustra come configurare il modulo PowerShell in modo da poter eseguire i cmdlet per Desktop virtuale Windows.

## <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell

Per iniziare a usare il modulo, installare prima di tutto la [versione più recente di PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core). I cmdlet per Desktop virtuale Windows funzionano attualmente solo con PowerShell Core.

Successivamente, sarà necessario installare il modulo DesktopVirtualization da usare nella sessione di PowerShell.

Eseguire il cmdlet PowerShell seguente in modalità con privilegi elevati per installare il modulo:

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Se il cmdlet non funziona, provare a eseguirlo di nuovo con autorizzazioni elevate.

Eseguire quindi il cmdlet seguente per connettersi ad Azure:

```powershell
Connect-AzAccount
```

Per accedere all'account Azure è necessario un codice generato quando si esegue il cmdlet Connect. Per accedere, passare a <https://microsoft.com/devicelogin>, immettere il codice e quindi accedere usando le credenziali di amministratore di Azure.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Questa operazione consentirà di accedere direttamente alla sottoscrizione predefinita per le credenziali di amministratore.

## <a name="change-the-default-subscription"></a>Modificare la sottoscrizione predefinita

Per modificare la sottoscrizione predefinita dopo aver eseguito l'accesso, eseguire questo cmdlet:

```powershell
Select-AzSubscription -Subscription <preferredsubscriptionname>
```

È anche possibile selezionarla da un elenco usando il cmdlet Out-GridView:
```powershell
Get-AzSubscription | Out-GridView -PassThru | Select-AzSubscription
```

Quando si seleziona una nuova sottoscrizione da usare, non è necessario specificare l'ID della sottoscrizione nei cmdlet eseguiti successivamente. Il cmdlet seguente, ad esempio, recupera l'host di una sessione specifica senza che sia necessario l'ID sottoscrizione:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

È anche possibile modificare le sottoscrizioni in base ai singoli cmdlet aggiungendo il nome della sottoscrizione desiderata come parametro. Il cmdlet successivo è identico a quello dell'esempio precedente, ad eccezione dell'ID sottoscrizione aggiunto come parametro per modificare la sottoscrizione usata dal cmdlet.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Ottenere un percorso

Il parametro relativo al percorso è obbligatorio per tutti i cmdlet **New-AzWVD** che creano nuovi oggetti.

Eseguire il cmdlet seguente per ottenere un elenco dei percorsi supportati dalla sottoscrizione:

```powershell
Get-AzLocation
```

L'output per **Get-AzLocation** sarà simile al seguente:

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

Quando si conosce il percorso dell'account, è possibile usarlo in un cmdlet. Ad esempio, di seguito è riportato un cmdlet che crea un pool di host nel percorso "southeastasia":

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato configurato il modulo di PowerShell, è possibile eseguire i cmdlet per eseguire tutte le operazioni in Desktop virtuale Windows. Di seguito sono riportate alcune delle posizioni in cui è possibile usare il modulo:

- Per configurare il proprio ambiente di Desktop virtuale Windows, è possibile eseguire le [esercitazioni su Desktop virtuale Windows]().
- [Creare un pool di host con PowerShell](create-host-pools-powershell.md)
- [Configurare il metodo di bilanciamento del carico di Desktop virtuale Windows](configure-host-pool-load-balancing.md)
- [Configurare il tipo di assegnazione del pool di host di desktop personale](configure-host-pool-personal-desktop-assignment-type.md)
- E molto altro ancora.

Se si verificano problemi, consultare l'articolo sulla [risoluzione dei problemi con PowerShell](troubleshoot-powershell.md) per assistenza.

