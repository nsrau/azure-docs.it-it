---
title: Configurare il metodo di bilanciamento del carico di anteprima di Desktop virtuale Windows - Azure
description: Come configurare il metodo di bilanciamento del carico per un ambiente di Desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0c4702dada17e759d89c33be99b3155f4b15ad9e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328885"
---
# <a name="configure-the-windows-virtual-desktop-preview-load-balancing-method"></a>Configurare il metodo di bilanciamento del carico di anteprima di Desktop virtuale Windows

Configura il metodo di bilanciamento del carico per un pool di host consente di modificare l'ambiente di anteprima di Desktop virtuale Windows per meglio soddisfare le proprie esigenze.

>[!NOTE]
> Ciò non si applica a un pool persistente host desktop perché gli utenti dispongono sempre un mapping 1:1 a un host sessione all'interno del pool di host.

## <a name="configure-breadth-first-load-balancing"></a>Configurare il bilanciamento del carico breadth-first

Bilanciamento del carico breadth-first è la configurazione predefinita per i nuovi pool di host non permanente. Il bilanciamento del carico breadth-first distribuisce nuove sessioni utente in tutti gli host di sessione disponibili nel pool di host. Durante la configurazione di bilanciamento del carico breadth-first, è possibile impostare un limite di durata massima della sessione per ogni host sessione nel pool di host.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto.

Per configurare un pool di host per eseguire breadth-first bilanciamento del carico senza modificare il limite di durata massima della sessione, eseguire il cmdlet di PowerShell seguente:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Per configurare un pool di host per eseguire il bilanciamento del carico breadth-first e usare un limite di durata massima della nuova sessione, eseguire il cmdlet di PowerShell seguente:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurare il bilanciamento del carico di profondità

Profondità il bilanciamento del carico distribuisce le nuove sessioni utente in un host di sessione disponibili con il numero massimo di connessioni, ma non ha raggiunto il limite della soglia di durata massima della sessione. Quando si configura il bilanciamento del carico depth-first, si **necessario** impostare un limite di durata massima della sessione per ogni host di sessione nel pool di host.

Per configurare un pool di host per eseguire il bilanciamento del carico depth-first, eseguire il cmdlet di PowerShell seguente:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
