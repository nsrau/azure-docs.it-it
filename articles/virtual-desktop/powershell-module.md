---
title: Modulo di PowerShell desktop virtuale Windows-Azure
description: Come installare e configurare il modulo PowerShell per desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 54501e7e00ba8a28dd7cb421232b9a9587604338
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82653127"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Configurare il modulo PowerShell per desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Spring 2020 con Azure Resource Manager oggetti desktop virtuali di Windows.
>
> L'aggiornamento di Spring 2020 per desktop virtuale di Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile usarla per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il modulo PowerShell per desktop virtuale di Windows è integrato nel modulo Azure PowerShell. Questo articolo illustra come configurare il modulo di PowerShell in modo da poter eseguire i cmdlet per desktop virtuale di Windows.

## <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell

Per iniziare a usare il modulo, installare prima la [versione più recente di PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core). I cmdlet per desktop virtuali Windows funzionano attualmente solo con PowerShell core.

Successivamente, è necessario installare il modulo DesktopVirtualization da usare nella sessione di PowerShell.

Eseguire il cmdlet di PowerShell seguente in modalità con privilegi elevati per installare il modulo:

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Se il cmdlet non funziona, provare a eseguirlo di nuovo con autorizzazioni elevate.

Eseguire quindi il cmdlet seguente per connettersi ad Azure:

```powershell
Connect-AzAccount
```

Per accedere all'account Azure, è necessario un codice generato quando si esegue il cmdlet Connect. Per accedere, passare a <https://microsoft.com/devicelogin>, immettere il codice e quindi accedere con le credenziali di amministratore di Azure.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Questa operazione consentirà di accedere direttamente alla sottoscrizione predefinita per le credenziali di amministratore.

## <a name="change-the-default-subscription"></a>Modificare la sottoscrizione predefinita

Se si vuole modificare la sottoscrizione predefinita dopo aver eseguito l'accesso, eseguire questo cmdlet:

```powershell
Select-AzureSubscription -SubscriptionName <preferredsubscriptionname>
```

Quando si seleziona una nuova sottoscrizione da usare, non è necessario specificare l'ID della sottoscrizione nei cmdlet eseguiti successivamente. Il cmdlet seguente, ad esempio, recupera un host sessione specifico senza che sia necessario l'ID sottoscrizione:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

È anche possibile modificare le sottoscrizioni in base ai singoli cmdlet aggiungendo il nome della sottoscrizione desiderato come parametro. Il cmdlet successivo è identico a quello dell'esempio precedente, ad eccezione dell'ID sottoscrizione aggiunto come parametro per modificare la sottoscrizione utilizzata dal cmdlet.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Ottieni località

Il parametro location è obbligatorio per tutti i cmdlet **New-AzWVD** che creano nuovi oggetti.

Eseguire il cmdlet seguente per ottenere un elenco delle località supportate dalla sottoscrizione:

```powershell
Get-AzLocation
```

L'output di **Get-AzLocation** sarà simile al seguente:

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

Quando si conosce la posizione dell'account, è possibile usarla in un cmdlet. Ad esempio, di seguito è riportato un cmdlet che crea un pool host nel percorso "SouthEastAsia":

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato configurato il modulo di PowerShell, è possibile eseguire i cmdlet per eseguire tutte le operazioni nel desktop virtuale di Windows. Ecco alcune delle posizioni in cui è possibile usare il modulo:

- Eseguire le [esercitazioni sul desktop virtuale di Windows]() per configurare l'ambiente di desktop virtuale di Windows.
- [Creare un pool di host con PowerShell](create-host-pools-powershell.md)
- [Configurare il metodo di bilanciamento del carico di Desktop virtuale Windows](configure-host-pool-load-balancing.md)
- [Configurare il tipo di assegnazione del pool di host personal desktop](configure-host-pool-personal-desktop-assignment-type.md)
- Sono stati apportati numerosi altri miglioramenti!

Se si verificano problemi, vedere l'articolo sulla [risoluzione dei problemi di PowerShell](troubleshoot-powershell.md) per assistenza.

