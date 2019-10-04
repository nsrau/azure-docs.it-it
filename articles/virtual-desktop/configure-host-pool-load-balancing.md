---
title: Configurare il metodo di bilanciamento del carico per desktop virtuali Windows-Azure
description: Come configurare il metodo di bilanciamento del carico per un ambiente desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 3a940dbf592087878cb9dd19f856f1a3d94291c5
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676769"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurare il metodo di bilanciamento del carico di Desktop virtuale Windows

La configurazione del metodo di bilanciamento del carico per un pool host consente di modificare l'ambiente di desktop virtuale Windows in base alle proprie esigenze.

>[!NOTE]
> Questa operazione non si applica a un pool host del desktop persistente perché gli utenti hanno sempre un mapping 1:1 a un host sessione nel pool host.

## <a name="configure-breadth-first-load-balancing"></a>Configurare la larghezza-primo bilanciamento del carico

Il bilanciamento del carico con un primo respiro è la configurazione predefinita per i nuovi pool host non permanenti. Il bilanciamento del carico per la prima volta distribuisce le nuove sessioni utente in tutti gli host di sessione disponibili nel pool di host. Quando si configura il bilanciamento del carico per il primo livello, è possibile impostare un limite massimo di sessioni per ogni host sessione nel pool host.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Per configurare un pool di host per eseguire il bilanciamento del carico con ampiezza del primo senza modificare il limite massimo della sessione, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Per configurare un pool di host per eseguire il bilanciamento del carico con ampiezza e per l'uso di un nuovo limite massimo di sessioni, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurare la profondità-primo bilanciamento del carico

Il bilanciamento del carico depth-first distribuisce le nuove sessioni utente a un host di sessione disponibile con il numero massimo di connessioni, ma non ha raggiunto la soglia massima di limite della sessione. Quando si configura il bilanciamento del carico per il primo livello di profondità, è **necessario** impostare un limite massimo di sessioni per host sessione nel pool host.

Per configurare un pool di host per eseguire il bilanciamento del carico per il primo livello di profondità, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
