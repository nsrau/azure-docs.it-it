---
title: Configurare il bilanciamento del carico di Windows Virtual Desktop - AzureConfigure Windows Virtual Desktop load-balancing - Azure
description: Come configurare il metodo di bilanciamento del carico per un ambiente Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128301"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurare il metodo di bilanciamento del carico di Desktop virtuale Windows

La configurazione del metodo di bilanciamento del carico per un pool host consente di regolare l'ambiente Windows Virtual Desktop in base alle proprie esigenze.

>[!NOTE]
> Ciò non si applica a un pool di host desktop persistente perché gli utenti dispongono sempre di un mapping 1:1 a un host di sessione all'interno del pool host.

## <a name="configure-breadth-first-load-balancing"></a>Configurare il bilanciamento del carico in base al primo dell'ampiezza

Il bilanciamento del carico breadth-first è la configurazione predefinita per i nuovi pool di host non persistenti. Il bilanciamento del carico breadth-first distribuisce le nuove sessioni utente tra tutti gli host di sessione disponibili nel pool host. Quando si configura il bilanciamento del carico con ampiezza-primo, è possibile impostare un limite massimo di sessione per ogni host di sessione nel pool host.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](/powershell/windows-virtual-desktop/overview/) da usare nella sessione di PowerShell, se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Per configurare un pool host per eseguire il bilanciamento del carico breadth-first senza modificare il limite massimo di sessione, eseguire il seguente cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Per configurare un pool host per eseguire il bilanciamento del carico breadth-first e utilizzare un nuovo limite massimo di sessione, eseguire il seguente cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurare il bilanciamento del carico con profondità

Il bilanciamento del carico con profondità-prima distribuisce le nuove sessioni utente a un host di sessione disponibile con il numero più alto di connessioni, ma non ha raggiunto la soglia massima del limite di sessione. Quando si configura il bilanciamento del carico con profondità, è **necessario** impostare un limite massimo di sessione per ogni host di sessione nel pool host.

Per configurare un pool host per eseguire il bilanciamento del carico depth-first, eseguire il seguente cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
